# Value Stack Construction

> The 6-12 priced value items + bonus stack that make the offer's price feel like the smaller number on the page — built on cited anchors, not invented numbers, defensible on a sales call without hedging.

## Why this framework exists

The price line is read in context with the value stack above it. Without the stack, the price reads as a cost. With a credible stack, the price reads as a discount on the cost. The trust band that converts is 3-5× the price; below that the offer reads as overpriced, above that it reads as sales-page math the buyer ignores.

The stack is also the operator's negotiating floor. When a prospect asks for a discount, the operator does not lower the price — the operator removes a value-stack item commensurate with the discount. This works only if the stack is real. Padded stacks force operators into actual price drops because there is nothing real to remove.

The framework exists because the failure mode is consistent: agencies inflate stacks to hit the 3-5× ratio, B2B buyers detect the inflation, the offer closes worse than a no-stack offer would have. **A short, defensible stack outperforms a long, padded one.**

## The 7 rules

### Rule 1 — 6-12 items, not 20+

The mind models 6-12 items as comprehensive. 20+ items reads as desperate. Each additional item past 12 produces decreasing trust gain and increasing risk that the marginal items are filler.

If the stack lands at 6 items after the anti-fluff audit (Rule 7), ship 6. The count that ships is the post-audit count, not the pre-audit count.

### Rule 2 — Each item is a real deliverable

A real deliverable is something the operator actually ships under this offer, with an honest hours-per-unit cost in the operator's time budget. Aspirational items, "we can also help with X" filler, or items the operator only ships sometimes — all fail this rule.

The test: pull the deliverables list from `product_strategy.{tier}.offers[{slug}].deliverables_per_month`. Every value-stack item must trace back to a real entry in that list, OR be a bonus (Rule 6) — never a phantom deliverable that exists only on the sales page.

Anti-pattern (block): "Lifetime updates worth $500." Lifetime updates are not a deliverable in a monthly retainer; they are filler.

### Rule 3 — Anchor pricing per item — competitor-pricing OR replacement-cost (NOT made-up)

Every dollar value gets a cited source. Three valid bases, in priority order:

1. **Competitor pricing** — find 2-3 comparable services priced openly, cite the median with a date-checked URL or screenshot. Best when competitors price publicly. ("Comparable LinkedIn ghostwriting agencies charge $4K-$6K/mo for 20 posts/mo.")
2. **Replacement-cost** — what it would cost the buyer to assemble the equivalent in-house, calculated from market salary data with loaded benefits + overhead. ("A full-time content writer loaded = ~$6K/mo at the buyer's market rate.")
3. **Transformation-value** — used sparingly, only when the deliverable directly produces a measurable outcome with established value. ("A monthly attribution trace saves 4-6 hours of operator analysis time = $800-$1,200 at fractional CMO rates.")

Anti-pattern (block): made-up dollar values, round numbers without justification, anchors copied from another agency's sales page without verification.

### Rule 4 — Stack ordering: largest anchor first, smaller items last

Order by anchor value descending. The buyer's attention is highest on the first 3 items; the heaviest anchors must occupy those slots. Smaller items at the bottom serve as completeness signal — they communicate "and we also do all of these things" without distracting from the lead anchors.

The opposite ordering (small to large) buries the credible heavy anchors and makes the early reading impression weaker.

### Rule 5 — Total stack value > 3-5× offer price (ratio depends on offer tier)

The trust band:
- **Below 3.0×** = "why am I paying more than the in-house equivalent?" — offer reads as overpriced
- **3.0×-5.0×** = the credibility band — offer reads as a clear value
- **Above 5.0×** = "this reads as sales-page math; I don't trust the numbers" — offer reads as inflated

Tier-specific tendencies:
- Pilot / wedge offers: 2.5×-3.5× (low end of band; aggressive pricing for first-cohort proof)
- Standard retainer: 3.0×-4.5× (the sweet spot)
- Premium / enterprise retainer: 4.0×-5.0× (higher anchor density justified by white-glove scope)
- Programs / cohorts: 4.0×-6.0× (higher tolerable ratio because community + curriculum + lifetime access compound — but cap at 6.0× even here)

If ratio falls below 3.0×: add real deliverables OR re-cite anchors using fresh competitor data. Never pad with filler bonuses.
If ratio rises above 5.0× (or 6.0× for programs): strip the lowest-credibility anchors. Often: over-priced replacement-cost anchors set above market rate.

### Rule 6 — 3-5 bonuses (separate from core stack)

Bonuses are deliverables the buyer is *getting included* — they sweeten the close. Value-stack items are deliverables the buyer is *paying for* — they justify the price. Mixing the two makes the core stack feel thin.

Each bonus must:
- Be named specifically (not "additional resources")
- Have an anchored value (per Rule 3)
- Solve an **adjacent buyer problem** the core stack does not address
- Be either one-time on signing OR recurring during contract term (declared explicitly)

Each bonus is keyed to one objection from the agency-ICP `objection_patterns` list. Bonuses without an objection-key are noise — strip them.

### Rule 7 — Anti-fluff audit (defensibility test)

Every item — value-stack and bonus — runs through a 4-check audit:

