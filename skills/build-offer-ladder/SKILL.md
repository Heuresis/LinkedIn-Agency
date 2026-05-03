---
name: build-offer-ladder
slug: /build-offer-ladder
agent: offer-architect
department: foundations
output_format: offer-ladder-document
required_profiles:
  product_strategy.service_tier: 70             # core retainer must be designed first
  product_strategy.program_tier: 50             # program tier at least scoped (full design can lag)
allowed_scopes: [agency-ladder]
output_path:
  agency-ladder: company.yaml::product_strategy.offer_ladder
---

# /build-offer-ladder — Hybrid Agency Offer Ladder Architecture

> **Purpose.** Design the agency's full offer ladder — front-door tripwire (or absence of one), core retainer, high-ticket program, and back-end (advisory, partnership, equity-track). The ladder defines how a prospect moves from first-touch to highest-LTV without hitting price-shock walls and without cannibalizing the higher-margin tier with the lower-margin one. For a hybrid B2B LinkedIn agency (DFY retainer + high-ticket program), the ladder is where the two business models stop competing for attention and start feeding each other.
>
> **Bar.** Reading the doc, the operator should answer in one breath: where does a cold lead land first, what does it cost them, what's the next step, who decides, who declines, what's the LTV at each rung, when does a retainer client get pointed to the program (or vice versa), when do they get declined entirely? If any of those is murky, the ladder is wrong — it's a list, not a ladder.

## When to invoke

- Core retainer (`/design-retainer-offer`) ≥70% completeness AND program offer scoped ≥50%
- Operator pivots from DFY-only to hybrid (DFY + program) — ladder redesigned, not extended
- Revenue ceiling traceable to mis-routing (program-fit bought retainer and churned, or vice versa)
- Pricing shift on any tier ≥20% — anchor sequence breaks and needs reset
- New low-ticket / tripwire being added, or back-end (advisory, partnership, white-label) being defined
- Lead-flow maturity crosses pattern threshold (100 or 500 qualified/quarter)

## Required inputs

1. **Service-tier offer ≥70%** in `product_strategy.service_tier` — retainer transformation hypothesis, deliverables, value stack, guarantee, and price band settled.
2. **Program-tier offer ≥50%** in `product_strategy.program_tier` — even if pre-launch, the price + cohort model + core promise must be drafted. A nameless program rung = 1-rung ladder.
3. **LTV + CAC data** per existing tier. Pre-program operators use projected LTV. CAC must be honest (operator hours at loaded rate + paid spend).
4. **Lead-flow snapshot** — inbound leads/month, outbound conversations/month, application submission rate, discovery-call book rate. Ladder design depends on lead-flow maturity.
5. **Existing implicit routing.** What already happens when an inbound lead doesn't qualify for the retainer? When a program grad's agency starts hiring DFY help? Surface before designing explicit routing.
6. **Operator available** for a 60-90 minute design session.

## The design sequence

### Step 1 — LTV mapping per tier (10 min)

Before deciding ladder shape, get honest about what each rung generates over its lifecycle. Without LTV honesty, the operator over-invests in low-LTV tiers that feel busy and under-invests in high-LTV tiers that feel scary.

```yaml
ltv_mapping:
  # For each populated tier capture: avg_price, avg_ltv (inclusive of upsell + renewals + back-end),
  # avg_cac (honest, includes ops hours at loaded rate), ltv_cac_ratio, gross_margin_pct, role.
  free_tier: { role: "audience-builder + intent-classifier", cost_to_serve: "" }
  low_ticket_tier: {}                 # $7-$300 — tripwire / qualifier / cash-flow
  mid_ticket_tier: {}                 # $500-$2K — warm-up to high-ticket
  retainer_tier: {}                   # $5K-$25K/mo, 6-18mo commitment, target margin ≥60%
  program_tier: {}                    # $7K-$15K, 4-12mo cohort, target margin ≥80%
  back_end_tier: {}                   # $25K+ advisory / partnership / equity / white-label
```

The diagnostic that matters most for hybrid agencies: **Is program LTV per student ≥ retainer LTV per client?** If yes, every retainer-qualified prospect who is also program-eligible should route to program *first* (higher margin, less ops drag). If no, the program is mis-priced or mis-scoped — fix before designing the ladder.

### Step 2 — Entry point design (10 min)

The entry point depends entirely on lead-flow maturity. Three patterns; pick the one matching current lead-flow.

