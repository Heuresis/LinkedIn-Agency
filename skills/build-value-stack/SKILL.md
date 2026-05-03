---
name: build-value-stack
slug: /build-value-stack
agent: offer-architect
department: foundations
output_format: value-stack-document
required_profiles:
  product_strategy.service_tier: 50    # OR product_strategy.program_tier: 50 — declared at invocation
allowed_scopes:
  - retainer-stack
  - program-stack
output_path:
  retainer-stack: company.yaml::product_strategy.service_tier.value_stack
  program-stack: company.yaml::product_strategy.program_tier.value_stack
---

# /build-value-stack — Offer Value Stack Construction

> **Purpose.** Build the value stack for an offer — the 6-12 distinct value items + their priced anchors that make the offer's price feel like the smaller number on the page. Used inside retainer proposals (retainer-stack scope) and program sales pages (program-stack scope). The value stack is the math that justifies the price; the bonuses are the psychology that solves the last-mile objections.
>
> **Bar.** A B2B founder reading the value stack should arrive at the price line and think *"of course it costs that — and it could honestly cost more."* If they think *"these numbers feel made up,"* the stack is wrong (anchor inflation). If they think *"the total is barely above the price,"* the stack is wrong (anchor under-build). The trust band is 3-5×. Anything outside fails.

## When to invoke

- After `/design-retainer-offer` (Step 4 produces a draft stack; this skill deepens it for the proposal)
- After `/design-program-offer` (Step 9 references the value stack; this skill builds the version that lives on the sales page)
- Major price change on an existing offer (≥20% up or down requires stack rebuild — old anchors no longer hold)
- Conversion-rate drop traced to "value isn't clear" objection on sales calls
- New competitor offer enters the market with a louder stack — audit and respond
- Annual offer audit — anchor prices in the market shift; restated quarterly is healthy

## Required inputs

1. **Scope declaration at invocation.** Operator must declare `--scope retainer-stack` OR `--scope program-stack`. The skill loads from the corresponding offer in `company.yaml`.
2. **Target offer ≥50% completeness.** Either `product_strategy.service_tier.offers[{slug}]` or `product_strategy.program_tier.offers[{slug}]` must be at ≥50%. Without core deliverables defined, there's nothing to anchor.
3. **Operator's deliverables list.** The actual things the operator ships per month (retainer) or per cohort (program). No hypothetical deliverables.
4. **Competitive pricing intelligence.** What comparable services cost in the open market — used for anchor justification, not for matching.
5. **Replacement-cost data where competitive data is thin.** What it would cost the buyer to assemble the equivalent in-house (hires, tools, time).
6. **Operator available** for a 30-45 minute design session (faster than offer design — the inputs are mostly already encoded).

## The build sequence

### Step 1 — Core deliverable inventory (10 min)

List every concrete thing the operator actually ships under this offer. No aspirational items, no "and we also help with…" filler. The list at this step is the **honest deliverable spine** — pulled directly from `product_strategy.{tier}.offers[{slug}].deliverables_per_month` (retainer) or `curriculum` + `cadence` (program).

For each deliverable, capture:

```yaml
- deliverable_name: ""              # Short, specific
  scope: retainer-stack | program-stack
  what_it_actually_is: ""           # Not marketing language — what gets shipped
  volume: ""                        # Specific count or scope
  quality_bar: ""                   # The standard the deliverable must meet
  hours_per_unit: 0                 # Honest operator-time cost
  ships_to: client | student
```

**Retainer-stack typical inventory** (varies by offer type):
- Founder-voice ghostwritten LinkedIn content (volume per month)
- Personalized cold DMs + sequence management
- ICP-precise lead-list build + monthly refresh
- DM qualification + booking management
- Discovery call prep documents
- Monthly client report + attribution traces
- Account review call (strategy + roadmap)
- Voice-drift audit + recalibration cadence
- Direct operator access (channel + cadence specified)

