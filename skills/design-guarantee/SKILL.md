---
name: design-guarantee
slug: /design-guarantee
agent: offer-architect
department: foundations
output_format: guarantee-document
required_profiles:
  product_strategy: 60                          # target offer tier — service or program
  case_studies.agency_case_studies: 60          # case-study density gates the strongest tiers
allowed_scopes: [retainer-guarantee, program-guarantee]
output_path:
  retainer-guarantee: company.yaml::product_strategy.service_tier.guarantee
  program-guarantee:  company.yaml::product_strategy.program_tier.guarantee
---

# /design-guarantee — Risk-Reversal Architecture

> **Purpose.** Design the guarantee that anchors the offer — the specific, measurable, time-bound promise that flips the buyer's risk calculus. The guarantee is the single highest-leverage element of the offer doc: it is the only line on the page that *spends* the operator's confidence in exchange for closing the deal. Most LinkedIn agencies skip it, hide it behind weasel language ("satisfaction guaranteed"), or ship one too strong for their case-study density and absorb refunds they can't afford. This skill picks the right tier, sets honest mechanics, and writes copy that survives the close + the post-mortem.
>
> **Bar.** A B2B founder reading the guarantee section should be able to (a) name what triggers it, (b) name what they receive when it triggers, (c) name what disqualifies them from triggering it, and (d) feel the operator stands behind it without strain. If the founder reads it and shrugs, the guarantee is wrong. If the founder reads it and the operator's stomach drops, the guarantee is wrong in the other direction. Calibrated correctly, both sides feel the guarantee is *fair* — that is the signal.

## When to invoke

- New retainer offer or new program offer being designed (always invoked from `/design-retainer-offer` Step 6 or `/design-program-offer` Step 6)
- Existing offer's close rate has degraded ≥15% over a quarter and the diagnostic traces to risk-perception (not transformation specificity)
- Case-study density crossed a tier threshold (≥5 wins of one type, ≥10 wins overall) and the operator can now honestly upgrade the guarantee
- A guarantee triggered and was paid out — full retrospective, then redesign with new evidence on the trigger frequency
- Vertical-specific offer launch where the parent offer's guarantee doesn't translate (different metric, different time horizon)
- Operator pivots positioning to "premium / category-king" tier and the existing guarantee reads as defensive

## Required inputs

1. **Target offer.** Which offer this guarantee is for — service-tier `offer_slug` or program-tier `offer_slug`. Determines `output_path` and which case studies count as evidence.
2. **Product strategy profile ≥60%** for the target offer. The transformation hypothesis, deliverables, and price band must all be settled — guarantee is downstream of all three.
3. **Case-studies profile ≥60%** with ≥3 validated transformations of the *same shape* as the offer's transformation hypothesis. This is the evidence that gates which tier is honest.
4. **Margin headroom data.** Gross margin % on the target offer (from `/design-retainer-offer` Step 8 margin honesty check) and the operator's float — how many simultaneous guarantee triggers the agency can absorb without cash strain.
5. **Operator's risk appetite.** A direct conversation, not an inferred number. Some operators ship Tier 5 guarantees on Tier 3 evidence because they trust their delivery; some ship Tier 2 guarantees on Tier 5 evidence because they've been burned. Both are real signals.
6. **Operator available** for a 45-60 minute design session.

## The design sequence

### Step 1 — Risk-asymmetry diagnostic (10 min)

Every guarantee adjusts the perceived asymmetry of risk between buyer and seller. Map both sides explicitly before picking a tier.

```yaml
risk_asymmetry:
  buyer_worst_case_loss:
    financial: 0           # $ paid before they can detect the offer is failing (price × min commitment)
    opportunity: ""        # what they don't do because they hired this agency
    political: ""          # internal credibility cost (CEO pitched LinkedIn agency, agency failed)
    time: 0                # months of LinkedIn momentum lost
  operator_worst_case_loss_if_guarantee_triggers:
    financial: 0           # refund amount + delivery cost already incurred
    capacity: ""           # team-hours redirected to "work-for-free" period
    case_study_damage: ""  # this client becomes anti-proof if exit is messy
    cash_position: ""      # how many simultaneous triggers the agency survives
  ratio_diagnostic: ""     # buyer-loss : operator-loss in $ — and asymmetry beyond $
```

