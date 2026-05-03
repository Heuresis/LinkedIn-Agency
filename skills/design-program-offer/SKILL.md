---
name: design-program-offer
slug: /design-program-offer
agent: offer-architect
department: foundations
output_format: program-offer-document
required_profiles:
  business_context: 50
  ideal_customer_profile.agency_icp: 75
  product_strategy.service_tier: 70
  case_studies.agency_case_studies: 60   # at least 3 validated transformations
allowed_scopes:
  - program-offer
output_path:
  program-offer: company.yaml::product_strategy.program_tier
---

# /design-program-offer — High-Ticket Education Program Architecture

> **Purpose.** Build the canonical high-ticket program offer — the $7K-$15K education product that teaches *other* agency operators the playbook this agency has validated on real DFY clients. Curriculum, cohort cadence, mentorship structure, first-win engineering, community ritual, pricing architecture, guarantee, and launch sequence all live here.
>
> **Bar.** The program is downstream of the validated DFY service tier. You cannot launch a credible program without ≥3 validated case studies and a working retainer offer. Reading this program offer doc, an agency operator at the program's ICP segment should be able to (a) name the specific transformation the program teaches, (b) see proof that the operator has personally caused that transformation for clients, (c) understand the cohort cadence and mentorship structure, (d) hold the operator to a falsifiable guarantee. If any of those is unclear, the program is not yet ready to ship.

## When to invoke

- Service tier validated with ≥3 case studies and the operator wants to monetize the playbook at higher margin
- Service-side fulfillment overflow — agency turning away clients and wants a productized path for them
- Operator hits a service-side capacity ceiling and wants the second income stream
- Program v2 redesign after Cohort 1 retrospective surfaces curriculum or cadence gaps
- Major shift in service-tier mechanism that the program must reflect (program is *always* a mirror of validated service work)

## Required inputs

1. **Agency-ICP profile ≥75%** with explicit segment for "agency operators" or "founder-led service businesses" — the program ICP is *agency operators*, not the agency's own end clients.
2. **Validated retainer offer** in `product_strategy.service_tier` ≥70%. The program teaches what this offer ships.
3. **At least 3 validated case studies** in `case_studies.agency_case_studies` with proof assets. Curriculum modules will be derived from these, not from theory.
4. **Operator commitment data** — confirmed weekly hours available for group calls, 1:1 calls, community engagement (cohort programs are operator-time heavy).
5. **Operator available** for a 90-120 minute design session.

## The build sequence

### Step 1 — Program thesis (15 min)

The program teaches one specific transformation. The operator must have *personally caused* that transformation for paying clients before the program can ship — otherwise the program is theory, and the market (which sits at *Skeptical→Exhausted* market sophistication) will smell it within one launch cycle.

Construct a one-sentence program thesis in this shape:

> "{Program-ICP role — agency operator profile} who currently {pain language drawn from the program ICP} will, in {program length}, {desire language tied to a measurable outcome}, taught through the same playbook we ship to our DFY clients (proof: {N} case studies)."

```yaml
program_thesis:
  one_sentence_thesis: ""
  who_it_teaches: ""              # e.g. "Solo or 2-person agency operators below $30K/mo MRR"
  current_pain_language: []       # 3-5 verbatim pains
  desired_transformation: ""      # The specific outcome
  measurable_metric: ""           # Revenue / clients signed / time-per-client / margin
  proof_basis_case_count: 0       # ≥3 required
  operator_personally_caused: true # gate — must be true
```

If `operator_personally_caused` is false, halt the skill. The operator is not yet ready to teach this — re-run service-tier work first.

### Step 2 — Cohort vs. self-paced decision (10 min)

The default for v1 is **cohort, never self-paced**. The decision logic:

