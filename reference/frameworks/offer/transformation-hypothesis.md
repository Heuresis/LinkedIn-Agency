# Transformation Hypothesis

> The one-sentence specification of what changes for a buyer in a defined window — the spine that every offer artifact (value stack, guarantee, proposal, onboarding doc, monthly report) is justified against.

## Why this framework exists

Positioning frames the agency. Transformation frames the offer. They are not the same artifact, and treating them as the same is the most common reason a B2B retainer offer reads as generic to its target ICP.

Positioning answers *"who are you and why do you exist."* Transformation answers *"what will be specifically different in my business in N days if I sign this contract."* Without an explicit transformation hypothesis, the value-stack anchor work has nothing to anchor against, the guarantee has nothing falsifiable to promise, and the monthly client report has no scoreboard to trace against. The hypothesis is the load-bearing artifact upstream of every offer-bound deliverable.

The hypothesis is also the point at which operator honesty enforces itself. If the operator cannot name the transformation in one specific sentence backed by case-study density, the offer is not yet ready to ship — and the right move is more case-study work, not better copy.

## The 7 rules

### Rule 1 — Specific, not aspirational

Bad: "Grow your business with LinkedIn." / "Become a thought leader." / "Build inbound on LinkedIn."

Good: "Go from 0 inbound qualified DMs per month to 25 qualified DMs per month from your LinkedIn profile within 90 days."

The test: can the buyer, on day 1, picture exactly what week 12 looks like? If the answer requires interpretation, the hypothesis is too abstract. Specificity = a number, a verb, a measurable artifact.

### Rule 2 — Time-bound (with a defended typical window)

Every hypothesis names a time horizon. The horizon is not picked from intuition — it is defended by the median time-to-result across the agency's case-study set.

If the agency has 5 clients who hit the transformation, the median time was 78 days, the slowest was 102 days, the fastest was 51 days → the public hypothesis time horizon is 90 days (rounded slightly above the median, well inside the slowest). Defending a 30-day window when the median is 78 sets the offer up to fail on month 1 of every engagement.

Time horizons typical for the LinkedIn-agency archetype: 60 days (fast wedge), 90 days (standard), 180 days (multi-touch enterprise). Sub-30-day horizons require pilot-level scope, not full-retainer scope.

### Rule 3 — Backed by case-study density (≥3 wins of this type)

Before claiming a transformation publicly, the agency must have ≥3 closed cases where this specific transformation hit, with proof assets in `case_studies.agency_case_studies`. Per N-3, no inventing. One case study = a coincidence. Three = a pattern the operator can defend.

If the count is below 3, the hypothesis is not yet ready to ship publicly — it ships as a "pilot-tier" transformation with explicit founder-cohort framing and pricing that reflects co-development risk. As the case-study count compounds (case 4, case 5, case 6+), the hypothesis graduates to the standard offer.

### Rule 4 — Falsifiable

Both the operator and the client must be able to look at the engagement at day-N and answer "did the transformation happen — yes or no" without ambiguity. Falsifiability requires the metric in the hypothesis to be measurable from a defined source the client controls or the agency reports against.

Bad metric: "more leads." Bad because *more* is not falsifiable.
Good metric: "≥25 qualified DMs per month" with `qualified` defined as "responded to lead-magnet ask OR booked discovery call OR replied to outbound with intent-class WARM+ per intent taxonomy."

Falsifiable hypotheses make the guarantee writeable. Non-falsifiable hypotheses produce squishy guarantees that erode trust.

### Rule 5 — ICP-mappable

The transformation must match what the agency-ICP segment actually wants — not what is easiest for the agency to deliver. Pull from `ideal_customer_profile.agency_icp.desire_language` and `buying_triggers`. If those segments want "qualified pipeline" and the easiest-to-deliver transformation is "more impressions," the offer ships impressions and churns when month 3 reveals no pipeline.

Cross-check: the hypothesis verb and metric must appear in at least 2 of the top 5 desire-language entries from the agency-ICP profile. If they do not, the operator is selling what is convenient instead of what is wanted.

### Rule 6 — Single-transformation per offer

One offer = one transformation. Bundling — "go from 0 DMs to 25 qualified DMs AND build a 5,000-follower audience AND ship a paid product" — dilutes belief because the buyer rationally discounts the probability of all three landing. Single-transformation offers close at materially higher rates than bundled ones because the buyer can mentally model the path to the win.

If the agency has multiple transformations to sell, ship multiple offers in an offer ladder (`/build-offer-ladder`). Each offer has its own hypothesis, its own value stack, its own guarantee. The ladder is the product surface for *and-and-and*; the individual offer is the surface for *one specific change*.