The B2B retainer baseline asymmetry: a $7K/mo retainer × 6-month commitment = $42K buyer-side commitment vs. operator-side ~$12K-$18K of delivered cost. The buyer is taking the larger nominal bet. The guarantee exists to compress that asymmetry.

For the program tier ($7K-$15K paid up-front or split): buyer commits a single large payment with longer horizon-to-results (3-6 months to see compounding wins). Asymmetry is sharper still — and so the guarantee tier must do more work.

### Step 2 — Case-study density gate (10 min)

The guarantee tier is gated by *evidence*, not appetite. Run the count:

| Evidence count | Honest tier ceiling |
|---|---|
| 0 wins of this transformation shape | Tier 1 only (conditional refund). Anything stronger is bluff. |
| 1-2 wins | Tier 2 (performance-based) maximum. Tier 3+ is dishonest. |
| 3-4 wins | Tier 3 (results-or-work-free) honest. Tier 4 is reach. |
| 5-7 wins, all same vertical | Tier 4 (full guarantee) honest within that vertical. |
| ≥8 wins across ≥2 verticals | Tier 5 (better-than-money-back) defensible. |

"Win of this transformation shape" = a closed case study where the *exact* metric in the offer's transformation hypothesis was hit within the *exact* time horizon. Not "we got them growing." Not "they were happy." Hit the metric, in the time, with this offer's mechanism.

If the operator wants a tier above their evidence count, the answer is not "ship anyway." The answer is "ship the honest tier now, run a proof-cohort to build evidence, upgrade in 2 quarters." Surface this directly.

### Step 3 — Guarantee tier selection (10 min)

Five tiers, weakest to strongest. Each has a fit profile and a real cost.

| Tier | Form | When honest | What it costs the operator |
|---|---|---|---|
| **1. Conditional refund** | "If you complete the onboarding + execute on our 30-day plan + we don't ship {N} of {N+M} deliverables at quality bar, refund last 30 days." | Default for first 6 months of a new offer. Works when case-study density is 0-2 and the operator needs *any* risk-reversal language without tail-risk exposure. | ~3-5% of revenue absorbed. Low cash strain. Low close-rate lift (~5-10%). |
| **2. Performance-based** | "If {leading-indicator metric} not hit by {time}, we work additional {N} weeks free until it is OR refund {percentage} of last 30 days, your choice." | 1-2 wins of this shape. Leading-indicator metric (DM reply rate, post-impressions threshold) is operator-controllable; lagging metric (revenue) is not. Use leading-indicator only. | Capacity strain (work-for-free period eats team hours). 8-12% close-rate lift. |
| **3. Results-or-work-free** | "If {lagging-result metric — e.g., 8 qualified discovery calls / 2 signed retainers / $X new pipeline} not hit by {90 days}, we work for free until you do." | 3-4 wins of this exact shape. Operator confident in delivery + has team capacity for extended fulfillment. Most credible mid-tier — no refund cash hit, just extended labor. | 1-2 simultaneous trigger absorption per quarter. 15-20% close-rate lift. |
| **4. Full guarantee** | "If {lagging-result metric} not hit by {90 days}, full refund of all fees paid + you keep all assets we built." | 5-7 wins same vertical. Margin ≥70%. Operator can survive refunding 1-2 deals per quarter without cash strain. | Direct cash exposure. 20-30% close-rate lift. Filters out cheap-shoppers (they assume the guarantee is fake). |
| **5. Better-than-money-back** | "If {metric} not hit by {time}, full refund + we pay you {$X stipend / 1 month of competing-agency retainer / placement assistance with vetted alternative}." | 8+ wins ≥2 verticals. Margin ≥75%. Operator using guarantee as a positioning weapon, not just risk-reversal. | High cash exposure per trigger but rare triggers (filters bad-fit aggressively). 25-40% close-rate lift. Used as headline in sales materials. |

Pick the highest tier that the case-study density honestly supports AND the margin/cash position can absorb at expected trigger frequency. Note: trigger frequency declines as the tier rises — Tier 5 customers are pre-filtered for fit, so triggers are rarer than at Tier 3.

### Step 4 — Guarantee mechanics (10 min)