1. **Is this a real deliverable the operator actually ships?** Blocks aspirational items.
2. **Is the anchor cited from a real source (not invented)?** Blocks made-up dollar values.
3. **Does this item solve a real buyer-side problem (not invented)?** Blocks filler that pads ratio.
4. **Would the operator be comfortable defending this anchor on a sales call?** Blocks over-anchored items the operator privately admits are inflated.

Items that fail any check get stripped. The operator-defensibility test (check 4) is the load-bearing one — if the operator hedges when defending an anchor, the buyer detects the hedge and the trust collapses.

Common stripping outcomes: "Lifetime updates worth $500" (fails 1, 3); "$10K bonus: priority support" (fails 2); "VIP onboarding worth $3K" without specified VIP scope (fails 1, 2); anchors copied from competitor without re-verification (fails 2).

## Output template

```yaml
value_stack:
  offer_slug: ""
  scope: retainer-stack | program-stack
  offer_price: 0                          # The actual price the buyer pays
  items:                                  # 6-12 items per Rule 1, ordered by anchor desc per Rule 4
    - item_name: ""
      what_it_actually_is: ""             # Real deliverable per Rule 2
      anchor_value_usd: 0
      anchor_basis: competitor-pricing | replacement-cost | transformation-value
      anchor_evidence: ""                 # The cited source — Rule 3
      anchor_calculation: ""              # The math
      hours_per_unit: 0                   # Honest operator-time cost
  bonuses:                                # 3-5 per Rule 6
    - bonus_name: ""
      what_it_is: ""
      value_usd: 0
      anchor_basis: competitor-pricing | replacement-cost | transformation-value
      adjacent_problem_solved: ""
      objection_keyed: ""                 # Pointer into agency_icp.objection_patterns
      one_time_or_recurring: one-time-on-signing | recurring-during-term
  totals:
    total_anchor_value: 0                 # Sum of value-stack item anchors
    total_bonus_value: 0                  # Sum of bonus values
    grand_total: 0
    ratio_total_to_price: 0.0
    ratio_band: low | in-band | high      # Per Rule 5
  anti_fluff_audit:
    items_pre_audit: 0
    items_stripped: 0
    items_post_audit: 0
    stripped_log: []                      # Each stripped item + reason
  operator_defensibility_test:
    operator_can_defend_each_anchor_without_hedging: true | false
```

## Failure modes

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| Stack pads past 12 items to look generous | Anti-fluff audit strips heavily, buyer sees thin stack on close | Length confused with value (Rule 1) | Ship the post-audit count even if it's 6 items |
| Phantom deliverables on sales page | Client signs expecting X, agency does not ship X | Aspirational items in stack (Rule 2) | Audit every item against `deliverables_per_month`; strip phantoms |
| Made-up anchor numbers | Buyer asks "where did the $5K come from" and operator stalls | No cited source (Rule 3) | Strip every uncited anchor; rebuild from primary sources only |
| Lead anchor is a small item | First impression is weak | Wrong order (Rule 4) | Reorder by anchor value descending |
| Ratio < 3.0× | Offer reads as overpriced | Stack under-built or anchors under-priced (Rule 5) | Add real deliverables; re-cite anchors with current market data |
| Ratio > 5.0× | Buyer skepticism, "these numbers feel made up" | Inflated anchors (Rule 5) | Strip lowest-credibility anchors; re-cite replacement-cost anchors at actual market rate |
| Bonuses are filler ("email support during business hours") | Bonuses do not move the close | No objection-key (Rule 6) | Strip filler; rebuild bonuses keyed to specific entries in `objection_patterns` |
| Core deliverables labeled as bonuses | Core stack feels thin | Categorization error (Rule 6) | Re-categorize: core = what they pay for, bonus = what they get included |
| Operator hedges when defending an anchor | Anchor does not survive negotiation | Anchor inflation private to operator (Rule 7) | Strip or re-cite; if operator can't defend, the anchor isn't real |
| Anchors copied from competitor without verification | Stale anchors lose buyer trust at audit | No primary-source verification (Rule 3) | Re-cite from primary source with date-checked URL |

## Cross-references

- `reference/frameworks/offer/retainer-architecture.md` Rule 4 — "Value stack anchors at 3-5× price" (canonical structural rule)
- `reference/frameworks/offer/transformation-hypothesis.md` — what the stack ultimately anchors against
- `reference/frameworks/offer/guarantee-strength-tiers.md` — guarantee design references stack-cost to size risk-reversal
- `skills/build-value-stack/SKILL.md` — the build skill (operationalizes this framework)
- `skills/design-retainer-offer/SKILL.md` Step 4 — produces v0 stack consumed here
- `skills/design-program-offer/SKILL.md` Step 9 — references stack for sales-page version
- `INVARIANTS.md` N-3 (no invented numbers), N-4 (no banned vocab), N-13 (anonymized descriptors), A-2 (citation required)
- `spec/BLIND-OUTPUT-TEST.md` — required for program-stack (sales-page-bound) outputs
- `spec/BANNED-VOCABULARY.md` — strip from item descriptions

---

*Framework version 1.0.0 — 2026-05-03*
