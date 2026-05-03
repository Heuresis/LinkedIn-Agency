---
name: triage-application
slug: /triage-application
agent: acquisition-head
department: acquisition
output_format: application-triage-result
required_profiles:
  application_form: submitted                # the populated submission from `/build-application`'s webhook
  dq_criteria: exists                        # the hard-DQ list (originated by `/build-icp` Step 2, codified into `/build-application` Step 2)
  ideal_customer_profile: 70                 # to validate scoring axes against ICP firmographics + buying triggers
  product_strategy: 70                       # to read the offer's `target_avatar` for Booked-tier calendar selection
allowed_scopes:
  - single-application                       # one /triage-application invocation = one application
output_path:
  single-application: outputs/{date}/triage/{prospect-slug}.md
---

# /triage-application — Application Triage (Score, Sort, Route)

> **Purpose.** Score and sort an incoming application submission. Triggered by the `lead.applied` webhook fired from `/build-application` Step 7 (per the FSM in `workflows/divisions/acquisition.md`: ApplicationSubmitted → AppTriaged). Output is one of three routes: **Booked** (calendar link sent automatically), **AppDQ** (archive with reason + decline message), **AppOperatorReview** (borderline — surfaces to operator with full triage rationale). Per `INVARIANTS.md` N-12, calendar links are minted *only* by this skill, only after triage, only for the Booked tier. Per A-13, hard-DQ trips default to **AppOperatorReview**, not auto-archive — operator confirms or overrides.
>
> **Bar.** Time from `lead.applied` event to one of the three routes is < 30 seconds for the workspace's automated portion. Operator's portion (AppOperatorReview queue review) takes < 90 seconds per item per the partner skill `/build-application` validation Check 2.
>
> **The load-bearing role.** Triage is where the calendar gate becomes binding. The form (`/build-application`) collects signal; this skill converts signal into an FSM state transition. Get the tier boundaries wrong and either close rate collapses (over-permissive Booked) or qualified prospects slip out the side door (over-restrictive AppDQ). Get A-13 wrong and the operator loses authorship of the DQ decision, which is the strongest brand signal the agency emits.

## When to invoke

- **Primary.** Fired automatically by the `lead.applied` webhook. One application = one triage. Idempotent on `submitted_at + prospect.slug` per `/build-application` Step 7.
- **Manual re-triage.** Operator overrides a prior tier (e.g., a 92-scoring AppOperatorReview prospect just had a buying-trigger surface in DM follow-up — re-score, re-route).
- **Batch re-triage.** Quarterly rebuild of `/build-application` (new questions, new anchors) — re-triage the prior cycle's borderline submissions against the new rubric to validate boundaries.
- **Audit re-run.** Tier boundaries shift (Step 4) — re-triage last 30 submissions to surface re-classifications before new boundaries go live.

## Required inputs

1. **Submitted application payload** from `lead.applied` — `prospect`, `application` (hard_dq_trips, scoring_answers with rubric_match, qs_answers, fc_answers), `metadata` (utm_source, referrer, fill_time_seconds).
2. **DQ criteria** — `hard_dqs` from `/build-application` Step 2 with each entry's `dq_route` (decline-up / down / out per offer-ladder Step 6).
3. **Scoring rubric** — per-question `scoring_rubric` anchors (Step 3) and tier boundaries (Step 8). Both must match submission's `form_version`.
4. **DM thread context** — if `source_dm_thread_id` present, hydrate prior exchange. Adds qualitative signal (energy, response cadence, language sophistication).
5. **Prospect's LinkedIn profile** — if `linkedin_url` present, retrieve posting cadence, headline, mutual connections. Validates fc-1 + LinkedIn-presence-depth scoring axis.
6. **Operator's calendar availability** — Booked tier only (Step 6). Live read of operator's Cal.com / Savvycal / Calendly slots in 60-min increments.
7. **Operator-review queue** — `outputs/{date}/triage/operator-review-queue.md`. This skill appends borderline-tier prospects.

## Triage architecture (the load-bearing primitive)

The triage decision has three properties at once: **deterministic, auditable, operator-overridable.** Operator trust degrades fast if the workspace makes inconsistent decisions or consistent decisions the operator can't see reasoning behind.

**Three routes, no fourth.** Booked / AppDQ / AppOperatorReview. Every triage produces exactly one. No "deferred," no "needs more info" (request via DM, re-trigger triage). Ambiguity collapses into AppOperatorReview by design.

