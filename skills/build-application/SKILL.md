---
name: build-application
slug: /build-application
agent: acquisition-head
department: acquisition
output_format: application-form-spec
required_profiles:
  ideal_customer_profile: 70                # recursive: agency-icp or client-icp (target scope)
  product_strategy: 70                      # the offer this application gates toward
  dq_criteria_from_build_icp_step_2: exists # explicit hard-DQ list from `/build-icp` Step 2 (closed-lost contrast)
allowed_scopes:
  - agency-application                      # the agency's own application (gates the agency's calendar)
  - client-application                      # rare: a client's application form (gates the client's calendar)
output_path:
  agency-application: company.yaml::content_strategy.agency_content.application_form
  client-application: company.yaml::content_strategy.client_content[{client_slug}].application_form
---

# /build-application — Application Form (the Calendar Gate)

> **Purpose.** Build the application form that gates every discovery call. Output is the form spec: 8-12 questions, hard-DQ logic, scoring rubric, skip-logic, conversational copy, and the submission webhook handler that hands off to `/triage-application`. Per `INVARIANTS.md` N-12, no calendar booking happens without the application — every booking path funnels here. Per A-13, hard-DQ trips do **not** auto-archive; they route to operator review by default.
>
> **Bar.** A B2B founder fills this form in 4-7 minutes; the operator (or workspace on operator's behalf) makes a Booked / AppDQ / AppOperatorReview decision in <90 seconds without re-reading the prospect's DMs.
>
> **The load-bearing role.** This form is what stops the calendar from filling with $0 prospects. It is the highest-leverage quality control mechanism in the acquisition FSM — every other lever (DM sequence, qualification thread, calendar gate) is upstream signal collection; this is where the workspace converts signal into a binding routing decision. Skip it and close rate collapses, operator hours torch.

## When to invoke

- New operator onboarding — build `agency-application` once `ideal_customer_profile.agency_icp` ≥ 70 and `product_strategy.service_tier` ≥ 70
- New retainer offer launched or repriced ≥ 20% — qs question and decline-down routing depend on price band
- Application completion rate < 30% — friction audit; redesign Step 6
- Triage-to-Booked rate > 80% (over-permissive) or < 15% (over-restrictive) — recalibrate Steps 2-5
- No-show rate on Booked calls > 25% — qs question (Step 4) under-weighted; rebuild
- Quarterly DQ-override review per `spec/CONTEXT-THRESHOLDS.md` — operator override > 15% means the criterion is wrong; route back to `/build-icp` Step 2
- New client signed needs their own application form (rare — only when client's offer is high-ticket enough to warrant a gate)

## Required inputs

1. **ICP profile ≥ 70%** — `agency_icp` or `client_icps[slug]`. Questions calibrate to `firmographics`, `behavioral_patterns.buying_triggers`, `voice_of_customer.objection_patterns`.
2. **Product strategy ≥ 70%** — `target_avatar`, `price_point_monthly`, `unique_mechanism` define the qs question and decline-down route.
3. **DQ criteria from `/build-icp` Step 2** — the closed-lost contrast set is the source of every hard-DQ. If absent, this skill **refuses** and routes operator back to `/build-icp` Step 2. No path bypasses it.
4. **`/build-offer-ladder` decline logic** — `decline_logic` (up/down/out) feeds the form's submission-handler routing. Without it, decline-up is missing and operator routes manually.
5. **Existing application form (if any)** — completion rate, drop-off-by-question, operator notes on signal vs. theater. Rebuilds use this as A-9 feedback signal.
6. **Submission webhook target** — CRM endpoint (GoHighLevel, HubSpot, Notion DB id) or workspace-internal `lead.applied` handler. Without a target, the form is decorative.
7. **Operator available** for 60-minute build session — questions and rubrics calibrate against operator's qualification instinct, not generated from null.

## Application architecture (the load-bearing primitive)

A working application is **short, signal-dense, routing-binding**. Most agency applications fail at least one. **8-12 questions, never 25** — past 12, completion halves; past 14, founders fill sloppily; below 8, signal density too low to route without re-reading the DM thread. **Four question classes — every question belongs to exactly one** (mixing breaks the rubric):

| Class | Count | Surfaces | Wrong answer = |
|---|---|---|---|
| **Hard-DQ filter** | 2-4 | Direct binary qualifier ("≥ 3 paying clients?") | AppDQ → routes to OperatorReview by default per A-13, never auto-archive |
| **Scoring** | 5-7 | WHO on a known ICP axis. One question per axis, scored 0-25. | Lower triage tier; never auto-DQ |
| **Qualifying-stake** | 1-2 | WHY-NOW: trigger + opportunity cost + urgency | Booked-without-stake = wasted slot → AppOperatorReview |
| **Fit-and-context** | 1-2 | HOW: decision-makers, timeline, scope, prior-agency baggage | Operator context only; never DQ alone |

**Routing-binding, not advisory.** Each answer must map to an FSM state (Booked / AppDQ / AppOperatorReview per `workflows/divisions/acquisition.md`). If an answer can't move state, the question is theater — cut it. **Conversational tone, never interrogation.** Founders fill conversational forms and abandon interrogations. Questions read like a 1:1 DM from the operator, not a HubSpot intake. Verbatim language from `voice_of_customer.pain_language_patterns` and `voice_of_customer.objection_patterns` loaded into question copy wherever possible.

## The build sequence

### Step 1 — DQ criteria pull from `/build-icp` Step 2 (5 min)

Open the ICP artifact, locate the **disqualifier set** from `/build-icp` Step 2 (closed-lost contrast). Classify each entry: **hard-DQ** (answering "yes" makes a successful retainer impossible — revenue below floor, no budget authority, wrong vertical, prior agency lawsuit — becomes Step 2 question); **scoring DQ** (real but graded, recoverable — becomes Step 3 axis); **stale DQ** (offer/market shifted — drop, flag for ICP refresh). Also pull from `/build-offer-ladder` Step 6: which trips route `decline-up` (back-end advisory), `decline-down` (mid-ticket / program), `decline-out` (named referral).

If `hard_dqs` is empty, this skill **refuses**. Without hard-DQs the form cannot gate the calendar, and per N-12 the calendar must be gated. Operator returns to `/build-icp` Step 2 for a real closed-lost contrast set.

### Step 2 — Hard-DQ question construction (10 min)

For each hard-DQ from Step 1, draft one direct question. Rules: **direct, not euphemistic** ("≥ 3 paying clients?" not "Where are you in your client journey?"); **binary or near-binary** (Yes/No or 3-option scale where one option clearly trips); **single criterion per question** (never compound); **no "we ask this so we can serve you better" preamble** (founders see through it).

```yaml
- question_id: hd-1
  class: hard-dq
  axis: revenue_floor
  answer_type: single-select
  question_copy: "What's your monthly recurring revenue right now? (rough is fine)"
  options:
    - { label: "Pre-revenue / under $10K/mo", trips_dq: true,  dq_route: "decline-down" }
    - { label: "$10K–$50K/mo",  trips_dq: false, score_modifier: -10 }
    - { label: "$50K–$250K/mo", trips_dq: false, score_modifier:   0 }
    - { label: "$250K–$1M/mo",  trips_dq: false, score_modifier:  +5 }
    - { label: "$1M+/mo",       trips_dq: false, score_modifier: +10, decline_up_eligible: true }
  rationale: "Retainer ROI math requires ≥ $10K MRR to break even within month 4."
  invariant_link: "A-13 — DQ trip routes to OperatorReview by default, not auto-archive."
```

Common hard-DQ axes: revenue floor (above), budget authority ("Are you the person who signs off on $5K-$25K/mo, or does someone else? Name them"), timeline reality ("decide in 2 weeks vs. maybe in Q3"), vertical fit ("What industry?" — gates un-encoded verticals per N-11), prior-agency posture.

A `trips_dq: true` answer **never** auto-archives. Per A-13, the Step 7 submission handler routes the prospect to AppOperatorReview with the trip rationale surfaced; operator confirms or overrides. Default = operator review, not algorithm rejection.

### Step 3 — Scoring question construction (15 min)

5-7 ICP axes that aren't binary enough to be hard-DQ but predict deal quality. Each axis = one question, scored 0-25, summed to a 0-175 total triage score (consumed by `/triage-application` Steps 3-4). Default axes for the agency-application archetype: **specificity-of-pain** (can the prospect describe what they want fixed in their own ICP-language, or is it generic?), **trigger event** (named event in prior 90 days?), **decision velocity** (solo / partner-consult / committee — faster = higher), **LinkedIn presence depth** (organic cadence, followers, engagement — is there a base to compound?), **mechanism alignment** (does their approach match the agency's `unique_mechanism`?), **prior-agency learnings** (operator-grade or vague articulation of why prior agency failed?), **offer fit-to-price-band** (self-stated budget posture maps to `price_point_monthly`?).

Each scoring question's copy uses verbatim `voice_of_customer.pain_language_patterns` and `voice_of_customer.desire_language_patterns`. Generic copy = generic answers = unusable score.

```yaml
- question_id: sc-1
  class: scoring
  axis: specificity_of_pain
  answer_type: text-long
  question_copy: 'In 2-3 sentences, what''s the actual thing you want fixed in the next 90 days? (Specifics > vague — "more inbound demos from B2B SaaS founders" beats "more leads".)'
  scoring_rubric:
    - { score: 25, signal: "Names ICP segment + named outcome + named time horizon + verbatim ICP language" }
    - { score: 20, signal: "Names ICP segment + named outcome, no time horizon" }
    - { score: 15, signal: "Names outcome but ICP-segment vague" }
    - { score: 10, signal: "Generic outcome ('more leads', 'grow my brand')" }
    - { score:  5, signal: "Vague aspiration with no measurable outcome" }
    - { score:  0, signal: "Cannot articulate or skipped" }
  rationale: "Specificity-of-pain is the highest single predictor of close rate in this offer category."
```

The rubric is what `/triage-application` Step 3 reads. Without rubric anchors per question, scoring drifts and triage tiers stop meaning anything.

### Step 4 — Qualifying-stake question construction (5 min)

The stake question(s) surface WHY-NOW — what is the cost of *not* solving this? Without stake, capacity (money + time + decision-power) without urgency books a call and ghosts. Stake is what holds the calendar slot.

```yaml
- question_id: qs-1
  class: qualifying-stake
  axis: opportunity_cost
  answer_type: text-long
  question_copy: "If LinkedIn for your business stayed exactly as it is for 12 months — same posts, same outbound, same pipeline — what would that cost you? (Hires you can't make, deals lost to a competitor with stronger presence, runway pressure, founder-time burned, etc.)"
  scoring_rubric:
    - { score: 25, signal: "Specific dollar number tied to a named lost-deal scenario or unmade hire" }
    - { score: 18, signal: "Directional cost (revenue plateau, founder burnout) with concrete anchor" }
    - { score: 10, signal: "Acknowledges cost but cannot anchor it" }
    - { score:  3, signal: "'Nothing major' — no felt urgency" }
  routing_rule: "score < 10 AND total ≥ 131 → AppOperatorReview, not Booked. Capacity without stake = wasted slot. Per /triage-application Step 5."
```

Optional qs-2 (only if qs-1's answer rate < 80%): trigger-specificity ("What changed in the last 90 days that made you start looking now vs. 6 months ago?"). Most operators ship with one.

### Step 5 — Fit/context question construction (5 min)

1-2 questions surfacing decision-maker structure, timeline, scope-context. Never trip a hard-DQ alone; add operator-facing context for the call (consumed by `/build-call-prep`).

```yaml
- { question_id: fc-1, class: fit-context, axis: decision_makers, answer_type: text-short,
    question_copy: 'If we got to "let''s start", who else (if anyone) needs to be on board? (Name them.)',
    feeds_into: "build-call-prep — call structure adapts if 2nd decision-maker is named" }
- { question_id: fc-2, class: fit-context, axis: timeline, answer_type: single-select,
    question_copy: "If we agreed it was a fit, what would your ideal start date look like?",
    options: ["This week", "Within 2-4 weeks", "Next quarter", "Just exploring"],
    routing_rule: '"Just exploring" + low qs-1 → AppOperatorReview. "Next quarter" → Booked, flagged slow-cycle.' }
```

Total count: hard-DQ (2-4) + scoring (5-7) + qs (1-2) + fc (1-2) = **9-15 max, target 10-12**. If > 12, cut lowest-signal scoring question or merge fit-context.

### Step 6 — Form-flow design (5 min)

Two design rules. **Rule 1 — Hard-DQ early.** Place 2-4 hard-DQ questions first; trips short-circuit (skip-logic) straight to the submission handler with `triage_route: AppDQ_to_OperatorReview`. Saves prospect time, preserves data for prospects who matter, reduces DQ-scenario friction. **Rule 2 — Scoring → stake → context.** After hard-DQ passes: 5-7 scoring (warm into specific-thinking mode), then 1-2 qs (hits hardest after specifics surface), then 1-2 fc (easy soft landing before submit).

```yaml
form_flow:
  page_1: { questions: [hd-*], skip_logic: "ANY trips_dq → submit immediately, route=AppDQ_to_OperatorReview, skip remaining" }
  page_2: { questions: [sc-*] }
  page_3: { questions: [qs-*] }
  page_4: { questions: [fc-*], submit_button_copy: "Send my application" }
  conversational_tone:
    voice_source: "brand_voice.{scope}.phrases_to_use, .phrases_to_avoid"
    page_intros:
      page_1: "First — a few quick fit checks."
      page_2: "Now — what we'd actually be working on together."
      page_3: "Last big one — the why-now."
      page_4: "Almost there. Two quick context questions."
  submission_incentive:
    expected_response_time: "Operator reviews within 1 business day."
    next_step_clarity: "If fit, calendar link to 60-min call. If not, candid note + referral if better-fit option exists."
    no_calendar_link_until_triaged: true     # Per N-12 — calendar comes after triage, not submit
```

**Critical N-12 enforcement.** Submit page MUST NOT show a calendar embed. Calendar link is generated by `/triage-application` Step 6 and sent only for Booked tier. Embedding it on submit (or anywhere upstream) violates N-12 and reverts the workspace to vibe-call mode.

### Step 7 — Submission webhook spec (5 min)

Form submission fires a `lead.applied` event triggering `/triage-application` per the FSM (ApplicationGate → ApplicationSubmitted → AppTriaged).

```yaml
submission_webhook:
  event_name: "lead.applied"
  fires_to: "/triage-application"
  payload_schema:
    prospect: { slug, email, linkedin_url, source_dm_thread_id }   # joins to prior `dm.replied`
    application:
      submitted_at: timestamp
      form_version: string                                          # echoed for A/B grouping at triage
      hard_dq_trips: [question_id]
      scoring_answers: { question_id: { value, score, rubric_match } }
      qualifying_stake_answers: { question_id: { value, score } }
      fit_context_answers: { question_id: { value } }
      preliminary_route: "AppDQ_to_OperatorReview | full_triage_required"
    metadata: { utm_source, referrer, fill_time_seconds }           # < 90s sloppy, > 600s abandoned-and-resumed
  fsm_transition: "ApplicationGate → ApplicationSubmitted"
  paperclip_manifest_entry: "triggers.lead.applied: { on: webhook, runs: /triage-application, with: { prospect_slug, application_payload } }"
```

Webhook MUST be idempotent — same `submitted_at + prospect.slug` twice → second dropped. Triage runs exactly once.

### Step 8 — Output emission (5 min)

Hydrate the form spec to `company.yaml` at the configured `output_path` and emit the standalone artifact for operator review.

```yaml
{scope_path}.application_form:
  form_version: "v1.0.0"
  built_at: YYYY-MM-DD
  questions: [ ...hd-*, sc-*, qs-*, fc-* from Steps 2-5 in flow order ]
  form_flow: { ...from Step 6 }
  scoring_rubric:
    total_axes: 5-7
    max_score_per_axis: 25
    max_total_score: 125-175
    triage_tiers: { AppDQ: "0-90", AppOperatorReview: "91-130", Booked: "131+" }
    hard_dq_handling: "any trip → AppOperatorReview by default (per A-13)"
    qualifying_stake_floor: "qs-1 < 10 + total ≥ 131 → AppOperatorReview, not Booked (per /triage-application Step 5)"
  submission_webhook: { ...from Step 7 }
  decline_routing:
    decline_up:   "offer-ladder Step 6 — back-end advisory"
    decline_down: "offer-ladder Step 6 — mid-ticket / program"
    decline_out:  "offer-ladder Step 6 — named referral"
  invariants_enforced: ["N-12", "A-13", "N-11", "A-9"]
```

## Validation gate (mandatory before declaring done)

Five checks. All must pass; failure = the form does not ship and ApplicationGate stays served by the prior form (or manual operator gating).

1. **5-minute fill test.** Operator (or 2-3 ICP-matched test fillers) fills the form. Median fill time 4-7 min. > 7 = too long, cut a scoring question. < 4 = too thin, deepen rubrics.
2. **90-second triage test.** Triage 5 prior-cycle real applications using ONLY the scored answers (no DM context). Operator's call must match live triage tier on 4 of 5. Below 4 = rubric mis-calibrated; re-run Step 3.
3. **Routing-binding check.** For each question: "If the prospect answers X, routing changes to Y." If any question has no routing change for any answer, cut it.
4. **No-calendar-leak check.** Read every page. Calendar embeds, calendar links, "book a time now" CTAs — none. Anywhere. Submission confirmation says "we'll get back in 1 business day," not "pick a time." Fail = N-12 violation.
5. **Voice match.** Pull 3 verbatim phrases from `voice_of_customer.pain_language_patterns` and `voice_of_customer.objection_patterns`. ≥ 2 of 3 must appear in question copy or page intros. Below 2 = generic intake; re-run Steps 3 + 6.

## Output format

```yaml
---
artifact_type: application-form-spec
scope: agency-application | client-application
client_slug: null | "<slug>"
form_version: "v1.0.0"
profile_completeness: 0-100
build_date: YYYY-MM-DD
validation_gate: passed | failed | not-yet-run
question_count: { hard_dq: 2-4, scoring: 5-7, qualifying_stake: 1-2, fit_context: 1-2, total: 9-15 }
profiles_used: [ideal_customer_profile, product_strategy, brand_voice]
frameworks_used: [dq-criteria-pull, hard-dq-construction, scoring-rubric-anchors, qualifying-stake-axis, form-flow-design, submission-webhook]
invariants_enforced: [N-12, A-13, N-11, A-9]
confidence: HIGH | MEDIUM | LOW
gap_flags: [list]
---

## Form architecture summary
{9-15 questions by class with axis tags — one-page operator brief}

## Question copy + scoring rubric
{full question YAML from Steps 2-5 in form-flow order}

## Form flow + skip logic
{page structure from Step 6 with page intros and hard-DQ short-circuit explicit}

## Submission webhook + decline routing
{Step 7 webhook contract; decline-up/down/out from offer-ladder Step 6 mapped to specific hard-DQ trips}

## Validation gate result + hydrate to company.yaml
{result of 5 checks; populated application_form block from Step 8 ready to merge}

## Next actions
{wire webhook to /triage-application; update DM sequences' Step 7 booking-gate to point here; run 5-prospect dry test; brief operator on AppOperatorReview queue cadence}
```

## Failure modes and guards

- **Calendar embed on submit page.** Hardest-to-spot N-12 violation; form builders auto-suggest it. Guard: validation Check 4.
- **Hard-DQ auto-archive.** Workspace silently archives DQ trips, violates A-13. Guard: Step 7 `preliminary_route` for any DQ trip MUST be `AppDQ_to_OperatorReview`, never `archive` — hardcoded.
- **Scoring questions without rubric anchors.** Operator scores differently each time → triage tiers become noise. Guard: Step 3 requires 5-point anchor minimum; Step 8 refuses if < 4 anchors.
- **More than 12 questions.** Completion rate halves past 12. Guard: Step 5 hard cap; cut lowest-signal scoring question.
- **Generic voice.** Reads as HubSpot, completion craters. Guard: validation Check 5 requires verbatim voice phrases.
- **Hard-DQ that's actually a scoring question.** "How sophisticated are you?" is graded, not binary; using it as hard-DQ over-rejects. Guard: Step 1 classification explicit.
- **Qualifying-stake collapsed into scoring.** Capacity-without-urgency books calls and ghosts. Guard: Step 4 mandatory; `/triage-application` Step 5 enforces routing rule.
- **Form-version drift untracked.** A/B tests can't be compared. Guard: Step 8 requires `form_version`; payload echoes for triage grouping.
- **Idempotency gap.** Double-submit → two AppTriaged states race. Guard: Step 7 idempotency on `submitted_at + prospect.slug`.
- **No decline-down route.** Pre-revenue applicant trips revenue-floor DQ, gets cold "not a fit" — burns relationship. Guard: Step 8 requires `decline_routing.decline_down`; ship-block until offer-ladder Step 6 complete.

## Cross-references

- `INVARIANTS.md` N-12 (calendar gated by application; absolute), A-13 (operator on hard-DQ; algorithm cannot auto-archive), N-11 (vertical-fit hard-DQ), A-9 (rejection signal updates profile; form versions track A/B feedback)
- `agents/acquisition-head.md` — owner of this skill + `/triage-application`
- `workflows/divisions/acquisition.md` — FSM ApplicationGate → ApplicationSubmitted → AppTriaged → Booked / AppDQ / AppOperatorReview
- `spec/CONTEXT-THRESHOLDS.md` — `/build-application` thresholds (ICP ≥ 70, Product ≥ 70, DQ criteria exists)
- `skills/build-icp/SKILL.md` — Step 2 (closed-lost contrast) is the source of every hard-DQ
- `skills/build-outbound-engine/SKILL.md` — Step 7 booking gate routes "medium-fit reply" to this form per N-12
- `skills/build-offer-ladder/SKILL.md` — Step 6 decline-up/down/out feeds this form's `decline_routing`
- `skills/triage-application/SKILL.md` — consumes submissions via `lead.applied` webhook
- `skills/build-call-prep/SKILL.md` (Cycle 2 — TBD) — consumes Step 5 fit-context answers for call structure
- `reference/swipe-file/application-questions/` — annotated swipe per archetype
- `reference/templates/decline-and-route.md` — decline-message templates referenced from `decline_routing`
- `paperclip.manifest.yaml` — `lead.applied` webhook trigger declaration

## Examples

See `examples/`: `example-agency-application-hybrid.md` (DFY + program operator's gate), `example-agency-application-saas-vertical.md` (calibrated to SaaS-founder ICP), `example-client-application-rare.md` (rare case of building a client's own application).

---

*Skill version 1.0.0 — 2026-05-03*
