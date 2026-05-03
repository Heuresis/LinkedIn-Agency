---
name: design-retainer-offer
slug: /design-retainer-offer
agent: offer-architect
department: foundations
output_format: retainer-offer-doc
required_profiles:
  business_context: 50
  ideal_customer_profile.agency_icp: 70
  brand_voice.agency_voice: 60
  case_studies.agency_case_studies: 30   # at least one validated transformation
output_path:
  company.yaml::product_strategy.service_tier.offers
---

# /design-retainer-offer — DFY Service Offer Architecture

> **Purpose.** Build the canonical DFY service offer — what the agency sells, what's in scope, what isn't, what it costs, what the guarantee is, and the value stack that justifies the price. This is the document that gets referenced by every retainer pitch, every proposal, every sales call, every onboarding doc.
>
> **Bar.** Reading this offer doc, a B2B founder in the agency-ICP segment should be able to (a) identify themselves as the target, (b) name the transformation they'd buy, (c) understand exactly what they're paying for and what they're not, (d) hold the operator to a specific guarantee. If any of those is unclear, the offer is wrong.

## When to invoke

- Initial workspace setup (Cycle 1 wedge)
- Major pricing change (≥20% up or down)
- New service tier or new vertical-specific offer
- Margin or close-rate degradation that traces to offer ambiguity
- Client recurring scope-creep (offer guardrails missing or weak)

## Required inputs

1. **Agency-ICP profile ≥70%** (from `/build-icp`). Cannot design an offer without knowing who buys it.
2. **At least one validated case study** in `case_studies.agency_case_studies`. Cannot price without proof.
3. **Operator's pricing intuition** — what they think the price should be, and what range they've actually closed at.
4. **Time-cost data** — hours per client per month for each deliverable, so margin math is honest.
5. **Operator available** for a 60-90 minute design session.

## The design sequence

### Step 1 — The transformation hypothesis (15 min)

Every offer answers one question: *"What changes for the buyer in 90 days?"*

From the agency-ICP profile (Pain → Desire → Buying Triggers), construct a one-sentence transformation:

> "{Agency-ICP role} who currently {pain language} will, in 90 days of working with us, {desire language} as measured by {specific metric}."

Examples (anonymized — descriptors not names):
- "B2B SaaS founders who currently post 1-2 times a month with no inbound from LinkedIn will, in 90 days, generate 8-12 qualified discovery calls per month from LinkedIn alone, as measured by call-bookings tagged 'LinkedIn-sourced.'"
- "Agency operators currently below $20K/month revenue will, in 90 days, sign 2-3 new $5K-$10K retainer clients sourced from LinkedIn DM operations, as measured by signed contracts tagged 'LinkedIn-DM-sourced.'"

The transformation is the offer's spine. Everything below it justifies it.

### Step 2 — Deliverable inventory (15 min)

List every concrete thing the agency produces in a month, per client. Each deliverable gets:

```yaml
- deliverable_name: ""           # Short, specific
  volume: ""                     # "20 LinkedIn posts" — specific number
  quality_bar: ""                # The standard the deliverable must meet
  hours_per_client_per_month: 0  # Honest time cost
  price_attribution_pct: 0       # What % of the monthly retainer this covers (sums to 100% across all)
```

Common deliverables for this archetype:
- **LinkedIn posts** (founder voice or client voice) — typical 12-25/month per client
- **Personalized cold DMs** — typical 200-500/month per client
- **DM sequence management** (replies, qualification, booking) — typical 10-15 hrs/month
- **Lead-list refresh** — typical 1-2/month
- **Discovery call prep docs** — typical 5-10/month
- **Monthly client report** — 1/month
- **Account review call** — 1/month
- **Voice-drift audit** — 1/month (workspace-automated, but the time to interpret + adjust is real)

### Step 3 — Scope guardrails (10 min)

For every deliverable, write the explicit **out-of-scope** list. The single largest cause of agency margin collapse is silent scope creep. Examples:

- ❌ Custom design / graphics work beyond LinkedIn-native formats
- ❌ Video production / editing
- ❌ Email-list management or email campaigns
- ❌ Paid-ad management or budget
- ❌ Client's internal-team training
- ❌ Calls outside scheduled cadence (overflow billed at $X/hour or routed to async)
- ❌ Influencer outreach beyond the lead-list ICP
- ❌ Outbound to lists not built by the agency (compliance + voice risk)