**Hard-DQ trips default to AppOperatorReview, never auto-AppDQ.** Per A-13, the workspace does not algorithmically reject. AppDQ is an operator-authored decision (after AppOperatorReview review). The default routing of DQ trips is OperatorReview; only when operator confirms does the prospect move to AppDQ.

**Capacity vs. urgency vs. fit scored separately.** Total score (scoring axes) = fit. qs-1 score = urgency. Hard-DQ trips = capacity. A high-fit (≥ 131) + low-urgency (qs-1 < 10) prospect is **not** Booked — routes to AppOperatorReview because capacity-without-urgency books a call and ghosts. Load-bearing rule from `/build-application` Step 4.

**Decline messages are kind, specific, route-aware.** Per `/build-offer-ladder` Step 6 decline logic, AppDQ messages route to better-fit alternatives when they exist (decline-up / down / out). Cold "you're not a fit" messages burn relationship and erode brand-margin in the LinkedIn community.

## The triage sequence

### Step 1 — Application ingestion (3 sec)

Parse the `lead.applied` payload. Validate against form spec — every expected `question_id` present, no extra fields, `form_version` matches a known build. Failure → triage **halts** to AppOperatorReview with `triage_route: ingestion_failed_check_form_version` (operator handles schema mismatch manually; prior form may have been re-deployed mid-flight).

```yaml
ingestion:
  prospect: { slug, email, linkedin_url, source_dm_thread_id }
  application: { submitted_at, form_version, hard_dq_trips, scoring_answers, qs_answers, fc_answers, preliminary_route }
  metadata: { utm_source, referrer, fill_time_seconds }
  dm_thread_context: <hydrated from source_dm_thread_id, or null>
  linkedin_profile_snapshot: <hydrated from linkedin_url, or null>
  schema_validation: passed | failed
```

If `metadata.fill_time_seconds < 90` flag as `low_fidelity` — rubric scoring still runs but auto-routes to AppOperatorReview regardless of total (sloppy fills produce noisy scores).

### Step 2 — Hard-DQ check (5 sec)

Read `application.hard_dq_trips`. For each tripped question, look up its `dq_route` from the form spec (decline-up / down / out per `/build-offer-ladder` Step 6).

```yaml
hard_dq_check:
  trips: [question_id, ...]
  trip_count: integer
  trip_routes: [{ question_id, dq_route, decline_message_template }]
  decision:
    if trip_count == 0: continue to Step 3
    if trip_count >= 1: route = AppOperatorReview (per A-13 — default, not auto-archive)
                       triage_rationale_for_operator: "Hard-DQ trip(s): {trips with dq_route}. Recommended: confirm AppDQ + send {decline-up | down | out} message OR override (rare — note reason in CRM for quarterly DQ-criterion review)."
```

**Per A-13, the workspace MUST NOT route a hard-DQ trip directly to AppDQ.** The default is AppOperatorReview with rationale and recommended-action surfaced. Operator confirms (→ AppDQ) or overrides (→ continue to Step 3 + Booked or back to OperatorReview based on score). Default-to-OperatorReview is hardcoded; no skill argument or webhook field can short-circuit it.

### Step 3 — Scoring axis tally (5 sec)

For each `sc-*` question, read `scoring_answers[question_id].score` (set during form submission per Step 3 rubric anchors). If `rubric_match` is `null` (rubric ambiguous against the answer), invoke the rubric-matcher subroutine to assign a score using the question's `scoring_rubric` anchors as ground truth. Sum across all 5-7 axes for the **total triage score** (0-175 per `/build-application` Step 8).

```yaml
scoring_tally:
  axes_scored: [{ question_id, axis, raw_answer, score, rubric_match: { anchor_score, anchor_signal } }]
  total_score: integer                                # 0 to (max_axes × 25), typically 0-175
  per_axis_breakdown: "sc-1..sc-7: {score}/25 — {rubric_match.anchor_signal} per axis"
  flag_low_axis_scores: [axis where score ≤ 5]       # surfaced in operator brief
```

