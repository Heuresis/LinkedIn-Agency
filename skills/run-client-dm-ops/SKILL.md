---
name: run-client-dm-ops
slug: /run-client-dm-ops
agent: dm-strategist
department: fulfillment
output_format: daily-dm-ops-cycle
required_profiles:
  client.outbound_engine_spec: exists
  client.lead_list: exists
allowed_scopes:
  - single-client
output_path:
  single-client: outputs/{date}/dm-ops-cycles/{client_slug}-{date}.md
---

# /run-client-dm-ops — Daily Client DM Operations Cycle

> **Purpose.** Run the daily VA-driven DM operations cycle for a single client. Produces today's send batch, triages yesterday's replies, advances qualifying threads, routes applications, surfaces hard-DQs to operator, captures end-of-day metrics. Designed to run on workspace + VA without operator interruption — except where INVARIANTS demand operator-in-loop.
>
> **Bar.** A VA executing this cycle daily produces the same DM ops output a senior operator would produce manually — minus the senior operator's bottleneck. If the VA needs operator input on >5% of decisions, the workspace's encoding is leaking and needs `/build-outbound-engine` refresh.

## When to invoke

- **Cron — every weekday 06:00 (per active client with DM ops in scope).** Per `paperclip.manifest.yaml`.
- Manually when daily VA cycle is interrupted (sick day, holiday) — operator can run a make-up cycle
- Backfill mode: catch-up batch when 2+ days missed (capped at 3 days backfill to avoid spam-pattern flag)

## Required inputs

1. `client_slug` declared
2. Client's outbound engine spec (per `/build-outbound-engine` client-scoped) — DM intent taxonomy + per-class sequences + qualification thread + booking gate
3. Client's lead list batch for today (from `/source-lead-list` — typically 25-50 contacts/day, sliced from a 200-500 weekly batch)
4. Client's voice profile (`brand_voice.client_voices.{slug}`) — for personalization slot generation
5. Client's ICP profile (`ideal_customer_profile.client_icps.{slug}`) — for intent reasoning
6. Yesterday's DM thread state (every active thread + new replies)
7. Client's calendar availability (for application-gate routing per N-12)

## The daily ops sequence

### Step 1 — Today's batch load

Pull today's slice from this week's lead list. Verify:
- Each contact has not been touched in last 180 days (anti-revisit per /source-lead-list)
- Each contact's ICP-fit score ≥70 (per /source-lead-list scoring)
- Each contact's intent score per `/score-lead-intent` (cold / warm / hot tier)

Output: today's contact set with class + tier per contact.

### Step 2 — M1 generation per contact

For each contact, generate M1 message per the matching intent class + tier:

| Tier | Class default | M1 source |
|---|---|---|
| Cold (intent score 0-40) | Class C — Cold sequence | Pulls cold M1 template from outbound engine spec; fills personalization slots from contact's recent profile activity |
| Warm (intent score 41-70) | Class W — Warm sequence | Pulls warm M1 template; references the warm signal (mutual connection, prior engagement, role fit) |
| Hot (intent score 71-100) | Class L or operator-personal | Pulls liked-post M1 if signal is post engagement; routes to operator-personal if signal is high-value (e.g., recent funding round at fit-vertical) |

Banned-vocab sweep on every M1 (per `spec/BANNED-VOCABULARY.md` + client-specific `phrases_to_avoid`).

### Step 3 — VA dispatch list

Format the day's send batch for the VA:

```markdown
# DM Send List — {Client Slug} — {date}

| Contact | Class | Tier | Channel | M1 draft | Send window |
|---|---|---|---|---|---|
| {slug-1} | C | cold | LinkedIn DM | {message} | 09-11 local |
| {slug-2} | W | warm | LinkedIn DM | {message} | 14-16 local |
| ...

Daily target: {N} sends. Pace evenly across send windows to avoid LinkedIn rate limit flagging.
```

VA's job: paste each M1 manually into LinkedIn (no automation that violates LinkedIn TOS) within the send window.

### Step 4 — Yesterday's reply triage

Pull every reply received in last 24h. Per reply, run `/classify-dm-intent`:
- New replies → Replied state per FSM
- Continuing thread → state-transition per intent classification
- Hard-DQ signals → OperatorReview queue (per A-13)

Output: reply triage list with state transitions + recommended next actions.

### Step 5 — Qualifying thread continuation

For every thread in `Qualifying` state per acquisition FSM:
- Pull the qualification thread spec from client's outbound engine
- Generate next message based on the prospect's last reply + the qualification axis being explored
- Banned-vocab sweep
- VA sends within 2-4 hours of prospect's reply (response speed matters in qualifying)

If qualification reveals soft-fit or high-fit: route to ApplicationGate per Step 6.
If qualification reveals hard-DQ: surface to OperatorReview.

### Step 6 — Application-gate routing (N-12 enforcement)

For prospects who passed qualification:
- Generate the application-gate message (NOT a calendar link directly per N-12)
- Message includes the application URL with personalized framing
- Tracks application submission via webhook (`lead.applied` triggers `/triage-application`)