- **Cohort (live group)** — required for v1 if proof pool ≥3 case studies AND operator can run weekly group calls AND program ICP needs the social proof of peers signing up alongside them. This is true for nearly every program in this archetype.
- **Self-paced + community** — only after Cohort 2 has proved the curriculum, the first-win triggers, and the mentorship cadence. Launching self-paced before this is the #1 failure mode for first-time program operators (low completion → low results → no case studies → program collapses by month 6).
- **1:1 mentorship-only** — for outlier high-ticket variants ($25K+) above the program tier. Out of scope for this skill; design through `/build-offer-ladder`.

```yaml
delivery_format_decision:
  chosen_format: "cohort"          # cohort | self-paced | 1:1 | hybrid
  cohort_size_target: 0            # 8-15 typical for first cohorts
  cohort_length_weeks: 0           # 8-12 typical
  rationale: ""                    # Why this format for v1
  next_format_trigger: ""          # When self-paced unlocks (typically: post-Cohort-2 with ≥40% completion + ≥30% case-study-rate)
```

### Step 3 — Curriculum architecture (20 min)

Modules are derived from the **real case-study journeys**, not from a theoretical framework. Map each case study's key unlocks → group similar unlocks → name a module per cluster.

```yaml
- module_number: 0
  module_name: ""               # Specific, not abstract
  module_objective: ""          # Capability the student walks out with
  source_case_studies: []       # Which case studies this module derives from
  deliverables_to_student: []   # SOPs, templates, decision trees, recordings
  student_action_required: ""   # What the student does that week (action over consumption)
  measurable_completion_signal: ""
  links_to_first_win: false     # Flag the 1-2 modules that drive Step 4
```

Common 8-12 week module spine: voice + positioning → ICP + first offer → inbound content engine → outbound DM engine → discovery + closing → fulfillment SOPs → scaling architecture → second-offer / program-launch. The exact spine reflects the operator's own playbook — do not import a generic curriculum.

### Step 4 — First-win engineering (15 min)

**Every student must hit a measurable first win in week 2-4.** This is the retention engine — not curriculum quality, not mentor charisma, not community vibe. Without a week 2-4 first win, completion drops below 40% and case-study density collapses.

```yaml
first_win:
  win_definition: ""        # e.g. "First post that breaks 5K impressions" / "First booked discovery call from cold DMs" / "First signed retainer at ≥$3K"
  target_week: 0            # Week 2-4
  win_pct_target: 0         # ≥80% of cohort hits this win
  upstream_modules_required: []         # Which modules deliver the prerequisites
  operator_intervention_trigger: ""     # When operator personally steps in if a student stalls
  proof_capture_protocol: ""            # Screenshot + before/after metric
  public_celebration_protocol: ""       # How the cohort sees the win (drives in-cohort social proof)
```

First-win design is a **constraint on curriculum design** — if Step 3 modules don't deliver the prerequisites for the Step 4 first win by week 2-4, restructure Step 3 until they do.

### Step 5 — Mentorship cadence (10 min)

Two layers — group + 1:1:

```yaml
mentorship_cadence:
  group_calls:
    cadence: ""                  # "Weekly 90-min group call, recorded"
    format: ""                   # "Hot-seat coaching + Q&A" — never "lecture only"
    operator_led: true           # Operator must lead v1; delegating to a coach kills retention
    asynchronous_alternative: "" # Loom hot-seat replies within 24hrs for conflicting timezones
  one_on_one_access:
    tier_included_with_program: "" # e.g. "1x 30-min in week 4 + 1x 45-min in week 10"
    add_on_tier_available: true    # Higher-tier 1:1 as paid add-on
    response_sla_async: ""         # e.g. "Slack DMs answered within 1 business day"
  office_hours:                  # Optional second drop-in per week for stuck students
    cadence: ""
    operator_or_coach_led: ""
```

For v1 cohorts, the **operator personally runs every group call**. Delegation to a coach happens only after Cohort 3 with documented call playbooks. The single most common student-cited refund reason is "I expected to learn from the operator, not from a coach."

### Step 6 — Community ritual design (10 min)

