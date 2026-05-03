---
name: onboard-client
slug: /onboard-client
agent: account-manager
department: fulfillment
output_format: client-onboarding-package
required_profiles:
  client.signed: true
  client.slug: declared
  client.intake_form: submitted
allowed_scopes:
  - single-client
output_path:
  single-client: outputs/{date}/onboarding/{client_slug}/onboarding-package.md
---

# /onboard-client — First-30-Days Client Onboarding

> **Purpose.** Convert a signed contract into an operational client subprofile. Run the foundations chain (voice → ICP → content engine → outbound engine) at client-scope so that by day 30 every client subprofile is ≥70% complete and weekly delivery can run without operator interruption.
>
> **Bar.** A client who completes onboarding has voice-validated content shipping by day 14 and qualified DM ops running by day 21. If either misses, onboarding failed and the engagement starts at a deficit no monthly report recovers.

## When to invoke

- Acquisition FSM transitions `Closed → ClientOnboarding` (auto-trigger)
- Operator manually invokes after a contract signs outside the standard pipeline (rare — direct referrals, partnership conversions)
- Re-onboarding scenario: existing client renews into a re-scoped offer (treat as new onboarding for the new scope)

## Required inputs

1. Signed contract (`client.signed: true`) with offer scope confirmed
2. Client slug declared (`client_slug` — non-PII alias, kebab-case, used in every subprofile path per ENCODING.md)
3. Initial intake form submitted (12-18 questions: company stage, revenue tier, target outcome, content history, prior agency experiences, decision-makers, communication preferences, slack/notion/email channel)
4. Offer deliverable spec from `/design-retainer-offer` output (what we ship, at what cadence, with what revisions allowed)
5. Operator + AM availability for the kickoff sequence (4-6 sessions across first 30 days)

## The onboarding sequence

### Step 1 — Client subprofile initialization (day 0)

Create empty subprofiles under the recursive paths declared in `ENCODING.md`:

```yaml
clients:
  {client_slug}:
    status: onboarding
    onboarding_started: {date}
    offer_tier: {retainer | retainer+ddm | retainer+content+ddm}
    offer_scope: {pulled from /design-retainer-offer output}
    success_metrics: []                # populated Step 8
    voice_drift_threshold: 0.80        # default; tightened or loosened in Step 3
    onboarding_milestones:
      voice_extraction: { due: day_7,  status: pending }
      icp_build:        { due: day_14, status: pending }
      content_engine:   { due: day_21, status: pending }
      outbound_engine:  { due: day_30, status: pending }
brand_voice.client_voices.{client_slug}: { status: initializing, completeness: 0 }
ideal_customer_profile.client_icps.{client_slug}: { status: initializing, completeness: 0 }
content_strategy.client_content.{client_slug}: { status: initializing, completeness: 0 }
case_studies.client_case_studies.{client_slug}: { status: initializing, entries: [] }
```

### Step 2 — Kickoff call (day 0-3, 45 min)

Agenda:
- Mutual intros (operator + AM + client + any client-side stakeholders)
- Deliverable scope confirmation (what's in scope, what's out, revision policy, voice-drift policy, comm cadence)
- Voice-extraction interview booking (within first 7 days — must be operator-led OR AM-led, never async)
- Channel choice (slack vs. notion vs. email — single channel, not all three)
- Revision-policy walkthrough (typically 2 revisions per post; beyond that triggers brief refresh)
- Q&A on the workspace's 24-hour SLA expectations (client review window before nudge)

**Output:** kickoff call notes + voice-extraction interview slot + channel-of-record + escalation contact.

### Step 3 — Voice-extraction interview (day 4-7)

Run `/extract-founder-voice` with `scope=client-voice client_slug={slug}`. The skill itself drives the 9-question interview (90 minutes). At end of session:

- `brand_voice.client_voices.{client_slug}.completeness` should be ≥75%
- Story bank seeded with 8-12 stories the ghostwriter draws from
- Voice-drift threshold calibrated per client preference (some founders want tight 0.85, some accept 0.75)
- 5 banned-vocabulary additions specific to this client (in addition to workspace-level `spec/BANNED-VOCABULARY.md`)