- **Pattern A — Direct-to-retainer (<100 qualified leads/quarter).** No tripwire. Free content flows directly to a high-intent CTA: book a discovery call (gated by application). Every lead is precious; a tripwire diverts attention, splits intent, and bleeds operator hours into low-LTV fulfillment. Where 70-80% of <$1M/yr LinkedIn agencies should sit.
- **Pattern B — Tripwire-to-retainer (100-500 qualified/quarter).** Front-door tripwire ($47-$297) at lead-magnet exit. Examples: "DM Operator's Playbook" ($97), "ICP Extraction Workshop + workbook" ($147), "Cold-DM Audit + Sequence Blueprint" ($297). Role: cash-flow + intent qualification + list-build + retargeting. Not the profit center. Converts to discovery-call invite (40-60% upsell typical) → retainer. Requires fulfillment automation so the tripwire eats near-zero operator hours.
- **Pattern C — Retainer-and-program parallel (>500 qualified/quarter, both tiers proven).** Two front doors: B2B founders (retainer) + agency operators (program). Same content engine, different CTAs at the post level. Lead magnets tier-specific. Application forms branch by self-identification. Pattern C only ships at 7-figure scale.

```yaml
entry_point_decision:
  current_lead_flow_qualified_per_quarter: 0
  selected_pattern: "A | B | C"
  rationale: ""
  tripwire_offer:                     # populated only if Pattern B or C w/ tripwire
    name: ""
    price_usd: 0
    fulfillment_mechanism: ""         # automated — Stripe → email → Notion / Skool / Loom
    operator_hours_per_unit_sold: 0   # MUST be near-zero or pattern defeats itself
    upsell_path: ""                   # next-step CTA inside tripwire fulfillment
  graduation_to_retainer_mechanism: ""
```

### Step 3 — Tier-to-tier bridge mechanics (15 min)

Each step up the ladder needs an explicit *bridge* — the mechanism by which a buyer at rung N becomes a buyer at rung N+1. Without explicit bridges, ladders silently collapse to flat product lists.

```yaml
bridges:
  free_to_low_ticket:                 # only if Pattern B — benchmark 3-8% conversion
    bridge_mechanism: ""              # e.g., "Lead-magnet exit-page tripwire offer w/ decay timer"
    expected_conversion_rate_pct: 0
  low_ticket_to_retainer:             # only if Pattern B — benchmark 5-15%, 7-30 day lag typical
    bridge_mechanism: ""              # e.g., "Inside tripwire fulfillment: 'Want us to run this for you?' → application"
    expected_conversion_rate_pct: 0
  retainer_to_program:                # the hybrid-ladder hinge — benchmark 3-8% of eligible
    triggering_event: ""              # e.g., "Retainer client month 4+ asks 'how do I do this in-house?'"
    bridge_mechanism: ""              # e.g., "Operator surfaces program on quarterly review call"
    eligibility_criteria: []          # usually: agency-operator clients, in-house marketers wanting agency-grade ops
    pricing_handling: ""              # e.g., "$2K tuition credit + retainer continues at -20% for cohort length"
  program_to_retainer:                # reverse hinge — benchmark 5-12% of grads
    triggering_event: ""              # e.g., "Grad at 4+ clients wants white-label fulfillment scale"
    bridge_mechanism: ""              # e.g., "White-label retainer track for program graduates"
    pricing_handling: ""              # often: 70-85% of standard retainer
  program_to_back_end:                # benchmark 1-3% of grads — advisory / partnership / equity
    triggering_event: ""              # e.g., "Grad at $50K+/mo with equity-eligible asset"
    bridge_mechanism: ""              # e.g., "Invitation-only, no public CTA"
  retainer_to_back_end:               # rare but high-value
    triggering_event: ""              # e.g., "Retainer client month 12+ with category-leader trajectory"
    bridge_mechanism: ""              # e.g., "Advisory retainer pitch w/ equity component"
```

Bridges are the agency's growth-rate multiplier. Without them, every rung is a separate sales motion. With them, each rung amplifies the next — a $97 tripwire-month becomes a $90K retainer-year becomes a $200K program-cohort spot.

### Step 4 — Tier exclusivity rules (10 min)

The most common ladder-collapse failure: a buyer at rung N+1 *also* buys at rung N (or vice versa) and the operator never decided whether that's allowed, encouraged, or forbidden. Without exclusivity rules, the ladder cannibalizes itself.

