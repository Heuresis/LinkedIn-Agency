---
name: strategic-advisory
slug: /strategic-advisory
agent: agency-director
department: cross-cutting
output_format: strategic-advisory-response
required_profiles: {}                    # reads all; no minimum threshold
allowed_scopes:
  - agency-wide
output_path:
  agency-wide: outputs/{date}/strategic-advisory-{question-slug}.md
---

# /strategic-advisory — Open-Ended Strategic Question Handler

> **Purpose.** When the operator asks an open-ended strategic question — "should I hire a VA this month or wait until $40K MRR?", "is my client roster too cohort-1-heavy?", "do I sunset the lowest-tier service or double its price?" — the workspace pulls the relevant profiles + frameworks + playbooks and produces a 2-3-options-with-tradeoffs response, not a single answer.
>
> **Bar.** The operator finishes the response and either makes the decision OR knows the exact piece of evidence they need to surface to make it. No "depends on a lot of things" hand-waving. Every option is a complete decision the operator could execute on Monday morning if they chose it.

## When to invoke

- Operator opens with "what should I do about...", "should I...", "is it time to...", "how do I think about..."
- Decision involves trade-offs across capacity, capital, optionality, brand, time-to-revenue
- Question crosses department boundaries (a hiring question touches fulfillment + acquisition + financial runway; a positioning question touches foundations + marketing + offer)
- Question doesn't map cleanly to a single existing skill — if it does, route to that skill instead
- Phase-transition questions (when do I shift from Phase 1 to Phase 2; do I launch the program tier yet)

## When NOT to invoke

- Symptom-with-a-number ("DM reply rate dropped from 15% to 6% over 4 weeks") → `/diagnose-bottleneck`
- Whole-pipeline-is-fuzzy ("I don't know what to focus on") → `/pipeline-pulse`
- Question maps to a single skill ("how do I write a cold DM sequence") → that skill
- Question is operational / tactical ("what should this week's posts cover") → `/build-posting-calendar` + content strategist

The advisory skill is for **strategic, multi-option, trade-off-heavy questions** — not for diagnostics and not for tactical execution.

## Required inputs

1. The operator's actual question (verbatim — short transcripts of the operator's framing matter for tone calibration)
2. Current `company.yaml` (full read)
3. Current phase per `reference/playbooks/hybrid-full-stack.md`
4. Last `/pipeline-pulse` report if one exists in the last 14 days (anchors the answer to recent reality)
5. Any specific data the operator references in the question (e.g., "I'm at $32K MRR with 9 clients" — confirm against profile)

## The build sequence

### Step 1 — Question classification (3 min)

Map the question to a category. Categories carry default profile + framework loads:

| Category | Trigger phrases | Default profile load | Default framework load |
|---|---|---|---|
| **Positioning** | "category", "what we're known for", "who we sell to", "vertical pivot" | business_context, ideal_customer_profile.agency_icp, brand_voice.agency_voice | `/build-positioning`, `reference/operators/external/`, market sophistication stages |
| **Pricing** | "raise prices", "pricing tier", "discount", "anchor", "package" | product_strategy.service_tier, case_studies, ideal_customer_profile | `/design-retainer-offer`, `reference/frameworks/offer/`, value-stack architecture |
| **Capacity** | "too many clients", "burned out", "cap", "VA", "ghostwriter hire" | business_context.operations.team_structure, fulfillment metrics, case_studies | `reference/playbooks/hybrid-full-stack.md` resource-allocation table per phase |
| **Hiring** | "hire", "VA", "setter", "ghostwriter", "account manager", "fractional" | business_context.operations, current pipeline volume, profile completeness across roles | `agents/_INDEX.md` role definitions + cost-of-not-hiring vs cost-of-hiring math |
| **Offer** | "launch", "new tier", "kill the offer", "productize", "sunset" | product_strategy (full), case_studies, ideal_customer_profile | `/design-retainer-offer`, `/design-program-offer`, `/build-offer-ladder`, hybrid-full-stack phase model |
| **Churn** | "lost", "leaving", "month 4", "renew", "downgrade" | client_voices (per affected client), client_icps (per), client_content (per), fulfillment metrics | `INVARIANTS.md` N-1, N-2, A-12, voice-drift-detector, hybrid-full-stack Phase 2 churn failure mode |
| **Launch** | "program launch", "cohort 1", "first cohort", "scaled launch" | product_strategy.program_tier, case_studies, business_context | `reference/playbooks/hybrid-full-stack.md` Phase 3, `/design-program-offer`, `/build-curriculum`, `/build-first-win-trigger` |
| **Pivot** | "pivot", "stop doing", "go all-in on", "kill", "quit a vertical" | business_context, ideal_customer_profile, case_studies, all per-vertical profiles | `reference/operators/external/`, `reference/playbooks/hybrid-full-stack.md` cross-phase rules |
| **Other** | none of the above | full profile read | full framework registry — operator-led narrowing |