Guardrails are operator-facing AND client-facing — they appear in the proposal and the onboarding doc.

### Step 4 — Value stack construction (10 min)

The value stack anchors the price. Each component of the offer gets a dollar value that sums to **3-5× the actual price**. Each value figure is justified by a concrete comparison (in-house cost, agency-market rate, or transformation value).

```yaml
value_stack:
  - component: "Founder-voice ghostwritten LinkedIn content (20 posts/mo)"
    value_usd: 6000   # vs. a $6K/mo full-time content writer
  - component: "Personalized DM ops (300 DMs + qualification + booking) "
    value_usd: 5000   # vs. a $5K/mo SDR salary equivalent
  - component: "ICP-precise lead-list build + monthly refresh"
    value_usd: 2000   # vs. lead-research agency rate
  - component: "Monthly attribution-clean client report + strategy call"
    value_usd: 1500   # vs. fractional-CMO consult rate
  - component: "Voice-drift audit + recalibration cadence"
    value_usd: 1000   # vs. cost of replacing a churned client
  - component: "Direct access to founder-operator for 6 months"
    value_usd: 3000   # vs. coaching-rate equivalent
# Sum: $18,500. Price: $5,000-$8,000/mo. Anchor ratio: 2.3x-3.7x.
```

### Step 5 — Bonuses (each counters a specific objection) (10 min)

Bonuses solve the last-mile objections. Each bonus is named and tied to one objection from the agency-ICP `objection_patterns` list.

```yaml
bonuses:
  - bonus_name: "30-day pilot DM sprint (200 DMs + intent classification)"
    value_usd: 3000
    objection_countered: "I've been burned by agencies who took 60 days to start delivering — I need proof in week 4"
  - bonus_name: "Voice extraction interview + validated voice profile"
    value_usd: 2500
    objection_countered: "Last ghostwriter never sounded like me — I'm worried this will be the same"
  - bonus_name: "Quarterly retainer review + scope renegotiation right (no fee)"
    value_usd: 1500
    objection_countered: "I don't want to be locked into something that doesn't work after 90 days"
  - bonus_name: "Co-built ICP and disqualifier list before any DMs go out"
    value_usd: 2000
    objection_countered: "I don't want random outreach that damages my brand"
```

3-5 bonuses ideal. Each must be named, valued, and tied to a specific objection. Generic "fast email support" bonuses are anti-bonuses — remove them.

### Step 6 — Guarantee construction (10 min)

The guarantee is a specific, falsifiable promise tied to the transformation. Three forms, in order of strength:

1. **Performance guarantee** (strongest): "If you don't reach {specific metric} in {time period}, we work for free until you do." — requires confidence + margin headroom.
2. **Result-or-refund guarantee** (medium): "If you don't reach {metric} in {time period}, full refund of the last 30 days."
3. **Process-completion guarantee** (weakest, default): "If we don't deliver {N} of {N+M} promised deliverables in {time period} at {quality bar}, we work the next month free."

Most LinkedIn agencies skip guarantees because they're afraid. The one that includes a real guarantee differentiates instantly. Use form 1 if the agency has the case-study density to back it. Form 2 if not. Form 3 only if the operator is genuinely uncertain about results.

### Step 7 — Pricing logic (10 min)

Three anchors:
- **Cost-anchor:** total hours × loaded hourly rate × target margin → minimum viable price.
- **Comparison-anchor:** in-house equivalent (one full-time content writer + one part-time SDR + one fractional strategist) → typically $15K-$25K/mo to assemble = retainer should be ≤ 50% of that.
- **Outcome-anchor:** dollar value of the transformation (revenue from added meetings × close rate × deal size) → retainer should be ≤ 10-20% of the outcome value.

Settle on a price band (low / standard / high). Standard is the default. Low is for early-validation case studies (limited slots). High is for vertical-specific or higher-touch variants.

### Step 8 — Output the offer doc

Hydrate the offer in `product_strategy.service_tier.offers[]`:

```yaml
- offer_slug: "dfy-content-dm-standard"
  offer_name: ""                              # Public-facing, specific
  offer_type: "DFY content + DM ops"
  price_point_monthly: 0
  contract_term_months: 6
  positioning: ""                             # 1-sentence position
  core_promise: ""                            # The transformation hypothesis from Step 1
  unique_mechanism: ""                        # What makes THIS retainer work — names a specific mechanism
  deliverables_per_month: []                  # from Step 2
  scope_guardrails: []                        # from Step 3
  value_stack: []                             # from Step 4
  bonuses: []                                 # from Step 5
  guarantee: ""                               # from Step 6
  target_avatar: ""                           # which agency-ICP segment
  onboarding_protocol_path: "skills/onboard-client/"
```

