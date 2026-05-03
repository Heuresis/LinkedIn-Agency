---
name: post-approval-tracker
slug: /post-approval-tracker
agent: account-manager
department: fulfillment
output_format: post-approval-status
required_profiles:
  client.slug: declared
  client.approval_workflow: defined
allowed_scopes:
  - single-client
  - batch
output_path:
  single-client: outputs/{date}/approval-status/{client_slug}-{week}.md
  batch: outputs/{date}/approval-status/_all-clients-{week}.md
---

# /post-approval-tracker — Client Post Approval Workflow Tracker

> **Purpose.** Track every post for every active client through the approval lifecycle (drafted → in-review → revision-requested → approved → scheduled → published → measured). Surface stuck items, enforce the SLA, prevent posts dying in the queue. Output is the AM's Friday digest + the operator's "what's blocked" view.
>
> **Bar.** A post that sits stuck for >7 days is a fulfillment failure. Either the client is disengaging (retention risk) or the workspace's review channel is broken. The tracker catches both before the monthly report does.

## When to invoke

- **Cron — every Friday 14:00 (weekly per active client).** Per `paperclip.manifest.yaml`.
- After any batch of posts ships into a client's approval queue (>5 posts at once)
- Manually when client communication channel went silent for >48 hours
- Before `/build-client-report` (monthly) — approval health is a report section

## Required inputs

1. `client_slug` declared
2. Client's approval workflow config (from `/onboard-client` Step 7): who reviews, response SLA (default 24h), max revision rounds (default 2), approval mechanism (slack message reaction / notion checkbox / email reply / direct workspace integration)
3. Active post inventory — every post in any state from drafted through scheduled (`outputs/posts/clients/{slug}/`)
4. Per-post timestamps for state transitions (drafted_at, sent_for_review_at, revision_requested_at, approved_at, scheduled_at, published_at)

## The tracking sequence

### Step 1 — Active post inventory