**Gate:** if completeness <70% after the session, second interview booked within 5 days. Per N-1, no client ghostwriting starts until voice profile passes threshold.

### Step 4 — ICP build (day 8-14)

Run `/build-icp` with `scope=client-icp client_slug={slug}`. The skill drives the 12-question interview (60 minutes). The ICP for the client is THEIR ICP (the people the client wants to attract on LinkedIn), not the agency's ICP.

**Gate:** `ideal_customer_profile.client_icps.{client_slug}.completeness` ≥75% before content engine build runs.

### Step 5 — Content engine build (day 15-21)

Run `/build-content-engine` with `scope=client-content client_slug={slug}`. Pillar mix calibrated to the client's ICP solution-awareness (per playbook). Hook library seeded with ≥30 client-voice-fit hooks via `/build-hook-library`.

By end of week 3:
- `content_strategy.client_content.{client_slug}.completeness` ≥70%
- Posting calendar via `/build-posting-calendar` for week 4 onward
- First 3-5 ghostwritten posts in the approval queue (via `/ghostwrite-client-post`) with voice-drift checks ≥ threshold

**First content ship target: day 14-17.** A client who waits 3+ weeks for the first post questions the engagement.

### Step 6 — Outbound engine kickoff (day 22-30, if in scope)

If the client opted into DM ops:
- Run `/source-lead-list` with `scope=client-leads client_slug={slug}` for first batch (200-500 contacts)
- Run `/build-outbound-engine` with client scope (DM intent taxonomy + per-class sequence specific to client's voice + ICP)
- VA dispatched daily via `/run-client-dm-ops` starting day 24-26
- First qualified replies expected day 27-32

If outbound not in scope, this step skipped.

### Step 7 — Expectation-setting doc (day 0-7, written in parallel with Step 2)

A single-page client-facing doc that codifies:
- What ships when (cadence per deliverable)
- Approval workflow (where posts land, how revisions are requested, SLA)
- Voice-drift policy (weekly audit, what triggers a re-extraction)
- Comm cadence (weekly summary every Monday, monthly report 1st of month)
- Escalation path (AM → operator → exit clause)

Client signs off in the kickoff or within 5 days. This doc becomes the reference when scope-creep questions surface in month 3.

### Step 8 — Success-metric definition (day 0-14)

Per offer scope, define the 3-5 metrics this engagement is judged by. Examples (per offer type):

| Offer Type | Metric 1 | Metric 2 | Metric 3 |
|---|---|---|---|
| Content-only | Impressions / month (target band) | Profile-clicks / month | Inbound DMs from content / month |
| Content + DM | Above + qualified DMs / month | Calls booked / month | Closed pipeline value |
| Full-stack (content + DM + ops) | Above + offer-bound applications / month | Cohort/retainer signed / quarter | Attribution-traced revenue |

Metrics persist to `clients.{slug}.success_metrics`. Monthly report (`/build-client-report`) reads from this list — never invents new ones mid-engagement.

### Step 9 — 30-day milestone audit (day 30)

AM runs the audit:
- All 6 client subprofiles ≥70% (per recursive paths in ENCODING.md)
- Onboarding milestones marked `complete` (voice + ICP + content + outbound)
- ≥5 ghostwritten posts shipped, voice-drift ≥ threshold on all
- DM ops running daily (if in scope) with ≥1 qualified reply
- Client has reviewed and approved at least 80% of posts in queue

If any check fails: surface to operator + AM, agree remediation in writing, do not proceed to month-2 fulfillment without remediation plan.

## Output format

```markdown
# Client Onboarding Package — {Client Slug}

**Onboarding window:** {start_date} → {start_date + 30 days}
**Offer tier:** {tier}
**AM:** {am_slug}

## Onboarding milestones

| Milestone | Due | Status | Owner |
|---|---|---|---|
| Voice extraction | day_7 | pending → complete | operator + AM |
| ICP build | day_14 | pending → complete | AM |
| Content engine | day_21 | pending → complete | AM + content-strategist |
| Outbound engine | day_30 | pending → complete | AM + dm-strategist |

## Channel of record

{slack | notion | email | combo} — single source of truth for approvals + comms.

## Approval workflow

[reference to expectation-setting doc]

## Success metrics ({reporting period})

[populated Step 8]

## Escalation path

AM → Operator → Exit clause invocation per contract section X.
```

## Verification checklist

1. `clients.{slug}.status == onboarding` set day 0
2. All 4 client subprofiles initialized (voice, ICP, content, case_studies)
3. Kickoff call held within first 3 days
4. Voice-extraction interview held within first 7 days, completeness ≥70%
5. ICP build complete by day 14, completeness ≥75%
6. Content engine + first posts shipped by day 17
7. DM ops running by day 26 (if in scope)
8. Expectation-setting doc signed by client
9. Success metrics defined and persisted
10. Day-30 audit clean OR remediation plan in writing

## Common failure modes

| Failure | Likely cause | Fix |
|---|---|---|
| Voice extraction <70% after 1 session | Client gave thin answers; operator rushed | Re-book within 5 days; AM joins for the second pass |
| ICP build delayed past day 14 | Client unavailable for interview | Switch to async-with-followup format; surface to operator if 14+ days slip |
| First post not shipped by day 17 | Approval workflow not set; client bottleneck | Trigger approval-workflow recheck via `/post-approval-tracker`; lower revision rounds for first 5 posts |
| Voice drift on first 5 posts | Ghostwriter using template defaults | Halt ghostwriting; recalibrate per `INVARIANTS.md` N-2; re-run voice-extraction Step 3 of `/extract-founder-voice` |
| DM ops not producing replies by day 32 | Lead list ICP-misaligned OR M1 weak | Audit lead list (drop to ≥85 ICP-fit); rewrite M1 with fresh personalization |
| Client disengaged from approval workflow by day 25 | Cadence too high OR channel mismatch | Reduce post volume by 30% temp; revisit channel choice |
| Day-30 audit reveals 2+ subprofiles <70% | Onboarding sequence cut corners | Halt new fulfillment work; complete missing subprofiles before resuming |

## Cross-skill routing

- **Upstream:** `acquisition-head` `Closed` state → triggers `/onboard-client`
- **Direct downstream chain:** `/extract-founder-voice` → `/build-icp` → `/build-content-engine` → `/build-outbound-engine` (all client-scoped)
- **Continuous downstream:** `/voice-drift-detector` weekly, `/post-approval-tracker` weekly, `/run-client-dm-ops` daily, `/client-content-sourcing-pulse` weekly
- **Monthly:** `/build-client-report` (the artifact this onboarding sets up to be defensible)
- **If Day-30 audit fails:** escalate to `agents/fulfillment-head.md` for remediation plan

## Cross-references

- `agents/account-manager.md` (Cycle 2 — TBD) — owning persona
- `agents/fulfillment-head.md` — escalation owner
- `INVARIANTS.md` N-1 (no client ghostwriting without voice profile), N-2 (mandatory voice-drift check), A-6 (operator/AM review for client-bound)
- `ENCODING.md` — recursive subprofile schema
- `spec/CONTEXT-THRESHOLDS.md` — onboarding gate thresholds
- `skills/extract-founder-voice/SKILL.md`, `skills/build-icp/SKILL.md`, `skills/build-content-engine/SKILL.md`, `skills/build-outbound-engine/SKILL.md`
- `skills/voice-drift-detector/SKILL.md`, `skills/post-approval-tracker/SKILL.md`, `skills/build-client-report/SKILL.md`
- `workflows/divisions/acquisition.md` — `Closed → ClientOnboarding` transition
- `workflows/divisions/fulfillment.md` (Cycle 2 — TBD) — full delivery FSM

---

*Skill version 1.0.0 — 2026-05-03*