```yaml
exclusivity_rules:
  retainer_client_buying_program:
    rule: "allowed | encouraged | bonus-grant | forbidden"
    common_archetype: ""              # e.g., "Retainer client internalizing playbook for in-house team"
    pricing_treatment: ""             # e.g., "Free seat as retainer-loyalty bonus" OR "Standard price w/ retainer-credit"
  program_student_buying_retainer:
    rule: "allowed | encouraged | program-grad-only-pricing | forbidden"
    common_archetype: ""              # e.g., "Grad at $50K/mo buying white-label fulfillment retainer"
    pricing_treatment: ""
  retainer_client_referring_program_candidates:
    rule: "encouraged-with-affiliate | encouraged-no-affiliate | discouraged"
    referral_economics: ""            # e.g., "$1K credit per closed enrollee"
  program_grad_referring_retainer_candidates:
    rule: "encouraged-with-affiliate | encouraged-no-affiliate | discouraged"
    referral_economics: ""
  back_end_exclusivity:
    rule: "back-end excluded from program | back-end-and-program coexist"
```

The healthiest hybrid-agency exclusivity profile is usually:

- **Retainer client → program: bonus-grant** (one free seat as loyalty bonus, capped 1/year). Client gets playbook for in-house team; agency keeps retainer and adds a referral source.
- **Program student → retainer: program-grad-only-pricing** (15-25% discount on standard retainer, grads only). Reverse-LTV capture without cannibalizing standard pricing.
- **Retainer → program referral: encouraged-with-affiliate** ($1-2K credit per closed enrollee). Highest-converting program lead source.
- **Program grad → retainer referral: encouraged-no-affiliate** (referral as brag-asset, not side-hustle).
- **Back-end clients: excluded from program** (dilutes peer-cohort value).

### Step 5 — Pricing-anchor sequence (10 min)

Each rung's price is read by buyers in *relation* to the rung above and below. A jump from $97 → $7,000/mo reads as price shock; $97 → $497 → $1,997 → $7,000/mo reads as natural progression. Design the anchor sequence explicitly.

The **2-4× rung-to-rung rule**: each rung should be 2-4× the previous. <2× and rungs feel redundant. >4× and price shock surfaces.

Proven sequence for hybrid B2B LinkedIn agencies:

| Rung | Typical price | Anchor from previous | Buyer logic |
|---|---|---|---|
| Free | $0 | — | "I'll see if this operator knows what they're talking about." |
| Tripwire | $47-$297 | n/a | "Cheap enough to test their thinking." |
| Mid-ticket | $497-$1,997 | 3-7× tripwire | "Worth a real ticket if it gives me a 30-day plan." |
| Retainer | $5,000-$25,000/mo | 4-10× monthly mid-ticket equivalent | "I'd pay this to have someone competent run this for me." |
| Program | $7,000-$15,000 | 1-2× one month of retainer | "This is what I'd pay to learn this in 90 days." |
| Back-end | $25,000-$100,000+/engagement | 3-10× retainer-monthly or program-tuition | "I'd pay this for a partnership whose name moves my deal flow." |

Note: program is *not* priced 4× retainer — it's priced *near* one-month-retainer. Buyer logic: "cheaper than the agency that would do it for me, AND I keep the playbook." Program above retainer kills cross-tier flow.

```yaml
pricing_anchor_sequence:
  rungs:
    - { rung: free,       price: 0,    multiple_from_prev: "n/a", buyer_logic: "" }
    - { rung: tripwire,   price: 0,    multiple_from_prev: "n/a", buyer_logic: "" }
    - { rung: mid_ticket, price: 0,    multiple_from_prev: 0.0,   buyer_logic: "" }
    - { rung: retainer,   price: 0,    multiple_from_prev: 0.0,   buyer_logic: "" }   # monthly
    - { rung: program,    price: 0,    multiple_from_prev: 0.0,   buyer_logic: "" }   # vs one-month retainer
    - { rung: back_end,   price: 0,    multiple_from_prev: 0.0,   buyer_logic: "" }   # per engagement
  shock_check_passed: true | false        # any rung-to-rung jump >4× = false; redesign
```

### Step 6 — Decline-up, decline-down, decline-out logic (10 min)

The strongest ladders aren't the ones that route every applicant *up*. They're the ones that route to the *right* rung — including down, including out. Without decline logic, applicants land on wrong rungs, churn, and damage the case-study density that fuels every other rung.