Any axis at `0` (couldn't articulate / skipped) flags for operator visibility — not a DQ on its own, but a pattern across axes suggests "see what happens" filling rather than real intent.

### Step 4 — Tier assignment (1 sec)

Apply tier boundaries from `/build-application` Step 8:

| Total score | Tier | Default action |
|---|---|---|
| **0-90** | AppDQ | (Per A-13, this default routes via Step 2 only when paired with a hard-DQ trip; for low-score-no-trip applications, route is AppDQ with operator-confirmable archive — Step 8 message generation runs.) |
| **91-130** | AppOperatorReview | Borderline — surfaces to operator queue with full triage rationale (Step 7) |
| **131+** | Booked | Calendar link generated and sent (Step 6) — but subject to qualifying-stake check in Step 5 |

**Boundary calibration note.** These tiers are calibrated to the rubric anchors at form-build time. If `/triage-application` returns >70% Booked or <15% Booked across a 30-application sample, the boundaries are wrong and `/build-application` reruns with re-anchored rubrics. Boundaries are not adjusted in this skill — only consumed.

### Step 5 — Qualifying-stake check (1 sec)

Read `application.qs_answers[qs-1].score`. Apply the routing rule from `/build-application` Step 4:

```yaml
qualifying_stake_check:
  qs_1_score: integer       # 0-25
  total_score: integer      # from Step 3
  decision:
    if (total_score >= 131 AND qs_1_score < 10):
      route = AppOperatorReview          # NOT Booked
      override_reason: "capacity-without-urgency = wasted call slot"
      operator_brief: |
        Prospect scored Booked-tier on fit ({total_score}) but qs-1 (opportunity_cost) = {qs_1_score}/25.
        Verbatim qs-1: "{raw_answer}"
        Recommendation: DM a single sharp question to surface stake before sending calendar.
        If stake surfaces in DM, manually re-triage with operator-override.
        If not, archive via AppDQ with decline-down message (re-engage in 90 days).
    else: continue with tier from Step 4 (no override)
```

This is the single most important downstream guard against the leaky calendar. Without it, every "I'd be open to learning more" prospect with no urgency books a call, ghosts, burns 60 min of operator time. Form Step 4 collects the stake signal; this step enforces the routing consequence.

### Step 6 — Calendar-link generation (Booked tier only — 5 sec)

For prospects routing to **Booked**, generate the calendar invite. Cal.com or Savvycal preferred (token-scoped per-prospect link with prospect slug embedded for attribution); Calendly acceptable. Link points to the operator's actual 60-min discovery-call slots — **not** a VA's calendar.

```yaml
calendar_link_generation:
  calendar_provider: cal.com | savvycal | calendly
  calendar_id: <operator's discovery-call calendar — explicitly NOT a VA calendar>
  slot_duration_minutes: 60
  prospect_slug_param: <embedded for attribution + post-call /summarize-discovery-call binding>
  link_url: <generated>
  invite_message_template: |
    {first_name} — operator reviewed your application. We're a fit. Pick a 60-min slot: {link_url}
    Pre-call brief lands 24h before with the 1-2 things I want us to dig into based on what you wrote.
  send_channel: email + DM (if source_dm_thread_id present)
  fsm_transition: "AppTriaged → Booked"
  triggers_downstream: "/build-call-prep fires 24h before scheduled call (per acquisition FSM)"
```

**N-12 enforcement.** This step is the *only* place in the workspace where a calendar link is generated and sent. Embedding links in DM sequences, application form pages, or operator-direct messages outside this skill = N-12 violation. Audit: any calendar link found outside this code path is a workspace defect.

### Step 7 — Operator-review queue entry (AppOperatorReview tier — 3 sec)

For prospects routing to **AppOperatorReview**, append to `outputs/{date}/triage/operator-review-queue.md` with full rationale. Queue is reviewed daily by the operator (acquisition-head responsibility per `agents/acquisition-head.md`).

```yaml
operator_review_entry:
  prospect_slug: <slug>
  submitted_at: <timestamp>
  total_score: integer
  tier_basis: "hard_dq_trip | borderline_91_130 | low_qs_high_total | low_fidelity_fill"
  full_triage_rationale:
    hard_dq_trips: <Step 2 trip_routes if any>
    scoring_breakdown: <Step 3 per_axis_breakdown>
    qs_1_answer_and_score: <verbatim + numeric>
    fc_answers_summary: <decision_makers + timeline>
    dm_thread_summary: <last 5 exchanges if source_dm_thread_id present>
    linkedin_snapshot: <headline + recent posting cadence>
  recommended_action:
    primary: "Booked (override DQ — note reason for ICP refresh) | AppDQ (confirm + send {template}) | DM-to-surface-stake"
    rationale: <one-paragraph operator brief — what this prospect is, what's borderline, what to ask if DMing>
  operator_decision_link: <one-click: Booked / AppDQ / Re-DM-then-re-triage>
  fsm_transition: "AppTriaged → AppOperatorReview"
  sla: "Operator review within 1 business day"
```

The queue keeps borderline judgment operator-authored (A-13 spirit extended to soft-DQ tier). `recommended_action` is the workspace's *opinion*, not its decision — operator reviews and confirms. Quarterly: aggregate decisions to refine `/build-application` rubric anchors (per A-9).

### Step 8 — Auto-DQ message generation (AppDQ tier — 3 sec)

For prospects routing to **AppDQ** (after operator confirms via Step 7, OR for clean low-score-no-trip submissions), send a kind, specific, route-aware decline message. Pull the template from `/build-offer-ladder` Step 6 `decline_protocol` per the matching `dq_route`:

```yaml
auto_dq_message:
  dq_route: decline-up | decline-down | decline-out | low-score-clean
  template_source: "reference/templates/decline-and-route.md::{dq_route}"
  message_body: |
    {first_name} — appreciate you taking the time to fill out the application.
    Honest read: {one-sentence specific reason drawn from the hard-DQ trip or low-score axis — not generic "not a fit"}.
    {Route-specific next step:
      decline-up:      "What you described sounds closer to {back-end advisory}. Reply and I'll point you to the right intake."
      decline-down:    "Most operators in your position get more leverage from {mid-ticket workshop / program} first: {url}."
      decline-out:     "What you're looking for is more in {named-specialist}'s lane than mine. Worth talking: {referral specifics}."
      low-score-clean: "Right now we wouldn't ship the result you'd want. If {specific change} happens in 90 days, ping me."}
    No hard feelings on either side. Good luck with the next 90 days.
  send_channel: email + DM (if source_dm_thread_id present)
  fsm_transition: "AppTriaged → AppDQ → archived"
  log_to: "outputs/{date}/triage/{prospect-slug}.md (full rationale + sent message)"
```

Decline-up/down/out preserves relationship and creates lateral revenue (mid-ticket, referrals, future re-application). Cold "not a fit" does not.

### Step 9 — Output emission (1 sec)

Emit the triage result to `outputs/{date}/triage/{prospect-slug}.md` AND fire the FSM state transition + downstream skill trigger.

```yaml
triage_output:
  prospect_slug: <slug>
  triage_completed_at: <timestamp>
  route: Booked | AppDQ | AppOperatorReview
  total_score: integer
  hard_dq_trips: [question_id]
  qs_1_score: integer
  decision_path: "Step 1 ingestion → Step 2 dq-check → Step 3 tally ({total_score}) → Step 4 tier → Step 5 qs-check → Step 6/7/8 action per route"
  fsm_transition: "AppTriaged → {Booked | AppDQ | AppOperatorReview}"
  downstream_triggers_fired:
    - if Booked: "/build-call-prep scheduled 24h before {scheduled_call_at}"
    - if AppDQ: "decline message sent via {channels}; archived to CRM with reason"
    - if AppOperatorReview: "appended to operator-review-queue.md; SLA = 1 business day"
  invariants_enforced: ["N-12 (calendar gate)", "A-13 (operator on hard-DQ)", "A-9 (rejection signal logged for rubric refinement)", "A-10 (artifact + metadata logged for compounding)"]
```

## Validation gate (mandatory before claiming triage done)

Three checks. All must pass; any failure routes the application to AppOperatorReview with `triage_route: validation_gate_failed`.

1. **Schema validation pass.** Step 1's `schema_validation` = passed. Form-version matches a known build. No missing fields. Failure = form changed mid-flight; operator handles manually.
2. **Routing-decision evidence trail.** `decision_path` includes a one-line reason for the route, citing the Step that produced it. No silent decisions. Failure = workspace cannot explain route → AppOperatorReview.
3. **N-12 + A-13 invariant check.** No calendar link for any non-Booked tier. No AppDQ without (a) operator confirmation via Step 7 OR (b) low-score-clean path with no hard-DQ trip. Failure = invariant violation; halt + escalate to acquisition-head.

## Output format

```yaml
---
artifact_type: application-triage-result
prospect_slug: <slug>
triage_completed_at: <timestamp>
form_version: <version of /build-application form filled>
route: Booked | AppDQ | AppOperatorReview
total_score: integer
qs_1_score: integer
hard_dq_trips: [question_id]
fill_fidelity: high | low
profiles_used: [application_form, dq_criteria, ideal_customer_profile, product_strategy]
frameworks_used: [hard-dq-check, scoring-tally, tier-boundaries, qualifying-stake-check, decline-routing]
invariants_enforced: [N-12, A-13, A-9, A-10]
validation_gate: passed | failed
confidence: HIGH | MEDIUM | LOW
---

## Decision summary
{one-line: route + reason + downstream action}

## Scoring breakdown + hard-DQ check
{Step 3 per_axis_breakdown + total + qs-1 + fc context; Step 2 trip_routes if any, OR "no trips"}

## Route-specific output
{Booked: calendar invite preview + send confirmation + /build-call-prep time}
{AppDQ: decline message sent + dq_route + archive reason}
{AppOperatorReview: queue entry preview + recommended action + SLA}

## Decision path
{Step 1 → Step 9 trace}

## Next actions
{Booked: none further; AppOperatorReview: operator review; AppDQ: archive confirmed}
```

## Failure modes and guards

- **Hard-DQ auto-AppDQ.** Workspace bypasses Step 2's A-13 default and archives directly. Guard: Step 2 hardcoded — `trip_count >= 1` always returns `route = AppOperatorReview`, never `AppDQ`. AppDQ only reached after operator confirms via Step 7.
- **Calendar link sent to non-Booked tier.** N-12 violation. Guard: Step 6 runs only if Step 4 returned Booked AND Step 5 did not override. Validation Check 3 is second-line defense.
- **Capacity-without-urgency Booked.** High total + low qs-1 routes to Booked, prospect ghosts. Guard: Step 5 enforces the override; Step 6 runs only after Step 5 returns no override.
- **Form-version mismatch.** Form redeployed mid-flight, schema drift, scores misaligned. Guard: Step 1 validates `form_version`; mismatch → AppOperatorReview with `ingestion_failed`.
- **Sloppy fill scored at face value.** `fill_time_seconds < 90` produces noisy scores. Guard: Step 1 flags `low_fidelity` → auto-route to AppOperatorReview regardless of total.
- **Decline message generic.** "Not a fit, good luck" burns relationship. Guard: Step 8 requires `dq_route` populated and pulls route-specific template; if null, AppOperatorReview for operator-authored message.
- **Triage result not logged.** Future cycles can't refine rubric. Guard: Step 9 writes to `outputs/{date}/triage/{prospect-slug}.md`; missing log = retry with backoff, escalate after 3 failures.
- **Operator-review queue overflow.** Queue accumulates beyond 1-business-day SLA. Guard: queue length monitored; > 10 entries escalates to agency-director per acquisition-head escalation path.
- **Re-triage clobbers prior decision.** Manual re-triage overwrites Booked prospect's calendar invite. Guard: re-triage requires explicit `--override-prior-route` flag; else "prospect already in {state}" warning.
- **Rubric-matcher subroutine drift.** Step 3 fallback rubric-matching diverges from operator's manual scoring. Guard: monthly 20-triage sample vs. operator re-scoring; > 15% divergence triggers rubric-anchor refresh.

## Cross-references

- `INVARIANTS.md` N-12 (calendar gated — enforced at Step 6), A-13 (operator on hard-DQ — enforced at Step 2), A-9 (rejection signal logged — Step 9), A-10 (artifact + metadata logged — Step 9)
- `agents/acquisition-head.md` — owner of this skill + `/build-application`; runs operator-review queue daily
- `workflows/divisions/acquisition.md` — FSM ApplicationSubmitted → AppTriaged → Booked / AppDQ / AppOperatorReview
- `spec/CONTEXT-THRESHOLDS.md` — `/triage-application` thresholds (form submitted, DQ criteria exists)
- `skills/build-application/SKILL.md` — produces form spec + tier boundaries + rubric anchors this skill consumes
- `skills/build-icp/SKILL.md` — Step 2 (closed-lost contrast) is the upstream source of every hard-DQ; rubric-anchor refresh per A-9 routes back here
- `skills/build-offer-ladder/SKILL.md` — Step 6 decline-up/down/out templates pulled by Step 8 decline-message generation
- `skills/build-outbound-engine/SKILL.md` — Step 7 booking-gate routes "medium-fit reply" to `/build-application`, which funnels here on submit
- `skills/build-call-prep/SKILL.md` (Cycle 2 — TBD) — fires 24h before scheduled call for prospects routed to Booked
- `reference/templates/decline-and-route.md` — message templates for the four decline routes
- `paperclip.manifest.yaml` — `lead.applied` webhook trigger declaration that fires this skill

## Examples

See `examples/`: `example-triage-booked-saas-founder.md` (Booked + calendar invite), `example-triage-app-dq-revenue-floor.md` (hard-DQ trip → operator-confirmed AppDQ + decline-down), `example-triage-operator-review-borderline.md` (91-130 tier + full operator brief), `example-triage-capacity-without-urgency.md` (high total + low qs-1 → AppOperatorReview override).

---
*Skill version 1.0.0 — 2026-05-03*
