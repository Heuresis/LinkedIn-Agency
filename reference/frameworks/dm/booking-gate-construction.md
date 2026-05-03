# Booking Gate Construction

> The structural rules for the application form + triage logic that gates the calendar. Per `INVARIANTS.md` N-12, no calendar without an application. The gate is what stops the calendar from filling with $0 prospects and surfaces the operator's real ICP through binding routing decisions.

## Why this framework exists

The calendar is the most expensive resource in a B2B services agency. A 30-minute discovery call costs the operator (or sales lead) ~45 minutes including prep + recovery. At 8-12 calls per week, that's a full operator-day. If half the calendar slots are filled with prospects who can't afford the offer, don't have the authority to buy, or have no real timeline — the operator burns 50% of their pipeline-conversion capacity on calls that close at 0%.

The booking gate solves this by inserting a routing-binding form between the qualification thread and the calendar. Every booking path funnels through the form. The form's design is what determines whether triage produces signal or noise: 8-12 questions of the right four types, scored against a rubric anchored to the ICP, with skip-logic that short-circuits hard-DQ trips and a submission webhook that triggers `/triage-application`.

Per A-13, hard-DQ trips do **not** auto-archive — they route to OperatorReview by default. The operator (not the algorithm) makes the final call on rejection. This protects against false-positive DQ trips that would burn relationships and referrals.

This framework is the design protocol for the form + triage logic. It pairs with `qualification-thread-design.md` upstream and `/triage-application` downstream.

## The 9 rules

### Rule 1 — 8-12 questions max (longer = drop completion)