If prospect requests calendar without applying: VA replies with the application requirement (operator override allowed but rare and logged).

### Step 7 — Hard-DQ surfacing (A-13 enforcement)

Any hard-DQ trip in qualifying threads → OperatorReview queue. Never auto-archive. Operator decides:
- Confirm DQ → archive with reason
- Override DQ → resume thread (rare; logged as A-13 override)

Daily hard-DQ count surfaces in end-of-day metrics.

### Step 8 — No-show recovery integration

For every no-show from prior 48h (`workflows/divisions/acquisition.md` NoShow state):
- Trigger `/no-show-recovery` for the prospect
- VA executes the recovery sequence per the skill's output
- After 2 no-shows: archive per FSM (no operator intervention needed unless prospect was high-value)

### Step 9 — End-of-day metrics emission

```yaml
date: {date}
client: {slug}
metrics:
  dms_sent_today: N
  dms_sent_by_class: { C: N, W: N, L: N, M: N }
  replies_received_24h: N
  reply_rate_24h: %
  qualifying_threads_advanced: N
  applications_submitted: N
  applications_triaged: { Booked: N, AppDQ: N, AppOperatorReview: N }
  hard_dqs_surfaced: N
  no_shows_recovered: N (rebooked) | archived
  operator_interrupts_required: N (target <5% of decisions)
```

Metrics flow to weekly digest in `/build-client-report` + monthly `/build-attribution-trace`.

## Verification checklist

1. Lead list ICP-fit pre-filtered (≥70 only)
2. Anti-revisit applied (no contact touched in <180d)
3. M1 personalization slots filled — no raw template sends
4. Banned-vocab sweep zero hits per outbound message
5. Send batch paced to avoid rate-limit flagging
6. Reply triage runs `/classify-dm-intent` on every new reply
7. Qualifying threads advance within SLA (<4h response when prospect replies)
8. **N-12 enforced:** no calendar link sent without application submission
9. **A-13 enforced:** hard-DQs surface to OperatorReview, never auto-archive
10. End-of-day metrics emitted by 18:00 local
11. Operator-interrupt rate <5% of total decisions (above = encoding gap to fix in /build-outbound-engine)

## Common failure modes

| Failure | Likely cause | Fix |
|---|---|---|
| Reply rate <8% on Cold class for 7+ days | M1 personalization weak OR ICP misalignment | Audit lead list ICP-fit + M1 personalization slot quality |
| Qualifying thread conversion to ApplicationGate <40% | Qualification thread too aggressive OR offer mismatch | Re-run /build-qualification-thread; soften early questions |
| Application submission rate <30% (of qualified) | Application form too long OR friction at gate | Audit form via /build-application; reduce questions |
| Hard-DQ frequency >15% of qualifying threads | DQ criteria too tight OR ICP needs refining | Re-run /build-icp Step 2 (DQ criteria); calibrate |
| Operator-interrupt rate >10% | Encoding leak — VA hitting decisions the workspace should handle | Audit which decision types need encoding; refresh /build-outbound-engine |
| LinkedIn account warning / DM rate-limit flag | Send pace too aggressive OR send pattern too uniform | Drop daily target by 30%; vary send windows; verify VA isn't using automation |
| No-show recovery rebook rate <40% | Recovery sequence weak OR no-show due to disinterest not conflict | Audit recovery sequence per /no-show-recovery; consider archiving sooner |

## Cross-skill routing

- **Upstream:** `/source-lead-list` (today's batch), `/score-lead-intent` (tier per contact), `/build-outbound-engine` (engine spec), `/onboard-client` (client subprofile)
- **Sibling skills called daily:** `/classify-dm-intent`, `/build-qualification-thread` (consumed not invoked), `/no-show-recovery`
- **Downstream:** `/triage-application` (when applications submitted), `/build-call-prep` (when calls booked), `/build-client-report` (monthly metrics)
- **Operator-escalation:** `agents/acquisition-head.md` for hard-DQ override; `agents/agency-director.md` if interrupt rate >10% sustained

## Cross-references

- `agents/dm-strategist.md` (Cycle 2 — TBD) — owning persona
- `agents/fulfillment-head.md`, `agents/acquisition-head.md`
- `INVARIANTS.md` N-5 (intent classification mandatory), N-12 (application before calendar), A-13 (operator on hard-DQ)
- `ENCODING.md` — recursive client subprofile schema
- `spec/CONTEXT-THRESHOLDS.md`
- `reference/frameworks/dm/intent-taxonomy.md`
- `skills/source-lead-list/SKILL.md`, `skills/score-lead-intent/SKILL.md`, `skills/build-outbound-engine/SKILL.md`
- `skills/classify-dm-intent/SKILL.md`, `skills/build-qualification-thread/SKILL.md`, `skills/no-show-recovery/SKILL.md`
- `skills/build-application/SKILL.md`, `skills/triage-application/SKILL.md`
- `workflows/divisions/acquisition.md` — full FSM
- `paperclip.manifest.yaml` — daily 06:00 cron

---

*Skill version 1.0.0 — 2026-05-03*