```yaml
decline_logic:
  applicant_to_retainer:
    decline_up:                       # too sophisticated → back-end
      - criterion: ""                 # e.g., "$1M+/yr LinkedIn-attributed revenue, looking for partner not vendor"
        route_to: "back-end advisory call"
    decline_down:                     # not yet ready → mid-ticket or program
      - criterion: ""                 # e.g., "Pre-revenue / <$10K/mo — retainer ROI math doesn't work"
        route_to: "program (if agency operator) | mid-ticket workshop (if B2B founder)"
    decline_out:                      # bad fit — no tier suits
      - criterion: ""                 # e.g., "Looking for paid-ad management, not LinkedIn organic"
        route_to: "decline + named referral to specialist"
  applicant_to_program:
    decline_up:                       # too far along → peer-mastermind / back-end
      - criterion: ""                 # e.g., "$50K+/mo agency w/ team — past first-win curriculum"
        route_to: "back-end peer-mastermind invite"
    decline_down:                     # not yet ready → retainer or mid-ticket
      - criterion: ""                 # e.g., "No first paying client yet — program assumes working agency"
        route_to: "mid-ticket 'first-client' workshop"
    decline_out:
      - criterion: ""                 # e.g., "Wants social-media-manager skills, not B2B agency operations"
        route_to: "decline + named referral"
  decline_protocol:
    channel: ""                       # email | DM | call | application-response page
    template_path: "reference/templates/decline-and-route.md"
    operator_review_required_for: []  # which classes need operator sign-off vs auto-decline
```

Decline logic is a foundations-head responsibility per `agents/foundations-head.md`. Hard-DQ applications route to operator per `INVARIANTS.md` A-13 — never algorithmic.

## Output format

```yaml
---
artifact_type: offer-ladder-document
profile_completeness: 0-100
ladder_pattern: "A | B | C"
ltv_diagnostic_passed: true | false      # program LTV ≥ retainer LTV?
shock_check_passed: true | false         # all rung-to-rung jumps ≤4×?
profiles_used: [product_strategy.service_tier, product_strategy.program_tier, ideal_customer_profile.agency_icp, business_context]
frameworks_used: [ltv-mapping, entry-point-pattern-selector, bridge-mechanics, tier-exclusivity-rules, pricing-anchor-sequence, decline-routing]
confidence: HIGH | MEDIUM | LOW
gap_flags: [list]
---

## LTV mapping per tier
{Step 1 YAML + cross-tier LTV diagnostic conclusion}

## Entry point pattern selection
{Pattern A / B / C with rationale + tripwire spec if applicable, from Step 2}

## Bridge mechanics
{Step 3 YAML — every active rung-to-rung path explicit}

## Tier exclusivity rules
{Step 4 YAML}

## Pricing anchor sequence
{rungs table with multiples + buyer logic from Step 5; shock-check status}

## Decline-up / decline-down / decline-out logic
{Step 6 YAML}

## Operator-facing ladder summary
{one-page narrative: how a cold lead moves through this agency to highest LTV — rungs, bridges, exclusions, declines. For operator reference + sales-team brief.}

## Hydrate to company.yaml::product_strategy.offer_ladder
{populated free / low_ticket / mid_ticket / high_ticket / custom arrays}

## Next actions
{usually: update sales-team brief; rewrite application-form decline routing; update sales-page CTAs to entry-point pattern; brief acquisition-head on bridge metrics}
```

## Verification checklist