**Program-stack typical inventory** (varies by program format):
- Curriculum modules (count, weekly cadence, format)
- Live group calls / hot-seats (count per cohort)
- 1:1 mentorship calls (count included)
- Voice extraction + ICP build worksheets (the operator's actual templates)
- Hook library + DM sequence library (the operator's swipe file, anonymized)
- Application-gate template + qualification thread template
- Lifetime alumni community access
- Fulfillment SOP library (account management, ghostwriting, reporting)
- First-win coaching trigger (operator intervenes when student stalls)

### Step 2 — Anchor pricing per item (15 min)

This is the load-bearing step. Each item gets a dollar value backed by **competitor pricing** OR **replacement-cost logic** — never made-up numbers. The operator must cite the source for each anchor.

For each item from Step 1:

```yaml
- deliverable_name: ""
  anchor_value_usd: 0
  anchor_basis: ""                  # competitor-pricing | replacement-cost | transformation-value
  anchor_evidence: ""               # The specific source — competitor URL OR market rate OR in-house cost calculation
  anchor_calculation: ""            # The math. e.g. "$6K/mo = mid-tier full-time content writer salary loaded ($72K/yr ÷ 12)"
```

**Anchor-basis hierarchy (preferred → fallback):**

1. **Competitor pricing** — find 2-3 competing services priced openly and cite the median. Best when competitors price publicly. ("Comparable LinkedIn ghostwriting agencies charge $4K-$6K/mo for 20 posts.")
2. **Replacement-cost** — what it would cost the buyer to assemble the equivalent in-house. ("A full-time SDR loaded is ~$6K/mo at the buyer's market rate.")
3. **Transformation-value** — used sparingly, only when the deliverable directly produces a measurable outcome with established value. ("A monthly attribution trace saves 4-6 hours of operator analysis time = $800-$1,200 at fractional CMO rates.")

**Anti-pattern (block):**
- "Value: priceless" — strip and replace with a real anchor.
- "$5,000 — a $50,000 value" without citation — strip until cited.
- Anchors copied from another agency's sales page without verification — re-cite from primary source.
- "Lifetime updates worth $500" — see Step 6 anti-fluff audit; this almost always strips.

### Step 3 — Value stack ordering (5 min)

Order the stack by **anchor value descending** (largest first, smaller items last). The buyer sees the heaviest items first while attention is highest, then sees the smaller items as completeness signal.

Sum the anchor values. The total must hit the **3-5× trust band** vs. the offer price:

```yaml
value_stack_summary:
  scope: retainer-stack | program-stack
  offer_price: 0                    # The actual price the buyer pays (monthly for retainer, total for program)
  total_anchor_value: 0             # Sum of all Step 2 anchor values
  ratio_total_to_price: 0.0         # Target: 3.0 - 5.0
  ratio_band: low | in-band | high  # < 3.0 = low | 3.0-5.0 = in-band | > 5.0 = high
```

**If ratio is < 3.0:** Either the offer is under-built (add real deliverables, not filler) OR the anchors are under-priced (re-cite using fresh competitor pricing). Do not pad with filler bonuses to hit ratio — see Step 6.

**If ratio is > 5.0:** B2B buyers smell sales-page math and bounce. Strip the lowest-credibility anchors first. Common over-inflation: replacement-cost anchors set above market rate ("a fractional CMO costs $25K/mo" when most market rates are $6K-$12K).

### Step 4 — Bonus stack (10 min)

3-5 bonuses, each named, each valued, each tied to one **adjacent buyer problem** that the core stack doesn't directly address. Bonuses increase perceived completeness — they signal "we thought about the things you'll need that aren't in the core scope."

Difference from value-stack items: value-stack items are deliverables the buyer is paying for (justify the price). Bonuses are deliverables the buyer is *getting included* (sweeten the close). The distinction matters — labeling a core deliverable as a bonus makes the core feel thin.

For each bonus:

```yaml
- bonus_name: ""                    # Specific, not abstract
  what_it_is: ""                    # The actual deliverable
  value_usd: 0                      # Anchored, not invented
  anchor_basis: ""                  # competitor-pricing | replacement-cost | transformation-value
  adjacent_problem_solved: ""       # The buyer-side problem this bonus solves that the core stack doesn't
  scope: retainer-stack | program-stack
  one_time_or_recurring: ""         # one-time-on-signing | recurring-during-term
```

**Retainer-stack bonus archetypes:**
- 30-day pilot DM sprint (counters "I need proof in week 4")
- Voice extraction interview + validated voice profile (counters "last ghostwriter never sounded like me")
- Quarterly retainer review + scope renegotiation right (counters "I don't want to be locked in")
- Co-built ICP and disqualifier list before any DMs go out (counters "I don't want random outreach")
- Founder-direct Slack channel for the contract term (counters "I don't want to be passed to a junior")

**Program-stack bonus archetypes:**
- Founding-cohort lifetime alumni access (counters "I want this network long-term")
- Pre-cohort 1:1 voice extraction call (counters "I need help getting started fast")
- Recorded library of all past cohorts' best moments (counters "I want examples from people like me")
- Hot-seat coaching priority slot in week 4 (counters "I'll get lost in a group setting")
- Operator-direct Loom-reply guarantee for stuck moments (counters "what if I get stuck between calls")

**Anti-bonus (strip):**
- "Email support during business hours" — implies the rest is rationed. Anti-value.
- "Lifetime updates" — meaningless unless the deliverable actually updates and matters.
- "PDF guides" — filler. Strip unless the guide is itself a high-value asset with a justifiable anchor.
- Any bonus the operator can't actually deliver — over-promising bonuses is a fast track to refund requests.

### Step 5 — Stack visualization (5 min)

The stack visualization is the markdown block that ships in the proposal (retainer-stack) or sales page (program-stack). Format:

```markdown
## What you get when you {sign / enroll}

**Core value (delivered every {month / cohort}):**

| What | Value |
|---|---|
| {Deliverable 1 — largest anchor first} | ${anchor_value} |
| {Deliverable 2} | ${anchor_value} |
| {Deliverable 3} | ${anchor_value} |
| {... continue descending ...} | ${anchor_value} |
| **Subtotal** | **${sum}** |

**Bonuses included (one-time, on {signing / enrollment}):**

| Bonus | Value | Solves |
|---|---|---|
| {Bonus 1} | ${value} | {adjacent problem} |
| {Bonus 2} | ${value} | {adjacent problem} |
| {Bonus 3} | ${value} | {adjacent problem} |
| **Subtotal** | **${sum}** | |

**Total value: ${grand_total}**
**Investment: ${offer_price}** {payment-options inline if applicable}
```

The "Solves" column is **only on the program-stack sales page**, not the retainer-stack proposal — proposals stay tighter. The retainer-stack proposal version omits the Solves column and uses cleaner two-column tables.

### Step 6 — Anti-fluff audit (5 min)

Every item in the stack — value-stack and bonuses — runs through the anti-fluff audit. Each item must pass all four:

```yaml
anti_fluff_audit:
  - check: "Is this a real deliverable the operator actually ships?"
    blocks: ["aspirational items", "items operator can't deliver at advertised quality"]
  - check: "Is the anchor cited from a real source (not invented)?"
    blocks: ["made-up dollar values", "round numbers without justification"]
  - check: "Does this item solve a real buyer-side problem (not invented)?"
    blocks: ["filler that pads the ratio", "items that exist only to inflate the total"]
  - check: "Would the operator be comfortable defending this anchor on a sales call?"
    blocks: ["over-anchored items the operator privately admits are inflated"]
```

Items that fail any check get stripped. The stack length after the audit is what ships, even if it lands at 6 items instead of 12. **A short, defensible stack outperforms a long, padded stack** — the trust band is built by anchor credibility, not item count.

**Common stripping outcomes:**
- "Lifetime updates worth $500" — fails check 1 and 3
- "$10K bonus: priority support" — fails check 2
- "VIP onboarding worth $3K" without specified VIP scope — fails check 1 and 2
- "Bonus: access to our network" without a specific access mechanism — fails check 1
- Anchors copied from competitor without re-verification — fails check 2

## Verification checklist (mandatory before declaring done)

1. **Scope declared.** Invocation specified `retainer-stack` OR `program-stack`; output writes to the correct path.
2. **Every value-stack item has an anchor basis cited.** No invented numbers. Each anchor traceable to competitor-pricing, replacement-cost, or transformation-value evidence.
3. **Total-to-price ratio is in the 3.0-5.0 trust band.** Ratio computed and recorded in Step 3. Out-of-band fails this check.
4. **Bonuses solve adjacent problems.** Each bonus in Step 4 names the specific buyer-side problem it solves. No "free email support" filler.
5. **Anti-fluff audit run.** Step 6 executed; failed items stripped from the final stack. Item count after stripping recorded.
6. **Operator-defensibility test.** Operator can defend each anchor on a sales call without hedging. Any anchor that requires hedging gets re-cited or stripped.
7. **No banned vocabulary.** Per `spec/BANNED-VOCABULARY.md` — strip any LinkedIn-guru filler ("game-changer," "transform your business," etc.) from item descriptions.
8. **Stack visualization renders cleanly.** Step 5 markdown block tested in proposal/sales-page context — no formatting errors, totals reconcile.
9. **Blind Output Test (program-stack only — sales-page-bound).** Per `spec/BLIND-OUTPUT-TEST.md`: 3 simulated reviewers in the program-ICP segment read the stack visualization; ≥2 of 3 say "the totals feel earned, not invented." Below pass: rebuild anchors. (Retainer-stack bound for proposal use is exempt from formal Blind Output Test but should still pass the operator-defensibility test.)
10. **Stack writes to the correct `company.yaml` path.** `product_strategy.service_tier.offers[{slug}].value_stack` for retainer-stack; `product_strategy.program_tier.offers[{slug}].value_stack` for program-stack.

## Output format

```yaml
---
artifact_type: value-stack-document
scope: retainer-stack | program-stack
target_offer_slug: ""
profile_completeness: 0-100
build_date: YYYY-MM-DD
total_anchor_value: 0
offer_price: 0
ratio_total_to_price: 0.0
ratio_band: in-band | low | high
items_after_anti_fluff: 0
blind_output_test: passed | conditional | failed | not-applicable
profiles_used: [product_strategy.{service_tier|program_tier}]
frameworks_used: [value-stack-construction, anti-fluff-audit]
confidence: HIGH | MEDIUM | LOW
gap_flags: [list]
---

## Value-stack items (descending by anchor value)
{the populated YAML list from Step 2, post anti-fluff audit, ordered per Step 3}

## Bonus stack
{the populated YAML list from Step 4, post anti-fluff audit}

## Stack visualization (proposal/sales-page block)
{the markdown block from Step 5}

## Anti-fluff audit log
{stripped items + reason for each}

## Verification checklist results
{each of the 10 checks with pass/fail + notes}

## Next actions
{usually: hydrate the offer doc with the value_stack field; run /design-guarantee if not yet built; for program-stack, integrate into the program sales page draft}
```

## Common failure modes

| Failure | Fix |
|---|---|
| Total-to-price ratio below 3.0 | Add real deliverables (not filler) OR re-cite anchors using fresh competitor data. Never pad the ratio with bonuses. |
| Total-to-price ratio above 5.0 | Strip lowest-credibility anchors first. Often: over-priced replacement-cost anchors. Re-cite from primary source. |
| Anchors invented to hit ratio | Strip every anchor without a cited source. Rebuild anchor list from real evidence only. |
| Bonuses are filler ("email support," "lifetime updates") | Anti-fluff audit Step 6 — strip and rebuild bonuses tied to specific objections. |
| Stack labels core deliverables as bonuses | Audit core scope vs. bonus scope — core is what they pay for, bonus is what they get included. Re-categorize. |
| Anchors copied from competitor without verification | Re-cite from primary source (competitor URL with date checked). Stale anchors lose buyer trust. |
| Operator hedges when defending anchors on sales calls | Strip or re-cite. An anchor the operator can't defend won't survive negotiation. |
| Wrong scope written to `company.yaml` | Halt and verify scope declaration; rewrite to correct path. |
| Stack pads past 12 items to look generous | Length ≠ value. After anti-fluff, ship the defensible items even if count drops to 6. |
| Banned vocabulary in item descriptions | Strip per `spec/BANNED-VOCABULARY.md`. Item descriptions are buyer-facing. |

## Cross-skill routing

- **Upstream prerequisites:**
  - `/design-retainer-offer` (for retainer-stack scope) — must produce target offer at ≥50%
  - `/design-program-offer` (for program-stack scope) — must produce target offer at ≥50%
- **Downstream consumers:**
  - `/design-guarantee` — guarantee design references the value stack to size the risk-reversal cost
  - Retainer proposal generation (consumes retainer-stack)
  - Program sales-page generation (consumes program-stack)
  - `/build-offer-ladder` — uses value stacks across tiers to ensure ladder pricing logic holds
  - Sales-call playbooks — operators reference the stack live during pricing conversations
- **Sibling skills:**
  - `/design-retainer-offer` Step 4 produces the v0 stack; this skill deepens it
  - `/design-program-offer` Step 9 references the stack; this skill builds the v1 sales-page version

## Cross-references

- `reference/frameworks/offer/retainer-architecture.md` — Rule 4 (value stack anchors at 3-5× price) is the canonical structural rule this skill operationalizes
- `reference/frameworks/offer/value-stack-construction.md` (Cycle 2 — TBD) — deeper anchor-basis hierarchy + competitive-pricing research protocol
- `reference/templates/retainer-proposal.md` (Cycle 2 — TBD) — proposal template that consumes the retainer-stack
- `reference/templates/program-sales-page.md` (Cycle 2 — TBD) — sales-page template that consumes the program-stack
- `skills/design-retainer-offer/SKILL.md` — upstream skill for retainer-stack scope
- `skills/design-program-offer/SKILL.md` — upstream skill for program-stack scope
- `INVARIANTS.md` N-3, N-4, N-9, N-13, A-2, A-3, A-11 — never invent results, no banned vocab, no "final" claims, methodology descriptors only, profile citation required, output format honored, anonymized descriptors
- `spec/BLIND-OUTPUT-TEST.md` — required for program-stack (sales-page-bound) outputs
- `spec/CONTEXT-THRESHOLDS.md` — `/build-value-stack` threshold table entry
- `spec/BANNED-VOCABULARY.md` — strip from item descriptions before ship

## Examples

See `examples/` (Cycle 2 — TBD):
- `example-retainer-stack-dfy-content-dm.md` — hybrid retainer value stack
- `example-retainer-stack-dfy-content-only.md` — content-only retainer value stack
- `example-program-stack-cohort-v1-founding.md` — founding-cohort program value stack
- `example-program-stack-cohort-v3-mature.md` — mature program value stack with selective performance guarantee

---

*Skill version 1.0.0 — 2026-05-03*