Output: `category: <category>`, `subcategory: <one-line refinement>`, `profile_load: [<list>]`, `framework_load: [<list>]`.

If the question spans two categories, classify both and load both. Spanning three or more = the question is too compound; ask the operator to split it.

### Step 2 — Relevant profile + framework load (5 min)

Hydrate the profiles and frameworks declared in Step 1. For each, write a single line of relevant evidence the answer depends on:

```yaml
loaded_evidence:
  - source: company.yaml::business_context.basic_info.revenue_tier
    value: $40K-$250K/mo
    relevance: phase-tier check — answer differs at Phase 1 vs Phase 2 economics
  - source: company.yaml::case_studies.agency_case_studies (count where use_in_content: true)
    value: 6
    relevance: program-launch eligibility per Phase 3 exit criteria (≥8)
  - source: reference/playbooks/hybrid-full-stack.md Phase 2 § resource_allocation
    value: 35% outbound, 35% content, 20% fulfillment, 10% foundations
    relevance: capacity question — current allocation vs prescribed
  - ...
```

Loaded evidence is the spine of every option. No option may rest on un-loaded evidence — if the option needs evidence the workspace doesn't have, the recommendation becomes "surface this evidence first."

### Step 3 — 2-3 options synthesis (12 min)

Generate 2-3 complete options. Each option is a full decision, not a half-thought. Two-option pulls have an A/B contrast; three-option pulls add a C that's intentionally different in kind (different time horizon, different risk tier, different brand posture).

For each option:

```yaml
- option_letter: A | B | C
  one_line_thesis: <the headline answer>
  full_description: <what the operator actually does, week 1 / month 1 / month 3>
  who_runs_it: <operator alone | operator + workspace | operator + new hire | external>
  capital_required: <usd>
  time_to_first_evidence: <weeks>            # how long until the operator knows if this is working
  reversibility: <reversible-in-30d | reversible-in-90d | hard-to-reverse>
  brand_implication: <strengthens | neutral | risks>
  optionality_implication: <opens-doors | preserves-options | closes-doors>
  best_case_outcome: <one-line, named metric>
  worst_case_outcome: <one-line, named metric, with how-to-detect-early>
```

Synthesis rules:
- Each option must be **internally coherent** — the resource allocation, the brand posture, the team move, the offer mix all fit one operating logic
- Options must be **meaningfully different** — Option A vs B should not be "do it now" vs "do it next month with the same plan." That's a timing variant, not an option
- **No "do nothing" option** unless "do nothing" is genuinely strategic (e.g., the operator is mid-Phase-1 and the question is about Phase 3 launch — do-nothing-now-and-revisit-at-month-12 is the right answer)
- Default to **2 options**. Use 3 only when the third is genuinely different in kind. A weak third option dilutes the choice

### Step 4 — Tradeoff comparison (5 min)

Side-by-side trade-off table. Same fields per option so the operator's eye trains on the contrasts:

```yaml
tradeoff_table:
  cost_operator_hours_30d:
    A: <int>
    B: <int>
    C: <int>
  cost_capital_30d_usd:
    A: <int>
    B: <int>
    C: <int>
  optionality_30d:
    A: <high | medium | low>     # what other moves remain available
    B: ...
    C: ...
  brand_durability_24m:
    A: <strengthens | neutral | risks>
    B: ...
    C: ...
  time_to_first_revenue_signal:
    A: <weeks>
    B: ...
    C: ...
  fit_with_current_phase:
    A: <fits | stretches | mismatches>
    B: ...
    C: ...
  fit_with_operator_energy:
    A: <sustainable | demanding | crashing-risk>
    B: ...
    C: ...
  reversibility_cost_if_wrong:
    A: <usd or operator-hours>
    B: ...
    C: ...
```

The trade-off table is the contract. If two options score identically on every row, they're not different options — collapse and re-synthesize.

### Step 5 — Phase-aware recommendation (5 min)

The recommendation MUST consider the operator's current phase per `reference/playbooks/hybrid-full-stack.md`. The same question gets a different answer at Phase 1 vs Phase 2 vs Phase 3.

Examples of phase-shaped reasoning:
- "Hire a VA?" — Phase 1: probably no, runway-tight, operator must build the SOPs first. Phase 2: probably yes, fulfillment overhead is now the bottleneck, $1500/mo VA buys back 20 hrs/wk. Phase 3: definitely yes, plus a ghostwriter and a setter
- "Launch the program?" — Phase 1: no, no proof. Phase 2 mid: build curriculum quietly. Phase 3 only: launch
- "Sunset the cheapest tier?" — Phase 1: keep, you need the volume to learn. Phase 2: maybe, if it's eating ICP attention. Phase 3: usually yes, productize the tier into the program instead

Output:

```yaml
recommendation:
  preferred_option: A | B | C
  rationale: <2-3 sentences: why this option fits the current phase, current evidence, current operator energy>
  caveats: [<list>]                    # what would change the recommendation
  hedge: <small concrete thing to do this week that costs nothing and preserves option to switch>
```

The hedge is the safety net. Even high-confidence recommendations get a hedge — strategic decisions evolve and the workspace doesn't pretend to certainty it doesn't have. Per `INVARIANTS.md` N-9 ("never claim outputs are final") and A-8 ("show confidence and gap flags").

### Step 6 — Decision-trigger criteria (3 min)

What evidence would cause the operator to choose A vs B vs C in the next 30 / 60 / 90 days? This is the operator's pre-commitment device — they decide *now* what evidence will move them, so they don't get pulled by week-by-week noise.

```yaml
decision_triggers:
  - if_evidence: <observable, named metric or event>
    by_when: 30d | 60d | 90d
    then_choose: A | B | C | reconvene-advisory
  - if_evidence: ...
    by_when: ...
    then_choose: ...
```

Examples:
- "If DM-from-content rate hits ≥15/month by day 60 → choose B (delay VA hire, use the inbound surplus to fund hire at day 90)"
- "If 2+ cohort-1 clients downgrade or churn by day 30 → choose A (pause new sales, run /voice-drift-detector across all active clients)"
- "If revenue does not clear $50K MRR by day 90 → reconvene advisory; the underlying assumption (Phase 2 inflection) was wrong"

Decision triggers separate strategic decisions from emotional decisions. The operator pre-commits; the workspace logs the trigger; the next pulse references it.

## Validation gate (mandatory before declaring done)

Two checks:

1. **The Monday-morning test.** Read the response as the operator on Monday morning, coffee in hand, kids at school. Do they have enough to act? They should be able to: (a) name the recommendation, (b) name the hedge, (c) name the decision trigger that would change the recommendation, (d) take the first action by end of day. If any of those four are missing, the response is too abstract.