Once the tier is selected, specify the mechanics in falsifiable terms. Vague mechanics neutralize the guarantee — the buyer reads "if it doesn't work, we'll make it right" and prices in a $0 risk-reversal.

```yaml
guarantee_mechanics:
  trigger_metric:
    name: ""                       # The exact metric — e.g., "qualified discovery calls tagged 'LinkedIn-sourced' in CRM"
    threshold: 0                   # The number — e.g., 8
    time_window: ""                # e.g., "by end of month 3 of engagement"
    measurement_source: ""         # CRM | LinkedIn analytics | shared dashboard | client self-report w/ proof
    measurement_owner: ""          # who computes it — operator | client | shared
  buyer_obligations:               # what the buyer must do for the guarantee to remain valid
    - obligation: ""               # e.g., "Complete onboarding within 14 days"
    - obligation: ""               # e.g., "Approve published posts within 48-hour window"
    - obligation: ""               # e.g., "Provide booked-call tagging in CRM weekly"
  trigger_response_options:
    - option_name: ""              # e.g., "Continued work until metric hit"
      mechanics: ""                # who decides cadence + scope during continuation
      cap: ""                      # max duration / max additional months
    - option_name: ""              # e.g., "Refund of last 30 days"
      mechanics: ""                # processed within X business days
      assets_handling: ""          # buyer keeps content / DMs / lists / etc.
    - option_name: ""              # for Tier 5 only: stipend / placement / etc.
      mechanics: ""
  buyer_choice_window: ""          # e.g., "Buyer chooses option within 14 days of trigger"
  arbitration_clause: ""           # how disputes resolve — usually: independent metric audit by named third party
```

The mechanics live in both the offer doc (summary form) and the contract (full form). The summary form must read identically to a B2B founder and a B2B founder's lawyer — no daylight between the marketing language and the contract language. Daylight is where churn lives.

### Step 5 — Anti-abuse design (10 min)

A guarantee with no buyer obligations gets gamed by 5-10% of buyers. A guarantee with too many obligations reads as bad-faith and depresses close rate. Calibrate to filter actual abuse without filtering legitimate triggers.

The legitimate-trigger profile: buyer executed in good faith, the offer's mechanism didn't deliver the promised result for *their* specific situation. The agency owes them.

The abuse profile: buyer signed without intending to execute, used the work-for-free period to extract assets, then triggers the guarantee as a refund mechanism. Or: buyer's situation changed (acquired, founder left, pivot) and they're trying to exit without the contract penalty.

**Anti-abuse terms that are honest:**

- ✅ Buyer-obligations list (Step 4) — onboarding completion, content approval cadence, attribution-tagging — are the real filters. A buyer who didn't approve posts can't blame the agency for low impressions.
- ✅ Mechanism-execution clause: "Guarantee applies if our deliverables shipped at the promised volume + quality bar AND buyer met the obligations above. If we missed deliverable volume, the guarantee triggers automatically and buyer chooses option."
- ✅ One-time trigger: "Buyer may invoke the guarantee once per contract term."

**Anti-abuse terms that are dishonest (do not use):**

- ❌ "Subjective satisfaction" exclusions — converts the guarantee into theater.
- ❌ Burden-of-proof on the buyer to prove the agency failed — inverts the asymmetry.
- ❌ "We reserve the right to determine whether the guarantee triggered" — operator-as-judge breaks the trust.
- ❌ Hidden conditions in the contract that don't appear in the offer doc — the lawsuit + reputation cost dwarfs any deal saved.

Anti-abuse design is *one paragraph* in the offer doc. If it requires more than one paragraph, the obligations are too heavy.

### Step 6 — Guarantee copy (5 min)

The on-page copy is short, specific, and operator-voiced. It does not hedge. It does not cushion. It states the guarantee, the trigger, the response, and the buyer obligations in the operator's actual voice.

Hydrate from `brand_voice.agency_voice.phrases_to_use` and pattern-match the operator's existing offer-doc copy. Banned vocabulary (`spec/BANNED-VOCABULARY.md`) applies — no "satisfaction guaranteed," no "100% guaranteed results," no "risk-free" headline language without specifics directly underneath.