Pull every post for the client where `status ∈ [drafted, in-review, revision-requested, approved, scheduled]`. Posts in `published` or `measured` exit the tracker (they're live or archived).

```yaml
active_posts:
  - post_id: {slug}
    status: drafted | in-review | revision-requested | approved | scheduled
    drafted_at: {timestamp}
    sent_for_review_at: {timestamp | null}
    revision_requested_at: {timestamp | null}
    approved_at: {timestamp | null}
    scheduled_at: {timestamp | null}
    revision_count: {0-N}
    pillar: {authority | proof | personality | offer-bridge | pier}
    voice_drift_score_at_draft: {0-1}
```

### Step 2 — SLA check per post

Apply the workflow's SLA per state:

| State | SLA | Action if breached |
|---|---|---|
| drafted (waiting for AM to send for review) | 12h | AM nudge; if >24h, escalate |
| in-review (waiting for client) | 24h | Client nudge via approval channel; if >48h, AM escalates; if >72h, operator escalates |
| revision-requested (waiting for ghostwriter rewrite) | 24h | Ghostwriter nudge; if >48h, AM escalates |
| approved (waiting for scheduling) | 12h | AM nudge; if >24h, scheduler-issue check |
| scheduled (waiting for publish window) | per scheduled time | If publish window passed without publish, scheduler failure — rerun |

### Step 3 — Approval rate calculation

Rolling 30-day approval rate:

```
approval_rate = approved_count / (approved_count + revised_count + rejected_count + stuck_count)
```

| Rate band | Interpretation | Action |
|---|---|---|
| ≥90% | Healthy | None |
| 80-89% | Acceptable | Monitor; check revision pattern |
| 70-79% | Soft fail | Audit voice profile + ICP framing; check pillar mix vs. client preference |
| <70% | Hard fail | Halt content production; escalate to fulfillment-head; rerun /voice-drift-detector + /build-content-engine for client-content |

### Step 4 — Revision pattern analysis

For posts that get revised, tag the revision reason (provided by client OR inferred from edit pattern):

| Revision reason | Pattern signal | Likely upstream issue |
|---|---|---|
| Voice doesn't sound right | Client edits sentence structure / phrasing | Voice profile drift (run /voice-drift-detector) |
| Wrong angle for ICP | Client edits hook + framing | ICP misalignment (audit client_icp completeness) |
| Specific factual error | Client edits a stat or claim | Sourcing gap (run /client-content-sourcing-pulse) |
| Wrong CTA | Client edits the call-to-action | Offer mismatch (re-confirm offer scope per client_content) |
| Pillar feels off | Client suggests reframing entire post | Calendar/pillar mix needs adjustment |

Patterns surface in the weekly digest. Repeat patterns over 2+ weeks = root cause to address upstream.

### Step 5 — Stuck-post escalation

A post is "stuck" if it sits in any non-terminal state >7 days. Stuck posts get one of three dispositions:

1. **Republish:** if stuck due to scheduling/AM lag, force-push through
2. **Retire:** if stuck due to no client response + post is now stale (e.g., references current event past relevance), archive without publish
3. **Escalate:** if stuck due to client disengagement, AM raises with operator within 48h — could be retention risk

Track `stuck_post_count` and `stuck_post_age_days_p50/p95` as fulfillment health metrics.

### Step 6 — Per-client weekly digest

The Friday brief, sent to AM (and operator if any escalation):

```markdown
# Approval Status — {Client Slug} — Week of {date}

**Approval rate (30d):** {0-100%}
**Posts in flight:** {N drafted | N in-review | N revision-requested | N approved | N scheduled}
**Stuck posts:** {N}
**SLA breaches this week:** {N}

## Posts published this week

| Post | Pillar | Approval cycle (h) | Revisions | Engagement (24h) |
|---|---|---|---|---|
| {slug} | {pillar} | {hours} | {0-N} | {impressions / engagement count} |

## Stuck posts (action required)

| Post | Status | Days stuck | Recommended action |
|---|---|---|---|
| {slug} | {state} | {days} | republish / retire / escalate |

## Revision pattern this week

{summary of any pattern from Step 4 — e.g., "3 posts revised for voice fit, all in Authority pillar"}

## Recommended actions for next week

1. {action 1}
2. {action 2}
```

### Step 7 — Cross-client batch view (operator-facing)

The `batch` scope produces an all-clients dashboard:

```markdown
# Approval Tracker — All Clients — Week of {date}

| Client | Approval rate | Posts in flight | Stuck | SLA breaches | Health |
|---|---|---|---|---|---|
| {slug-1} | {%} | {N} | {N} | {N} | green / yellow / red |

## Clients in red

[surface for operator]
```

### Step 8 — Output emission + integration

Outputs:
- Per-client weekly digest: `outputs/{date}/approval-status/{slug}-{week}.md`
- Cross-client batch dashboard: `outputs/{date}/approval-status/_all-clients-{week}.md`
- Stuck-post escalation queue: surfaces in AM's daily checklist; operator-facing if 2+ weeks unresolved
- Approval-rate trend feeds `/build-client-report` monthly

## Verification checklist

1. All active posts pulled (none missed — cross-check against `/ghostwrite-client-post` output log)
2. SLA applied per workflow config (not workspace default — clients with custom SLAs respected)
3. Approval rate computed correctly (denominator includes ALL terminal states, not just approved)
4. Stuck posts surfaced with disposition recommendation
5. Revision pattern analysis includes root-cause hypothesis where pattern repeats
6. Weekly digest delivered by Friday 16:00 local
7. Operator-facing batch dashboard updated
8. Trends fed to monthly client report

## Common failure modes

| Failure | Likely cause | Fix |
|---|---|---|
| Approval rate calc wrong (e.g., always 100%) | Revised + rejected counts not pulled | Audit data source; verify state machine logs all transitions |
| Stuck-post count grows weekly | Client disengaging OR channel broken | Audit channel of record; consider channel switch; surface to operator if pattern |
| Posts disappear (in workspace but not in tracker) | Post created via direct write, never sent through `/ghostwrite-client-post` | Workflow bypass — surface as audit gap |
| SLA breach noise (lots of false positives) | Client + AM in different timezones | Calibrate SLA against client's timezone, not AM's |
| Revision pattern says "voice fit" but `/voice-drift-detector` says PASS | Subtle drift below cosine threshold but client-perceived | Tighten drift threshold for this client by 0.02; add to next voice-extraction prompt list |
| All clients show approval rate <80% | Workspace-level voice profile or content engine issue | Run `/pipeline-pulse` Layer 3 audit |

## Cross-skill routing

- **Upstream:** `/onboard-client` Step 7 sets the workflow config; `/ghostwrite-client-post` produces posts that enter this tracker
- **Sibling:** `/voice-drift-detector` informs revision-pattern root-cause; `/client-content-sourcing-pulse` informs sourcing-gap revisions
- **Downstream:** `/build-client-report` monthly approval-rate section; `/pipeline-pulse` Layer 3 audit
- **On stuck-post escalation:** `agents/fulfillment-head.md`; if retention risk → `agents/agency-director.md`

## Cross-references

- `agents/account-manager.md` (Cycle 2 — TBD) — runs the tracker
- `agents/fulfillment-head.md` — escalation owner
- `INVARIANTS.md` A-6 (operator/AM review for client-bound), A-9 (every action leaves a receipt)
- `skills/onboard-client/SKILL.md` — workflow config source
- `skills/ghostwrite-client-post/SKILL.md` — post source
- `skills/voice-drift-detector/SKILL.md` — informs revision-pattern root-cause
- `skills/build-client-report/SKILL.md` — consumes monthly trend
- `workflows/divisions/fulfillment.md` (Cycle 2 — TBD) — full delivery FSM
- `paperclip.manifest.yaml` — Friday 14:00 cron

---

*Skill version 1.0.0 — 2026-05-03*