2. **The "two operators in the same phase" test.** If two different operators in the same phase with similar profile completeness asked this exact question, they would receive the same recommendation. (Same question, same evidence, same answer. The advisory is not personalized vibes — it's encoded reasoning.) If the recommendation drifts based on the operator's emotional framing rather than the evidence, recalibrate Step 5.

## Output format

```yaml
---
artifact_type: strategic-advisory-response
question_verbatim: "<the operator's question, verbatim>"
question_slug: <kebab-case-derived-slug>
category: <category from Step 1>
subcategory: <subcategory>
phase: 1 | 2 | 3
profiles_used: [<list>]
frameworks_used: [<list>]
loaded_evidence_count: <int>
options_count: 2 | 3
confidence: HIGH | MEDIUM | LOW
validation_gate: passed | failed
advisory_date: YYYY-MM-DD
---

## TL;DR
Recommendation: Option {letter}. {one-line thesis}. Hedge: {one-line}.

## The question
{the operator's verbatim question}

## Loaded evidence
{the populated loaded_evidence from Step 2}

## Options

### Option A — {one-line thesis}
{the populated option spec from Step 3}

### Option B — {one-line thesis}
{the populated option spec}

### Option C — {one-line thesis}      (only if Step 3 produced 3)
{the populated option spec}

## Trade-offs side-by-side
{the populated tradeoff_table from Step 4}

## Recommendation (phase-aware)
{the populated recommendation from Step 5}

## Decision triggers — 30 / 60 / 90 days
{the populated decision_triggers from Step 6}

## Open questions for the operator
{any evidence gaps that, if filled, would shift the recommendation}
```

## Failure modes and guards

- **The "depends on a lot of things" trap.** Symptom: 4 paragraphs of caveats, no recommendation. Guard: TL;DR is the contract; one option must be named or the response doesn't ship.
- **Three weak options instead of two strong.** Operator chooses none and stays paralyzed. Guard: default to 2; only add a third if it's different in kind, not just degree.
- **Phase-blind recommendation.** Symptom: telling a Phase 1 operator to launch a program. Guard: Step 5 is mandatory — phase fit is a row in the trade-off table AND in the recommendation rationale.
- **Recommendation drifts to match operator's tone.** Operator sounds excited about hiring → recommendation skews "yes hire." Operator sounds anxious → recommendation skews "wait." Guard: the validation Test 2 — "two operators, same evidence, same answer."
- **Skipping the hedge.** Operator commits hard, evidence shifts, no exit. Guard: every recommendation has a hedge, even at HIGH confidence.
- **Skipping decision triggers.** Operator agrees in the moment, drifts back to old plan in week 3. Guard: triggers are the pre-commitment.
- **Too much loaded evidence — the report becomes a research dump.** Loaded evidence is filtered to what the options actually rest on. If a piece of evidence isn't load-bearing for at least one option, drop it.
- **Question is actually a diagnostic question.** Operator says "what should I do about my low DM reply rate" — that's `/diagnose-bottleneck`, not advisory. Guard: classifier in Step 1 routes diagnostic-flavored questions to the right skill instead.

## Cross-references

- `SYSTEM.md` Layer 2 §6 — Three-Lens Audit (advisory borrows the layer logic for capacity / hiring / pivot questions)
- `SYSTEM.md` Layer 2 §9 — Interaction Modes (Strategic Advisory mode formal definition)
- `INVARIANTS.md` N-9 — never claim outputs are final (informs the hedge requirement)
- `INVARIANTS.md` A-8 — show confidence and gap flags
- `agents/agency-director.md` — owning agent; escalation path for ≥$5K/mo decisions
- `reference/playbooks/hybrid-full-stack.md` — phase model + per-phase resource allocation (drives Step 5)
- `reference/operators/external/` — operator archetypes for positioning + pivot questions
- `spec/CONTEXT-THRESHOLDS.md` — informs feasibility of options that depend on running specific skills
- `skills/pipeline-pulse/SKILL.md` — ground truth for current state; advisory cites the latest pulse
- `skills/diagnose-bottleneck/SKILL.md` — when the question turns out to be a diagnostic question
- `skills/_INDEX.md` — full skill catalog (every option's "who runs it" cites a real skill or a clearly external action)

## Examples

See `examples/`:
- `example-hire-va-vs-wait-phase-2.md` — capacity question at $32K MRR, recommends hedge-and-wait
- `example-launch-program-now-vs-q4.md` — launch question with 6 case studies (below the Phase 3 threshold of 8)
- `example-sunset-cheapest-tier-vs-double-price.md` — pricing/positioning question, two options A/B
- `example-vertical-pivot-saas-to-services.md` — pivot question, three options (stay / split / pivot)

---

*Skill version 1.0.0 — 2026-05-03*