```markdown
## Our guarantee

{One-sentence statement of the trigger metric and time window — operator's voice.}

If we don't deliver, here's what happens:

{Numbered list of the 1-3 trigger response options from Step 4. Each one is a single sentence — what the buyer gets, with the cap and timeline.}

For this guarantee to apply, you'll need to:
- {Buyer obligation 1 — Step 4}
- {Buyer obligation 2}
- {Buyer obligation 3}

We've designed the obligations to match what working with us actually requires — they're not a trapdoor. If any of them feel unreasonable for your situation, raise it on the discovery call so we can adjust before signing.

{Optional one-line tier-5 escalation: "And if our work doesn't get you there, we'll {stipend / placement language} — because we'd rather lose the deal cleanly than keep you in something that isn't working."}
```

## Output format

```yaml
---
artifact_type: guarantee-document
scope: retainer-guarantee | program-guarantee
target_offer_slug: ""
profile_completeness: 0-100
case_study_density: 0                      # count of wins of this transformation shape
selected_tier: 1 | 2 | 3 | 4 | 5
selected_tier_rationale: ""
margin_check_passed: true | false
risk_appetite_alignment: ""                # what the operator confirmed in Step 1
expected_trigger_frequency_per_quarter: 0  # honest forecast
profiles_used: [product_strategy, case_studies.agency_case_studies, brand_voice.agency_voice]
frameworks_used: [risk-asymmetry-diagnostic, case-study-density-gate, guarantee-strength-tiers]
confidence: HIGH | MEDIUM | LOW
gap_flags: [list]
---

## Risk-asymmetry diagnostic
{populated YAML block from Step 1}

## Case-study density gate result
{count + tier ceiling + rationale from Step 2}

## Selected guarantee tier
{tier number + form + selection rationale from Step 3}

## Guarantee mechanics
{populated YAML block from Step 4}

## Anti-abuse design
{the buyer-obligations summary + the one-paragraph anti-abuse statement from Step 5}

## Offer-doc copy
{the markdown copy block from Step 6 — drop-in for the offer doc}

## Contract-language equivalents
{the same mechanics rewritten in contract-precise language for the SOW — no daylight from offer-doc copy}

## Tier-upgrade path
{what evidence + margin conditions trigger the next tier upgrade. Forecast the upgrade window — usually 2-4 quarters.}

## Next actions
{usually: hydrate `product_strategy.{tier}.guarantee` field; update the live offer doc; brief the closer on the new mechanics; update the contract template.}
```

## Verification checklist

Run before declaring done.

1. **Tier matches evidence.** Case-study density count justifies the selected tier per the Step 2 gate table. If selected tier is above gate ceiling, BLOCK and surface honest tier.
2. **Trigger metric is operator-controllable OR shared-controllable.** Lagging metrics (revenue, close rate) are partially controllable; leading metrics (DM reply rate, post-impression threshold) are operator-controllable. Tier 1-2 should use leading metrics. Tier 3+ may use lagging if buyer obligations are tight.
3. **Trigger metric is unambiguously measurable.** Source is named. Owner is named. No "we'll know it when we see it." If two reasonable people could disagree on whether the trigger fired, the metric is wrong.
4. **Buyer obligations are concrete and non-trapdoor.** Each obligation is something a normal buyer in this ICP segment will naturally do during the engagement. Obligations that require atypical behavior (weekly status reports, custom analytics integration) are trapdoors and depress close rate.
5. **Trigger response options match operator capacity.** If selected tier is "continued work until metric hit," team has capacity to absorb 1-2 simultaneous triggered engagements. If "refund," cash position survives 1-2 simultaneous refunds.
6. **No daylight between offer-doc copy and contract language.** Same mechanics, same triggers, same caps. Any difference = future churn or legal exposure.
7. **Banned vocabulary check** (`spec/BANNED-VOCABULARY.md`). No "satisfaction guaranteed." No "risk-free" without specifics. No "100% guaranteed results."
8. **Blind Output Test** (`reference/canonical/spec/BLIND-OUTPUT-TEST.md`). Show the offer-doc copy block to 3 operators in the agency-ICP segment. Pass criterion: at least 2 say "this reads like a real promise from a real operator," not "this reads like marketing copy." Below pass, iterate the copy in operator's voice.
9. **Margin honesty check.** (price × commitment) × expected close rate − (delivery cost + expected guarantee-trigger cost × trigger frequency) ≥ 60% margin floor. If guarantee math drops gross margin below 60%, downgrade tier or scope down delivery.
10. **Tier-upgrade path defined.** Document explicitly states what evidence + conditions unlock the next tier. Without this, operators leave money on the table — the offer holds at Tier 2 for 18 months because nobody re-runs the gate.