Community is not "we have a Slack channel." Community is the set of **rituals that drive student-to-student case-study creation**. Without rituals, the community goes silent by week 4.

Required rituals (each gets owner + cadence + success signal):

```yaml
community_rituals:
  weekly_win_post:        # Every student posts one win + one stuck (template provided); operator replies to every stuck within 48hrs
  cohort_kickoff_call:    # Week 0 — every student names target ICP + 12-week outcome
  midpoint_retrospective: # Week 6 — cohort identifies what's working; operator adjusts modules 7-12 accordingly
  graduation_case_study:  # Week 12 — each completing student documents before/after as a one-page case study; feeds future program proof
  alumni_channel:         # Permanent — past-cohort students help current-cohort; highest-leverage retention asset
```

### Step 7 — Pricing architecture (10 min)

Three anchors per proof tier:

```yaml
pricing_architecture:
  proof_tier: ""              # founding-cohort | proven-cohort | mature-program
  cost_anchor: 0              # Operator hours/cohort × loaded rate ÷ cohort size → minimum price
  comparison_anchor: 0        # In-house head-of-growth + content team for 12 months ($120K-$240K/yr equivalent)
  outcome_anchor: 0           # Dollar value of the first 2-3 retainers the student signs ($30K-$60K typical)
  price_point: 0              # ≤ 25-35% of outcome_anchor
  payment_options:            # PIF (5-10% discount) | 3-mo plan (+5-10%) | 6-mo plan (+10-15%)
  guarantee:
    form: ""                  # See guarantee logic below
    specific_promise: ""
    qualification_for_refund: [] # Action gates, not vibe
```

**Guarantee form by proof tier:**
- **Founding cohort (cohorts 1-2):** Process-completion + extended access. "Complete every module + 80% of group calls; if you don't have {first win} by week N, we work with you free until you do." Strongest signal; eats operator time but builds case-study density.
- **Proven cohort (cohorts 3-5):** Result-or-refund. "Hit {measurable outcome} by {time}, or refund."
- **Mature program (cohorts 6+):** Selective performance guarantee tied to high-confidence sub-segments of the program ICP.

Skip the guarantee only if the operator is genuinely uncertain — but then surface that as a real strategic question. An uncertain operator should not yet ship a program.

### Step 8 — Launch sequence (10 min)

Pre-launch → application gate → cohort sale window. No "open enrollment forever" — programs in this archetype convert on **scarcity-by-cohort** (named cohort start date + capped seats), not on always-available.

```yaml
launch_sequence:
  pre_launch_phase:
    duration_weeks: 0              # 3-6 typical
    content_arc: ""                # Pillar that surfaces program-ICP pains + operator authority on the transformation
    lead_magnet: ""                # Opt-in that bridges from authority content to application gate
    waitlist_protocol: ""
  application_gate:
    application_link: ""           # Built via /build-application
    auto_disqualifiers: []         # Hard-DQ criteria for program ICP (revenue too low, no validated offer, etc.)
    operator_review_sla_hours: 48  # Per INVARIANTS.md A-13
  cohort_sale_window:
    duration_days: 0               # 5-10 typical — short window forces decision
    seats_capped_at: 0             # 8-15 for v1 cohorts
    sales_call_required: true      # 1:1 enrollment call after application approved
    sales_call_owner: ""           # Operator personally for v1; delegated to closer in v3+
  pre_cohort_intake:
    duration_days: 7               # Between sale-window-close and module-1
    onboarding_protocol_path: ""
```

### Step 9 — Output the program offer doc

Hydrate `product_strategy.program_tier.offers[]`:

```yaml
- offer_slug: "program-cohort-v1"
  offer_name: ""                              # Public-facing, specific to the transformation
  offer_type: "Group cohort"                  # cohort | self-paced | 1:1 | hybrid
  price_point: 0
  payment_options: []                         # from Step 7
  program_length_months: 0                    # converted from cohort_length_weeks
  positioning: ""                             # 1-sentence position
  core_promise: ""                            # The thesis from Step 1
  unique_mechanism: ""                        # What makes THIS program work — names the validated playbook
  curriculum: []                              # from Step 3
  cadence:
    mentorship_calls_per_month: 0             # group calls
    one_on_one_calls_included: 0              # from Step 5
    office_hours: ""                          # from Step 5
    community_engagement: ""                  # from Step 6 — summarized
  success_metrics: []                         # The first win + program-completion outcomes
  guarantee: ""                               # from Step 7
  target_avatar: ""                           # which agency-ICP segment (must be the program-ICP segment, not end-client ICP)
  proof_basis_case_studies: []                # ≥3 from agency_case_studies
  launch_sequence_path: ""                    # from Step 8
```

Plus generate the **public-facing program offer document** as markdown for the sales page and application gate, with these sections in order: `# {offer_name}` → What this is (Step 1 thesis) → Who this is for (target avatar) → Who this is NOT for (disqualifiers) → What you'll achieve in {program length} (first win + end-state outcomes) → How you'll learn it (Steps 5+6 cadence) → What's inside the curriculum (Step 3 modules) → The proof we've done this for clients (≥3 anonymized case studies) → Our guarantee (Step 7) → Investment (price + payment options) → How to apply (application link, never "book a call") → Cohort starts (specific date + capped seats).

## Verification checklist (mandatory before declaring done)

1. **Proof basis is real.** ≥3 case studies in `case_studies.agency_case_studies` with `proof_assets` paths and `use_in_sales: true`. The operator has personally caused this transformation for paying clients.
2. **First win is engineered, not hoped.** Step 4 names a specific week-2-4 win, the upstream modules that deliver its prerequisites, and the operator-intervention trigger.
3. **Curriculum maps to case studies.** Every module in Step 3 has at least one source case study cited. No module derived from theory.
4. **Operator commitment is honest.** Step 5 cadence × cohort size × cohort length ≤ operator's confirmed weekly hours. Cohort programs collapse when the operator over-commits at design time.
5. **Guarantee is falsifiable.** Step 7 names a specific promise tied to a measurable outcome and a clear qualification gate. "Satisfaction guaranteed" fails this check.
6. **Cohort vs. self-paced gate.** v1 is cohort unless explicitly justified. If self-paced, document why the gates in Step 2 are met.
7. **Application gate exists.** Step 8 names the application protocol; `/build-application` is queued or already complete for the program ICP.
8. **Blind Output Test.** Per `spec/BLIND-OUTPUT-TEST.md`: 3 simulated reviewers (matched to the program-ICP — agency operators in the target segment) read the public-facing program offer doc; ≥2 of 3 say "this came from a human operator who has actually done this work." Below pass: identify the gap (usually proof specificity, mechanism vagueness, or guarantee weakness) and iterate.
9. **Margin honesty check.** Operator hours per cohort × loaded hourly rate vs. price × cohort size. Program gross margin target ≥80%. Below 80%: reduce 1:1 access tier, restructure group-call cadence, or raise price.
10. **No banned vocabulary.** Per `spec/BANNED-VOCABULARY.md` — strip "transform your business," "10X your agency," "next-level," etc.

## Output format

```yaml
---
artifact_type: program-offer-document
profile_completeness: 0-100
design_date: YYYY-MM-DD
proof_basis_case_count: 0
delivery_format: cohort | self-paced | hybrid
margin_check: passed | failed
blind_output_test: passed | conditional | failed | not-yet-run
profiles_used: [business_context, ideal_customer_profile.agency_icp, product_strategy.service_tier, case_studies.agency_case_studies]
frameworks_used: [program-thesis, first-win-engineering, cohort-cadence, guarantee-strength-tiers]
confidence: HIGH | MEDIUM | LOW
gap_flags: [list]
---

{the populated program offer YAML from Step 9}

## Public-facing program offer document
{the markdown program offer doc from Step 9}

## Verification checklist results
{each of the 10 checks with pass/fail + notes}

## Next actions
{usually: `/build-application` for the program-ICP application gate, then `/build-curriculum` to expand each module into deliverables, then `/build-first-win-trigger` to operationalize Step 4}
```