1. **Every rung is named, priced, and assigned a role.** No rung sits as "we also have X" — each has explicit LTV, role, and bridge logic.
2. **LTV diagnostic passed.** Program LTV per student ≥ retainer LTV per client (or operator has explicit strategic reason it isn't). If program LTV < retainer LTV, kick back to `/design-program-offer` before continuing.
3. **Pattern matches lead-flow.** Pattern B requires ≥100 qualified/quarter + automated tripwire fulfillment. Pattern C requires ≥500 qualified/quarter + both tiers proven. Mismatched pattern bleeds operator hours.
4. **Every bridge has triggering event + mechanism + expected conversion rate.** No "we hope they upgrade" — the trigger is named and the mechanism is operator-or-system-actionable.
5. **Exclusivity rules explicit for every cross-tier pair.** Both buying directions, both referral directions, and back-end posture all stated. No implicit "we'll figure it out."
6. **Shock check passed.** No rung-to-rung jump >4× (with program-near-one-month-retainer exception noted). Failed = redesign anchor sequence.
7. **Decline logic covers up + down + out for both retainer and program.** Decline templates referenced. Operator-review classes flagged per `INVARIANTS.md` A-13.
8. **Margin honesty per tier.** Retainer ≥60%, program ≥80%. Tier below floor re-prices, re-scopes, or comes off the ladder.
9. **Blind Output Test on operator-facing summary** (`reference/canonical/spec/BLIND-OUTPUT-TEST.md`). Show the one-pager to 3 similar-archetype operators. Pass: 2 of 3 say "I see how a cold lead becomes a $200K customer." Fail: ladder reads as a price list.
10. **Hydrated `company.yaml::product_strategy.offer_ladder`** matches the rung structure + pricing from Step 5. No silent drift.

## Common failure modes

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| **Tripwire-without-flow** | $97 tripwire eats operator hours, negligible cash | Pattern B chosen at Pattern A maturity | Kill tripwire; revert to Pattern A; revisit at ≥100 qualified/quarter |
| **Program over-priced vs retainer** | Program LTV < retainer LTV; cross-tier flow stalls | Program priced at "training premium" not "near one-month-retainer" | Re-price program at 1-2× monthly retainer; re-scope value into 4-12 wks |
| **Implicit exclusivity** | Retainer client double-pays into program at standard price, complains | Exclusivity rules not stated | Run Step 4; add program-grad-only-pricing + retainer-loyalty bonus seat |
| **Price-shock collapse** | Lead magnet → $7K/mo retainer, close rate <10% | No mid-ticket or tripwire; >7× jump from previous touch | Add mid-ticket bridge OR longer-warmth nurture |
| **Bridge missing in writing** | "We convert when it feels right" — bridge in head, not doc | Step 3 skipped or vague | Force triggering event + mechanism + rate per bridge |
| **No decline-up logic** | $50K+/mo operator buys program, finds it remedial, churns | Decline-up criteria not designed | Add decline-up-to-back-end criteria + named back-end intake call |
| **No decline-down logic** | Pre-revenue applicant buys retainer, churns at month 3 (ROI math impossible) | Decline-down criteria not designed | Add minimum-revenue floor + named mid-ticket route |
| **Program cannibalizes retainer** | Retainer prospects pivot to program because cheaper | Pricing anchor inverted | Re-position program as operator-skill transfer, not cheaper-DFY |
| **Back-end undefined** | Operator turns down 6-figure advisory ask | Top-of-ladder rung skipped | Define back-end tier (advisory / partnership / equity) — even invite-only |
| **Stale ladder** | Doc unchanged 18 months despite price changes | No quarterly review cadence | Add ladder review to foundations-head quarterly; trigger on ≥20% tier price change |

## Cross-skill routing

- **Upstream invoked from:** `agents/foundations-head.md` (operator + foundations-head review); `/design-retainer-offer` completion; `/design-program-offer` completion; or operator request.
- **Downstream consumes:** `/build-application` (decline routing + exclusivity), `/build-call-prep` (closer brief), `/build-content-engine` (entry-point CTA design), `/build-outbound-engine` (DM sequence routes), `/design-guarantee` (cross-tier exclusivity may shift asymmetry), `reference/templates/retainer-proposal.md`.
- **Triggers re-run when:** tier price ≥20% change, lead-flow crosses pattern threshold, new tier added/removed, LTV diagnostic flips, exclusivity violated. **Cannot invoke without:** retainer ≥70%, program ≥50%.

## Cross-references

- `reference/frameworks/offer/retainer-architecture.md` — retainer rules; ladder is downstream
- `skills/design-retainer-offer/SKILL.md` — the retainer rung
- `skills/design-program-offer/SKILL.md` (Cycle 2 — TBD) — the program rung
- `skills/design-guarantee/SKILL.md` — guarantee mechanics per rung
- `skills/build-application/SKILL.md` — qualification gate consumes decline-routing
- `INVARIANTS.md` A-13 (operator review for hard-DQ), N-3 (no invented results), A-4/A-8 (Blind Output Test, confidence flags)
- `reference/canonical/spec/BLIND-OUTPUT-TEST.md` — verification protocol
- `agents/foundations-head.md` — owns ladder-review cadence; decline routing is foundations-head authority
- `ENCODING.md` Profile 6 — `product_strategy.offer_ladder` schema this skill hydrates

---

*Skill version 1.0.0 — 2026-05-03*