### Rule 7 — Operator-personally-caused (per N-3)

Per `INVARIANTS.md` N-3, the transformation in the hypothesis must be one the operator (or the agency under operator direction) has personally caused for prior clients — not adjacent transformations, not "we helped a team that helped a client achieve this," not transformations from the operator's pre-agency career unless the methodology and tooling are identical.

Operator-personally-caused = operator can walk through the engagement week-by-week, name the deliverables shipped, name the decisions made, and trace the metric back to specific actions. If the operator cannot do that walkthrough for ≥3 clients, the transformation is not yet personally caused at the density required to ship.

## Output template

```yaml
transformation_hypothesis:
  offer_slug: ""                          # Which offer this hypothesis spines
  one_sentence: ""                        # The full sentence — Rule 1
  metric_name: ""                         # The specific measurable thing — Rule 4
  metric_definition: ""                   # How the metric is measured + source of truth
  baseline_state: ""                      # Where the buyer starts (specific)
  end_state: ""                           # Where the buyer ends (specific number)
  time_horizon_days: 0                    # Defended window — Rule 2
  time_horizon_evidence:
    case_count_in_set: 0                  # ≥3 per Rule 3
    median_days_to_result: 0
    slowest_days_to_result: 0
    fastest_days_to_result: 0
  icp_segment_targeted: ""                # Named segment from agency_icp
  icp_desire_language_match: []           # Top desire-language entries this hypothesis maps to — Rule 5
  case_study_density:
    proven_cases: 0                       # Must be ≥3 for standard offer per Rule 3
    proven_case_slugs: []                 # Pointers into case_studies.agency_case_studies
    pilot_cohort_or_standard: pilot | standard
  falsifiability:
    pass_condition: ""                    # The exact yes-answer for Rule 4
    fail_condition: ""                    # The exact no-answer for Rule 4
    measured_by: agency-report | client-report | both
  bundling_check:
    is_single_transformation: true | false  # Rule 6 — must be true to ship
  operator_personal_causation:
    operator_walked_through_each_case: true | false  # Rule 7
    methodology_same_as_offer: true | false
```

## Failure modes

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| Hypothesis reads as marketing copy | Discovery calls open with "tell me more about what you do" | Specificity gap (Rule 1 violated) | Rewrite with named metric + named horizon + named baseline |
| Time horizon misses on month 1 | Refund requests arrive at day 31 | Time window picked from intuition, not median (Rule 2) | Pull median from case-study set; add 25% buffer; restate publicly |
| Case-study density insufficient | One client churns and the offer loses its only proof | <3 cases when shipping at standard tier (Rule 3) | Demote to pilot-tier with co-development pricing until count reaches 3 |
| Guarantee can't be written | Operator hedges on every guarantee draft | Hypothesis isn't falsifiable (Rule 4) | Restate metric with measurement source; remove squishy verbs |
| Offer closes wrong-fit clients | Bad-fit churn at month 3 | ICP-mapping gap (Rule 5) | Cross-check hypothesis against `agency_icp.desire_language`; rewrite to match top 2 entries |
| Buyer skepticism on close call | "That sounds great but also you mentioned audience growth, attribution, and a community…" | Bundled hypothesis (Rule 6) | Strip to one transformation; route the others to ladder offers |
| Operator can't defend the case in detail | Operator dodges "walk me through how this client got there" question | Causation gap per N-3 (Rule 7) | Halt offer ship; build the case-study walkthrough doc; only re-ship when the walkthrough is fluent |
| Hypothesis copy-pasted across offer tiers | Three offers, identical promise | Single hypothesis serving multiple offers | Each offer needs its own hypothesis with its own metric / horizon / proof |

## Cross-references

- `reference/frameworks/offer/retainer-architecture.md` Rule 1 — "Transformation is named, not implied"
- `reference/frameworks/offer/value-stack-construction.md` — anchors against the transformation value
- `reference/frameworks/offer/guarantee-strength-tiers.md` — guarantee references the hypothesis metric
- `skills/design-retainer-offer/SKILL.md` Step 1 — operationalizes this framework during offer design
- `skills/design-program-offer/SKILL.md` Step 1 — same hypothesis structure for program offers
- `skills/build-icp/SKILL.md` — produces the desire-language and buying-triggers used in Rule 5
- `INVARIANTS.md` N-3 (never invent results), N-8 (per-client transformation hypothesis required for pitch), A-5 (ICP → Voice → Content/DM → Conversion chain)

---

*Framework version 1.0.0 — 2026-05-03*