Plus generate the **client-facing offer document** as markdown for use in proposals and the sales page:

```markdown
# {offer_name}

## What this is
{core_promise — the 1-sentence transformation hypothesis}

## What changes for you in 90 days
{measurable outcomes from the transformation hypothesis}

## What we deliver each month
{deliverables, with volumes and quality bars}

## What's not included
{scope guardrails — explicit out-of-scope items}

## What this is worth
{value stack with anchor comparisons}

**Total value: ${sum} / month**
**Investment: ${price_point_monthly} / month**

## Bonuses included
{bonuses, with values and the objections they counter}

## Our guarantee
{guarantee — the specific, falsifiable promise}

## Who this is for
{target_avatar — the agency-ICP segment with disqualifier reminders}

## Who this is NOT for
{disqualifier set, plain-language version}

## How to start
{specific next step — link to application, not a "book a call" CTA}
```

## Validation gate (mandatory before declaring done)

Two checks:

1. **The "would I pay this?" test.** Show the offer doc to 3 people in the agency-ICP segment (operator's network). Ask: "If you saw this and the price, would you pay it? If yes, what convinced you. If no, what's the gap?" Pass: 2 of 3 say yes. Below pass: identify the gap (usually transformation specificity, value-stack credibility, or guarantee strength) and iterate.

2. **The margin honesty check.** Reconcile Step 2 (hours per client per month) × loaded hourly rate × clients-the-team-can-handle vs. price × clients-the-team-can-handle. Gross margin must be ≥60%. Below 60%, either price up, scope down, or efficiency-encode the workflow.

## Output format

```yaml
---
artifact_type: retainer-offer
profile_completeness: 0-100
design_date: YYYY-MM-DD
validation_gate: passed | failed | not-yet-run
margin_check: passed | failed
profiles_used: [business_context, ideal_customer_profile.agency_icp, brand_voice.agency_voice, case_studies.agency_case_studies]
frameworks_used: [transformation-hypothesis, value-stack-construction, guarantee-strength-tiers]
confidence: HIGH | MEDIUM | LOW
gap_flags: [list]
---

{the populated offer YAML from Step 8}

## Client-facing offer document
{the markdown offer doc from Step 8}

## Validation gate result
{the 3-person test, the margin check}

## Next actions
{usually: `/build-application` to construct the qualification gate, then `/build-call-prep` template}
```

## Failure modes and guards

- **Generic offer copy.** "Done-for-you LinkedIn growth" is not a transformation. The hypothesis must name a specific metric and time horizon.
- **Value stack inflation.** If the value-stack ratio exceeds 5×, B2B buyers smell sales-page math. Stay 3-5×.
- **Weak guarantee.** "Satisfaction guaranteed" is not a guarantee. If the operator can't promise a falsifiable outcome, surface that as a real strategic question — maybe the agency isn't ready to ship this offer yet.
- **No "not for" section.** Removing this loses qualification — bad-fit clients sign and churn at month 3, damaging margin and case-study density. Always include.
- **Skipping margin honesty check.** Pricing on intuition without time-cost reconciliation is the #1 way agencies hit $50K/mo and stall.

## Cross-references

- `reference/frameworks/offer/transformation-hypothesis.md` — the hypothesis-construction protocol
- `reference/frameworks/offer/value-stack-construction.md` — value-anchoring rules
- `reference/frameworks/offer/guarantee-strength-tiers.md` — guarantee form selection logic
- `reference/templates/retainer-proposal.md` — proposal template that consumes this offer
- `reference/templates/onboarding-doc.md` — onboarding template referenced from offer
- `INVARIANTS.md` N-3, N-8 — never invent results, never run pitch without per-client hypothesis

## Examples

See `examples/`:
- `example-dfy-content-dm-standard.md` — standard hybrid retainer
- `example-dfy-content-only-vertical-saas.md` — vertical-specific lighter retainer
- `example-dfy-full-stack-enterprise.md` — high-touch enterprise variant

---

*Skill version 1.0.0 — 2026-05-03*
