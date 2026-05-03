# Account Management Protocol

> The AM's per-client weekly + monthly cadence — onboarding (first 30 days), weekly drift audit, daily approval queue, weekly sourcing pulse, monthly client report — and the escalation path that keeps the operator out of operational details until tier-3 escalation.

## Why this framework exists

Most agencies fail at fulfillment in one of two ways. They run with a project-manager-style coordinator who has no decision authority, which means every client question routes to the operator and the operator's calendar collapses. Or they run with no AM at all, which means the operator IS the AM, and the agency cannot scale past 4-5 clients.

The right pattern is an Account Manager who owns end-to-end client experience with decision authority within a defined scope. The AM is the channel-of-record for the client, runs a defined weekly + monthly cadence, escalates on a tier system with SLAs, and owns the milestone audits that prevent slow-fail engagements.

This framework codifies the AM's cadence so a new AM hire can be operational within 2 weeks. It also codifies the escalation tiering so the operator gets pulled into client-side decisions only when the AM's authority is genuinely insufficient — not because the AM is conflict-averse.

## The 8 rules

### Rule 1 — AM owns end-to-end client experience (decision authority within scope)

The AM is not a project manager who routes everything to the operator. The AM has decision authority within a defined scope:
- Approval workflow logistics (when posts move, who reviews, how revisions are tracked)
- Scope-question adjudication when the question is inside the contract (no escalation needed; AM cites the contract and decides)
- Cadence adjustments within the contract (e.g., shifting weekly call from Tuesday to Wednesday)
- Tone-and-voice operational decisions on the ghostwriter's behalf when the situation is covered by the voice profile

Outside the AM's scope (escalation required):
- Pricing changes
- Contract renegotiation
- Scope expansion or contraction beyond the contract
- Refund requests
- Client complaints about the operator personally
- Anything triggering the contract's exit clause

The AM-vs-PM distinction is the single biggest factor in whether the agency scales past 5 clients per AM.

### Rule 2 — Weekly cadence (Monday + Wednesday + Friday + ongoing daily)

The AM's standard week per client:

**Monday — Voice drift audit + week plan**
- Run `/voice-drift-detector` per active client (cron-triggered 09:00; AM reviews + actions by 11:00)
- Pull last week's metrics; surface anomalies
- Send weekly client summary (channel-of-record): "What shipped last week, what's planned this week, anything from you"
- Per `INVARIANTS.md` A-12 — the weekly drift audit is mandatory

**Wednesday — Sourcing pulse**
- Run `/client-content-sourcing-pulse` per active client
- Review what stories, observations, and operator-side material is flowing into the content engine
- If the pulse is dry (≤2 fresh inputs in 7 days), trigger a sourcing call with the client to refill

**Friday — Approval digest + week wrap**
- Pull approval-queue status across all active clients via `/post-approval-tracker`
- Send Friday digest to client: "Here's what's queued for next week + decisions waiting on you"
- Internal: hand off any drift-flagged items to ghostwriter for recalibration over weekend

**Ongoing daily — Approval queue management**
- Posts move through queue in <24-hour SLA from ghostwriter → AM review → client review → publish
- AM does first review (catches drift, format, rubric scoring) before client sees the post
- Per `INVARIANTS.md` A-6 — never auto-publish; AM-or-operator review required on every client-bound post

### Rule 3 — Monthly cadence (1st of month + monthly call + last-day capture)

**1st of month — Client report**
- Run `/build-client-report` per active client; report ships by EOD on the 1st
- Report includes: metrics vs. success-metric targets (per `/onboard-client` Step 8), drift trend chart (per `voice-drift-detection.md` Rule 7), top-performing post + 1 lesson, what's next month's focus
- Per `INVARIANTS.md` N-3 — never invent results; report is generated from logged data only

**Mid-month — Monthly strategy call**
- 30-45 minute call between AM + client (operator joins for tier-2 clients or on request)
- Agenda: report walkthrough, what's working / what's not, scope-creep adjudication, next 30-day focus
- Notes saved to `outputs/{date}/strategy-calls/{client_slug}-{month}.md`

**Last day of month — Case-study capture**
- Per active client, AM checks: did this client hit a milestone worth case-study capture this month?
- If yes, run `/capture-case-study` immediately while the data is fresh and the client is enthusiastic
- Case-study density (per `transformation-hypothesis.md` Rule 3) compounds via this monthly capture rhythm

### Rule 4 — AM-or-operator review on client-bound posts (per A-6)