Past 12 questions, completion rate halves. Past 14, founders fill sloppily — they batch-skim, give one-word answers, or abandon mid-form. Below 8, signal density is too low to route the prospect without re-reading their DM thread (which defeats the form's purpose).

The target band is 10-12. The four classes (hard-DQ, scoring, qualifying-stake, fit-context) sum to that range: 2-4 hard-DQ + 5-7 scoring + 1-2 qualifying-stake + 1-2 fit-context = 9-15 max, target 10-12. If the form runs past 12, cut the lowest-signal scoring question (the one whose answers are most-tied to other axes already covered).

Founders fill conversational forms in 4-7 minutes. They abandon interrogations at 8+. Length is the ceiling on completion.

### Rule 2 — 4 question types: hard-DQ filters / scoring / qualifying-stake / fit-and-context

Every question belongs to exactly one of four classes. Mixing classes breaks the rubric:

| Class | Count | Surfaces | Wrong answer = |
|---|---|---|---|
| **Hard-DQ filter** | 2-4 | Direct binary qualifier | AppDQ → routes to OperatorReview per A-13 |
| **Scoring** | 5-7 | WHO on a known ICP axis (graded 0-25) | Lower triage tier; never auto-DQ |
| **Qualifying-stake** | 1-2 | WHY-NOW: trigger + opportunity cost | Booked-without-stake = wasted slot |
| **Fit-and-context** | 1-2 | HOW: decision-makers, timeline, scope | Operator context only; never DQ alone |

The class mix is what makes triage tractable. Hard-DQ produces yes/no routing decisions. Scoring produces a 0-175 total that maps to triage tiers. Qualifying-stake gates the Booked tier (capacity without urgency = wasted slot). Fit-and-context is operator-facing, consumed by `/build-call-prep` for call structure.

If a question can't be classed cleanly, it's theater — cut it.

### Rule 3 — Hard-DQ questions are direct ("Do you have ≥3 paying clients?")

Hard-DQ questions are direct, binary or near-binary, single-criterion. No euphemisms ("Where are you in your client journey?"), no compound criteria ("Are you over $50K MRR AND have you been in business >2 years?"), no "we ask this so we can serve you better" preamble (founders see through it).

Bad: "Tell us about your business stage."
Good: "What's your monthly recurring revenue right now? (rough is fine)" with options that map clearly: Pre-revenue / under $10K (trips_dq, decline-down) / $10K-$50K / $50K-$250K / $250K-$1M / $1M+ (decline-up eligible).

The directness is what makes the question routing-binding. A vague hard-DQ ("how big is your team?") produces vague answers ("we're growing fast") that can't trip a binary route. A direct hard-DQ produces a binary that the submission webhook can act on without operator intervention for the obvious cases.

Common hard-DQ axes: revenue floor, budget authority (signs off solo or needs committee?), timeline reality (decide in 2 weeks vs. maybe Q3), vertical fit (gates un-encoded verticals per N-11), prior-agency posture.

### Rule 4 — Scoring questions surface WHO + WHEN

Scoring questions are graded 0-25 against a 5-anchor rubric per question. They surface the WHO (which ICP axis) and WHEN (where in the buying cycle). The 5-7 scoring questions sum to a 0-125 to 0-175 total that `/triage-application` consumes to assign triage tier (AppDQ / AppOperatorReview / Booked).

Default scoring axes: specificity-of-pain (can the prospect describe what they want fixed in their own ICP-language?), trigger event (named event in prior 90 days?), decision velocity (solo / partner-consult / committee), LinkedIn presence depth (organic cadence, followers, engagement), mechanism alignment (does their approach match the agency's `unique_mechanism`?), prior-agency learnings (operator-grade or vague articulation of why prior agency failed?), offer fit-to-price-band (self-stated budget posture maps to `price_point_monthly`?).

Every scoring question carries an explicit 5-point rubric anchor. Without anchors, the operator (or workspace) scores differently each time, triage tiers become noise, and the whole gate stops functioning. Example anchor for specificity-of-pain: 25 = names ICP segment + named outcome + named time horizon + verbatim ICP language; 20 = names ICP segment + outcome, no horizon; 15 = names outcome but ICP-segment vague; 10 = generic outcome ("more leads"); 5 = vague aspiration; 0 = cannot articulate or skipped.

### Rule 5 — Qualifying-stake questions surface WHY-NOW

Qualifying-stake (qs) questions surface what the prospect loses by *not* solving this in the next 90 days. Without stake, capacity (money + time + decision-power) without urgency books a call and ghosts. Stake is what holds the calendar slot.

The default qs question: "If LinkedIn for your business stayed exactly as it is for 12 months — same posts, same outbound, same pipeline — what would that cost you? (Hires you can't make, deals lost to a competitor with stronger presence, runway pressure, founder-time burned, etc.)" The rubric grades from 25 (specific dollar number tied to a named lost-deal scenario) down to 3 ("nothing major" — no felt urgency).

The qs floor is a routing rule, not a soft scoring axis: qs-1 score <10 AND total ≥131 → AppOperatorReview, not Booked. A prospect who scores high on capacity-axes but low on stake is the worst kind of false-positive — they look great on paper, book the call, and ghost. The qs floor is the workspace's defense against that pattern.

Optional qs-2 (only if qs-1 answer rate <80%): trigger-specificity ("What changed in the last 90 days that made you start looking now vs. 6 months ago?"). Most operators ship with one qs question.

### Rule 6 — Fit/context questions surface HOW (decision-makers, timeline, scope)

Fit-and-context (fc) questions surface how the deal would actually run if the prospect signed. They produce operator-facing context for the call (consumed by `/build-call-prep`), not routing-decisive signal. They never trip a hard-DQ alone.

Default fc questions: decision-makers ("If we got to 'let's start', who else (if anyone) needs to be on board? Name them.") and timeline ("If we agreed it was a fit, what would your ideal start date look like?" with options: This week / Within 2-4 weeks / Next quarter / Just exploring).

The decision-maker question feeds call structure: if a 2nd decision-maker is named, the call adapts to address their absence. The timeline question feeds nuanced routing: "Just exploring" + low qs-1 → AppOperatorReview; "Next quarter" → Booked but flagged slow-cycle (different cadence than This week); "This week" with passing scores → Booked with priority slot.

Fit-context is operator context. Treat answers as briefing material, not as triage tier modifiers.

### Rule 7 — Skip-logic: hard-DQ early questions short-circuit

Form flow runs hard-DQ first, then scoring, then qualifying-stake, then fit-context. The reason: hard-DQ trips short-circuit (skip-logic) — the moment a hard-DQ trips, the form submits with `triage_route: AppDQ_to_OperatorReview` and the prospect skips remaining questions.

This pattern saves prospect time (they don't fill 9 more questions for a no), preserves data quality for prospects who matter (the form isn't bloated with answers that don't load-bear), and reduces friction in the DQ scenarios (a soft "thanks for applying — we'll be in touch within 1 business day" beats forcing 4 more minutes of fill).

After hard-DQ passes, the order is scoring → stake → context. Scoring warms the prospect into specific-thinking mode. Stake hits hardest after specifics surface (a prospect who just articulated their pain in detail is primed to articulate the cost of not solving it). Context is the easy soft landing before submit.

The skip-logic is also the form's primary guard against N-12 leakage: a hard-DQ short-circuit cannot show a calendar embed on the submit page. Per N-12, the calendar appears nowhere on the form — the only way to a calendar is through `/triage-application` Step 6 routing to Booked tier.

### Rule 8 — Submission webhook triggers /triage-application (per acquisition FSM)

Form submission fires a `lead.applied` event triggering `/triage-application` per the acquisition FSM (ApplicationGate → ApplicationSubmitted → AppTriaged → Booked / AppDQ / AppOperatorReview). The webhook payload contains the full scored answers, hard-DQ trips, qualifying-stake answers, fit-context answers, plus metadata (form_version, fill_time_seconds, source DM thread join key).

The webhook MUST be idempotent: same `submitted_at + prospect.slug` twice → second dropped. Without idempotency, double-submits race two AppTriaged states and the workspace produces conflicting routing decisions.

The webhook MUST echo `form_version` so triage can group A/B test results correctly. Form-version drift untracked = A/B tests can't be compared = the workspace can't learn from form changes.

The webhook target is configured per operator: GoHighLevel, HubSpot, Notion DB id, or workspace-internal `lead.applied` handler. Without a target, the form is decorative — submissions fall on the floor.

### Rule 9 — Per A-13, hard-DQ trips route to OperatorReview not auto-archive

This is the single most-important rule in the framework. Per A-13, when an application response trips a hard-DQ, the workspace does **not** auto-reject — it routes to the operator with a short brief. Founder-led DQ stays a founder call, not an algorithm call.

The reason: hard-DQ false-positives are expensive. A prospect at $9K MRR (just under the $10K floor) who got incredible referral momentum from a recent client should not be auto-archived because they tripped the revenue-floor question. The operator looks at the brief, sees the referral context, and overrides — the prospect becomes a Booked slot, then a closed client.

The operator brief includes: 2-sentence prospect summary, the trip rationale (which hard-DQ + the answer), all scoring + stake answers (so operator has full context), and recommended decision options (confirm_dq / override_dq_and_continue / flag_for_re_engagement). SLA: <24h.

The submission webhook handler for any hard-DQ trip is hardcoded to `preliminary_route: AppDQ_to_OperatorReview`. There is no path through the form that bypasses operator review on hard-DQ. The workspace cannot silently archive — every breach surfaces.

## Form spec template

```yaml
application_form:
  scope: agency-application | client-application
  client_slug: null | "<slug>"
  form_version: "v1.0.0"
  built_at: YYYY-MM-DD
  questions:
    - { id: hd-1, class: hard-dq, axis: revenue_floor, copy: "...", options: [...with trips_dq + dq_route per option] }
    - { id: hd-2, class: hard-dq, axis: budget_authority, copy: "...", options: [...] }
    - { id: sc-1, class: scoring, axis: specificity_of_pain, copy: "...", scoring_rubric: [5 anchors 0-25] }
    - { id: sc-2, class: scoring, axis: trigger_event, copy: "...", scoring_rubric: [...] }
    - { id: sc-3, class: scoring, axis: decision_velocity, copy: "...", scoring_rubric: [...] }
    - { id: sc-4, class: scoring, axis: linkedin_presence_depth, copy: "...", scoring_rubric: [...] }
    - { id: sc-5, class: scoring, axis: mechanism_alignment, copy: "...", scoring_rubric: [...] }
    - { id: qs-1, class: qualifying-stake, axis: opportunity_cost, copy: "...", scoring_rubric: [...], routing_rule: "qs<10 AND total>=131 -> AppOperatorReview" }
    - { id: fc-1, class: fit-context, axis: decision_makers, copy: "...", feeds_into: build-call-prep }
    - { id: fc-2, class: fit-context, axis: timeline, copy: "...", options: [...with routing_rules] }
  form_flow:
    page_1: { questions: [hd-*], skip_logic: "ANY trips_dq -> submit immediately, route=AppDQ_to_OperatorReview, skip remaining" }
    page_2: { questions: [sc-*] }
    page_3: { questions: [qs-*] }
    page_4: { questions: [fc-*], submit_button_copy: "Send my application" }
    page_intros: { p1: "First — a few quick fit checks.", p2: "Now — what we'd actually be working on together.", p3: "Last big one — the why-now.", p4: "Almost there. Two quick context questions." }
    no_calendar_link_until_triaged: true
  scoring_rubric:
    total_axes: 5-7
    max_score_per_axis: 25
    max_total_score: 125-175
    triage_tiers: { AppDQ: "0-90", AppOperatorReview: "91-130", Booked: "131+" }
    hard_dq_handling: "any trip -> AppOperatorReview by default (per A-13)"
    qualifying_stake_floor: "qs-1 < 10 + total >= 131 -> AppOperatorReview, not Booked"
  submission_webhook:
    event_name: lead.applied
    fires_to: /triage-application
    payload_schema: { prospect, application: {hard_dq_trips, scoring_answers, qs_answers, fc_answers, preliminary_route}, metadata }
    fsm_transition: ApplicationGate -> ApplicationSubmitted
    idempotency: "same submitted_at + prospect.slug -> drop second"
  decline_routing:
    decline_up:   "offer-ladder Step 6 — back-end advisory"
    decline_down: "offer-ladder Step 6 — mid-ticket / program"
    decline_out:  "offer-ladder Step 6 — named referral"
  invariants_enforced: ["N-12", "A-13", "N-11", "A-9"]
```

## Failure modes

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| Calendar embed on submit page | Prospects book direct without triage; no-show rate spikes; N-12 violated | Form builder auto-suggested calendar widget on submit confirmation | Remove every calendar reference from form pages; submit confirmation says "we'll get back in 1 business day" |
| Hard-DQ auto-archive | Workspace silently archives DQ trips; operator never sees them; A-13 violated | Webhook handler routes hard-DQ to `archive` instead of `AppOperatorReview` | Hardcode preliminary_route for hard-DQ trips to AppOperatorReview; never `archive` |
| Scoring questions without rubric anchors | Operator scores same answer differently each session; triage tiers stop meaning anything | Scoring questions written without 5-anchor rubric | Per Rule 4, every scoring question requires 5-point anchor minimum; refuse output if missing |
| More than 12 questions | Completion rate halves | Per Rule 1, hard cap is 12; cut lowest-signal scoring question | Audit for class overlap; merge or cut |
| Qualifying-stake collapsed into scoring | Capacity-without-urgency books calls and ghosts | qs question scored as part of total without routing-rule floor | Restore Rule 5: qs has its own floor rule that gates Booked tier independently of total |
| Fill time <90 seconds | Sloppy answers, low signal | Form too short or poorly framed | If <8 questions, deepen scoring; if conversational copy generic, rewrite per voice profile |
| Fill time >10 minutes | Abandoned-and-resumed; stale data | Form too long or interrogative | Cut to 10-12 questions; rewrite copy per Rule 2 voice |
| Form-version drift untracked | A/B tests can't be compared; learning gate broken | Webhook payload doesn't echo form_version | Per Rule 8, webhook MUST include form_version; refuse output if missing |
| Generic voice / HubSpot tone | Completion craters | Copy not pulled from voice_of_customer.pain_language_patterns | Rewrite ≥2 of 3 verbatim phrases from voice profile into question copy |

## Cross-references

- `skills/build-application/SKILL.md` — the build skill that produces forms from this framework
- `skills/triage-application/SKILL.md` — consumes submissions via `lead.applied` webhook
- `skills/build-qualification-thread/SKILL.md` — the upstream thread that funnels prospects to this gate
- `skills/build-icp/SKILL.md` — Step 2 (closed-lost contrast) is the source of every hard-DQ
- `skills/build-offer-ladder/SKILL.md` — Step 6 decline-up/down/out feeds this form's `decline_routing`
- `skills/build-call-prep/SKILL.md` — consumes Rule 6 fit-context answers for call structure
- `reference/frameworks/dm/qualification-thread-design.md` — the upstream thread architecture
- `reference/frameworks/dm/intent-taxonomy.md` — classification logic that determines which prospects reach the form
- `reference/templates/decline-and-route.md` — decline-message templates referenced from `decline_routing`
- `reference/swipe-file/application-questions/` — annotated swipe per archetype
- `workflows/divisions/acquisition.md` — FSM ApplicationGate → ApplicationSubmitted → AppTriaged → Booked / AppDQ / AppOperatorReview
- `paperclip.manifest.yaml` — `lead.applied` webhook trigger declaration
- `INVARIANTS.md` N-12 (calendar gated by application; absolute), A-13 (operator on hard-DQ; algorithm cannot auto-archive), N-11 (vertical-fit hard-DQ), A-9 (rejection signal updates profile)

---

*Framework version 1.0.0 — 2026-05-03*