## Common failure modes

| Failure | Fix |
|---|---|
| Program shipped with <3 case studies | Halt. Run service-tier first. Programs without proof-density burn the operator's brand within one cohort. |
| Curriculum derived from theory, not case studies | Restructure Step 3 — every module cites which case study it's drawn from. |
| Self-paced launched as v1 | Redo as cohort v1; self-paced unlocks only after Cohort 2 with documented completion + first-win rates. |
| First win not engineered | Restructure Step 3 + Step 4 together — modules must deliver the week-2-4 win prerequisites. |
| Operator over-commits at design time | Honest hours audit in Step 5; restructure cadence or raise cohort price. |
| Guarantee theater ("satisfaction guaranteed") | If operator can't promise a falsifiable outcome, surface as strategic question — program may not be ready. |
| Open enrollment instead of cohort sale window | Redesign as named-date cohort with capped seats; scarcity-by-cohort converts in this archetype. |
| Program ICP confused with end-client ICP | Validate Step 1 — program teaches *agency operators*, not the agency's own clients. Common v1 error. |
| No application gate | Per `INVARIANTS.md` N-12, no high-ticket sale without application. Queue `/build-application` first. |
| Coach delegates Cohort 1 group calls | Operator must lead v1. Delegating before Cohort 3 kills retention. |

## Cross-skill routing

- **Upstream prerequisites:**
  - `/build-icp` (program-ICP scope) — must be at ≥75% before this skill executes
  - `/design-retainer-offer` — must be at ≥70% (program is downstream of validated service tier)
  - `/extract-founder-voice` (operator voice) — for sales page voice fit
- **Downstream consumers:**
  - `/build-application` — qualification gate built from program ICP + program offer
  - `/build-curriculum` — expands Step 3 modules into student-facing deliverables
  - `/build-first-win-trigger` — operationalizes Step 4
  - `/build-offer-ladder` — ladders the program against service tier and any higher-tier offers
  - `/build-content-engine` (agency-content) — builds the pre-launch content arc (Step 8)
- **Sibling skills:**
  - `/build-value-stack` (program-stack scope) — builds the value stack referenced inside the program offer doc
  - `/design-guarantee` — deepens the Step 7 guarantee design

## Cross-references

- `reference/frameworks/offer/retainer-architecture.md` — structural rules pattern (the program offer mirrors most of these with cohort + curriculum overlays)
- `reference/frameworks/offer/program-architecture.md` (Cycle 2 — TBD) — parallel framework for program-specific structural rules
- `reference/templates/program-sales-page.md` (Cycle 2 — TBD) — sales page template that consumes this offer
- `reference/templates/cohort-onboarding-doc.md` (Cycle 2 — TBD) — onboarding template referenced from offer
- `skills/design-retainer-offer/SKILL.md` — the upstream service-tier offer this program productizes
- `skills/build-value-stack/SKILL.md` — value-stack construction for the program-stack scope
- `INVARIANTS.md` N-3, N-9, N-10, N-12, N-13, N-14, A-4, A-5, A-13 — never invent results, never claim "final," Blind Output Test required, application required, methodology descriptors only, no AI attribution, dependency chain, hard-DQ escalation
- `spec/BLIND-OUTPUT-TEST.md` — the publish-bar gate
- `spec/CONTEXT-THRESHOLDS.md` — gates that prevent premature launch

## Examples

See `examples/` (Cycle 2 — TBD): founding-cohort variant (process-completion guarantee), mature-program variant (result-or-refund), post-cohort-2 self-paced variant.

---

*Skill version 1.0.0 — 2026-05-03*