## Common failure modes

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| **Guarantee theater** | "Satisfaction guaranteed" appears in the offer doc | Operator afraid to commit to falsifiable outcome | Re-run Step 2 honestly — if 0 wins, ship Tier 1 *with specifics*, not theater |
| **Tier overshoot** | Tier 4-5 guarantee on 2-win evidence | Operator confidence not calibrated to evidence | Block; offer the proof-cohort path (ship Tier 2 + run a proof-cohort to build evidence) |
| **Vague trigger metric** | "If you don't see meaningful growth" appears | Operator hedging on what "success" means | Force a single metric, single threshold, single time window. If the operator can't pick one, the offer's transformation hypothesis is wrong — kick back to `/design-retainer-offer` Step 1 |
| **Trapdoor obligations** | Buyer obligation list >5 items, includes atypical behavior | Operator anti-abuse anxiety | Cut to 3 obligations max, all of which are normal-engagement behavior |
| **Daylight between offer + contract** | Marketing copy says "full refund," contract says "credit toward future services" | Legal pressure during contract drafting outpaced offer-doc update | Force re-write of one to match the other; the offer doc copy is the source of truth |
| **Cash-strain miscalculation** | Single guarantee trigger threatens payroll | Tier selected without simultaneous-trigger absorption math | Re-run Step 1 with honest float numbers; downgrade tier or build cash buffer first |
| **Stale tier (no upgrade)** | Same Tier-2 guarantee 18 months after evidence supports Tier 4 | Tier-upgrade path not documented | Add quarterly tier-review cadence to the operator's foundations-head review |
| **Subjective-trigger language** | "If you're not satisfied with the work quality" | Operator copying generic guarantee templates | Replace with measurable trigger; subjective-satisfaction triggers cannot be falsified or contested fairly |

## Cross-skill routing

- **Upstream invoked from:** `/design-retainer-offer` Step 6, `/design-program-offer` Step 6, `/build-offer-ladder` Step 5 (when ladder pricing changes guarantee asymmetry)
- **Downstream consumes:** `/build-application` (mentions guarantee in qualification thread), `/build-call-prep` (closer must explain mechanics), `/build-client-report` (reports against trigger metric monthly), `reference/templates/retainer-proposal.md` (renders guarantee section)
- **Triggers re-run when:** case-study density crosses tier threshold, margin shifts ≥10 points, guarantee triggered (full retro + redesign), positioning pivots
- **Cannot be invoked without:** target offer's transformation hypothesis settled (Step 1 of `/design-retainer-offer` or `/design-program-offer`); ≥1 case study of the same shape

## Cross-references

- `reference/frameworks/offer/retainer-architecture.md` Rule 6 — guarantee falsifiability rule + tier ladder
- `skills/design-retainer-offer/SKILL.md` Step 6 — invocation point for retainer guarantees
- `skills/design-program-offer/SKILL.md` (Cycle 2 — TBD) — invocation point for program guarantees
- `skills/build-offer-ladder/SKILL.md` Step 5 — when ladder shifts guarantee asymmetry
- `skills/build-application/SKILL.md` — references guarantee in qualification thread
- `INVARIANTS.md` N-3 — never invent results (case-study density gate enforces this)
- `INVARIANTS.md` N-9 — never claim outputs are final (guarantee mechanics evolve with evidence)
- `INVARIANTS.md` A-4, A-8 — Blind Output Test required, confidence + gap flags shown
- `reference/canonical/spec/BLIND-OUTPUT-TEST.md` — verification protocol for client-bound copy
- `spec/BANNED-VOCABULARY.md` — phrases-to-avoid for guarantee copy
- `agents/foundations-head.md` — owns guarantee-tier upgrade-path review cadence

---

*Skill version 1.0.0 — 2026-05-03*