Per `INVARIANTS.md` A-6, no client-bound artifact ships without AM or operator review. The AM is the default reviewer; the operator reviews only on:
- Tier-2 / enterprise clients (operator review on every post by contract)
- Posts flagged by AM for operator judgment (e.g., legally sensitive content, public-controversy adjacent topics)
- Posts auto-flagged by `/voice-drift-detector` (HARD_DRIFT halts queue per `voice-drift-detection.md` Rule 6)

The review is not a rubber-stamp. The AM applies:
- Voice-drift threshold check (per `voice-drift-detection.md` Rule 4)
- Hook rubric (≥4 of 5 axes per `/build-hook-library` rubric)
- Banned vocabulary scan (per `spec/BANNED-VOCABULARY.md`)
- Format check (length, line breaks, hashtag usage per client preference)

Failed review routes back to ghostwriter with named gaps. Passed review routes to client approval.

### Rule 5 — Escalation tiering (24h SLA per tier)

Three escalation tiers with explicit SLAs:

| Tier | Trigger | Routes to | SLA |
|---|---|---|---|
| **Tier 1 — AM-resolvable** | Inside AM's scope per Rule 1 (approval logistics, scope-inside-contract questions, cadence adjustments) | AM resolves directly | Acknowledge ≤4 hrs, resolve ≤24 hrs |
| **Tier 2 — Fulfillment-head** | Cross-client patterns, ghostwriter calibration issues, repeat drift across clients, AM-judgment-needed cases | `agents/fulfillment-head.md` | Acknowledge ≤8 hrs, resolve ≤48 hrs |
| **Tier 3 — Operator** | Pricing, contract renegotiation, refund requests, client complaints about operator, exit-clause triggers, hard-drift-2-weeks-unresolved | Operator | Acknowledge ≤24 hrs, decision ≤72 hrs |

Escalation is one-way and logged. AM does not skip-escalate (jump from Tier 1 directly to operator); going through fulfillment-head ensures pattern-recognition across the AM team. Exception: client-side emergencies (legal, PR, contract dispute) can skip-escalate with notice to fulfillment-head in parallel.

### Rule 6 — Channel of record (single, not three)

Per `/onboard-client` Step 2, every client picks ONE channel of record at kickoff: Slack OR Notion OR email. Not all three. Not "Slack for casual, email for formal."

Why: split channels = split context. The AM cannot maintain channel-of-record discipline across three platforms. Approvals get missed because the client sent the approval to the wrong channel. Audit trails fragment.

The AM enforces channel-of-record by gently routing client communication back: if the client emails when the channel is Slack, the AM responds in Slack with "Saw your email — let's continue here so we keep everything in one place." After 2-3 redirects the pattern holds.

Exception: legal / contract communication ALWAYS routes to email regardless of channel-of-record (audit trail requirement).

### Rule 7 — Day-30 onboarding milestone audit (all subprofiles ≥70%)

Per `/onboard-client` Step 9, the AM runs the day-30 milestone audit:
- All 6 client subprofiles ≥70% (voice, ICP, content, case_studies, etc. per recursive paths in `ENCODING.md`)
- Onboarding milestones marked `complete` (voice + ICP + content + outbound)
- ≥5 ghostwritten posts shipped, voice-drift ≥ threshold on all
- DM ops running daily (if in scope) with ≥1 qualified reply
- Client approved ≥80% of posts in queue

If any check fails: surface to operator + AM, agree remediation in writing, do not proceed to month-2 fulfillment without remediation plan. Skipping the audit because "it's mostly fine" is the failure mode this rule prevents — engagements that start at deficit do not recover via monthly reports.

### Rule 8 — Renewal preparation (60 days before contract end)

The AM does not wait until contract end to discuss renewal. 60 days before end:
- Pull last 3 monthly reports
- Pull case-study density (any milestones captured this contract term?)
- Pull voice-drift trend chart for the contract term
- Build renewal narrative: "Here is what we shipped, here is what hit, here is what we'd shift next term, here is the renewal proposal"
- Schedule renewal conversation 45 days out

The renewal conversation is not "do you want to renew" — it is "here is the case for renewal, here is the next-term plan, here is the next-term offer." Clients who get a thoughtful renewal narrative renew at materially higher rates than clients who get a "your contract is up" reminder.

If the renewal is at risk (drift trend bad, success metrics missed, client disengaged), the renewal conversation triggers a tier-2 escalation to fulfillment-head 60 days out so there is time to course-correct or to have the operator take the renewal call directly.

## Output template

```yaml
account_management_record:
  client_slug: ""
  am_slug: ""
  am_owns_since: ""
  client_status: onboarding | active | at-risk | renewal-window | exited
  channel_of_record: slack | notion | email
  weekly_cadence_log:
    monday_drift_audit:
      last_run: ""
      result: PASS | SOFT_DRIFT | HARD_DRIFT
    wednesday_sourcing_pulse:
      last_run: ""
      fresh_inputs_last_7_days: 0
    friday_approval_digest:
      last_sent: ""
      pending_decisions: 0
  monthly_cadence_log:
    last_client_report: ""
    last_strategy_call: ""
    last_case_study_capture_attempt: ""
  approval_queue_status:
    posts_pending_am_review: 0
    posts_pending_client_review: 0
    avg_queue_time_hrs: 0
  escalation_log:
    last_tier_1: ""
    last_tier_2: ""
    last_tier_3: ""
    open_escalations: []
  day_30_milestone_audit:
    audit_date: ""
    all_checks_passed: true | false
    failed_checks: []
    remediation_plan_doc: ""
  renewal_window:
    contract_end_date: ""
    renewal_prep_due: ""                  # 60 days before contract end
    renewal_call_scheduled: ""            # 45 days before contract end
    renewal_narrative_doc: ""
    renewal_status: not-yet-started | prep-in-progress | conversation-scheduled | decided-renew | decided-end
```

## Failure modes

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| AM functions as PM (escalates everything) | Operator's calendar collapses; AM looks busy but adds no decisional value | Decision authority unclear (Rule 1 violated) | Define explicit AM scope at hire; train on contract-citation; redefine escalation tiers |
| Weekly drift audit slipped to monthly | Late drift detection; client churns mid-cycle | Cadence weakened (Rule 2 violated) | Restore Monday cron; AM personally responsible for completion within 24 hrs of trigger |
| Monthly report skipped because "client is busy" | Audit trail fails when scope-creep dispute arises in month 6 | Monthly cadence dropped (Rule 3 violated) | Report ships regardless; client read-receipt is separate from report ship |
| Posts auto-publish to client (no AM review) | Drift-laden posts ship; voice-drift audit becomes reactive | A-6 violated (Rule 4) | Restore AM review gate in approval queue; post cannot move past `am-pending` without AM action |
| AM skip-escalates to operator | Fulfillment-head loses pattern visibility | Rule 5 violated | Restore tier ordering; only skip-escalate on legal/PR/contract emergency with parallel notice |
| Client communicates across 3 channels | Approvals missed; context fragments | Channel-of-record discipline lost (Rule 6 violated) | AM redirects back to channel-of-record after every cross-channel message; pattern holds in 2-3 redirects |
| Day-30 audit deferred or skipped | Engagement starts at deficit; month 4 reveals missing subprofiles | Milestone audit treated as optional (Rule 7 violated) | Block month-2 fulfillment work until audit complete + remediation plan signed |
| Renewal conversation at last week | Client renews under pressure or doesn't renew at all | Renewal prep skipped (Rule 8 violated) | 60-day calendar trigger per active client; renewal narrative built before conversation booked |
| AM caseload >5 clients per AM | All cadence rules slip simultaneously | AM under-staffed | Cap caseload at 4-5; hire next AM before load exceeds cap |

## Cross-references

- `skills/onboard-client/SKILL.md` — Step 3 sets drift threshold; Step 9 day-30 milestone audit
- `skills/voice-drift-detector/SKILL.md` — Monday weekly audit; AM is the audit consumer
- `skills/post-approval-tracker/SKILL.md` — approval queue the AM manages daily
- `skills/build-client-report/SKILL.md` — monthly report the AM ships
- `skills/client-content-sourcing-pulse/SKILL.md` — Wednesday sourcing pulse
- `skills/capture-case-study/SKILL.md` — last-day-of-month case-study capture
- `reference/frameworks/fulfillment/voice-drift-detection.md` — drift framework operationalized in Monday audit
- `reference/frameworks/offer/transformation-hypothesis.md` Rule 3 — case-study density compounds via Rule 3 monthly capture rhythm
- `agents/account-manager.md` (Cycle 2 — TBD) — AM persona definition
- `agents/fulfillment-head.md` — tier-2 escalation owner
- `INVARIANTS.md` N-2 (mandatory voice-drift check), N-3 (no invented results), A-6 (AM-or-operator review on client-bound), A-12 (weekly drift cadence)

---

*Framework version 1.0.0 — 2026-05-03*
