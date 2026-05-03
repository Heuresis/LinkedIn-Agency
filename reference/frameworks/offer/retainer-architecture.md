# Retainer Architecture

> The structural rules for a B2B LinkedIn retainer offer that closes consistently and survives 6+ months without scope-creep collapse.

## What a retainer offer is

A LinkedIn DFY retainer is a productized monthly service contract where the agency delivers a specified bundle of LinkedIn-related artifacts and operations on a recurring cadence in exchange for a fixed monthly fee.

The retainer offer document is the single source of truth referenced by:
- The retainer proposal sent to prospects
- The contract / SOW
- The onboarding doc
- The monthly client report (which traces deliverables back to the offer)
- Scope-creep adjudication (when "can you also...?" surfaces)

## The 8 structural rules

### Rule 1 — Transformation is named, not implied

Every retainer offer has a one-sentence transformation hypothesis at the top. Specific role + specific pain language → specific desired outcome + specific time horizon + specific measurable metric.

Bad: "We help B2B founders grow on LinkedIn."

Good: "B2B SaaS founders who currently post 1-2x/month with no inbound from LinkedIn will, in 90 days of working with us, generate 8-12 qualified discovery calls per month from LinkedIn alone, as measured by call-bookings tagged 'LinkedIn-sourced.'"

The transformation IS the spine. Everything else is justification.

### Rule 2 — Deliverables are specific and counted

Every deliverable has: a name, a volume, a quality bar, and an honest hours-per-month estimate.

Bad: "LinkedIn content management."

Good: "20 LinkedIn posts per month, written in your voice (validated via voice profile), each scored ≥4 on the 5-axis hook rubric, published per our 4-week editorial calendar."

Counted deliverables prevent ambiguity at month 4 when the client says "I thought we were getting more posts."

### Rule 3 — Scope guardrails are explicit (and on the offer doc)

Every retainer offer includes an explicit "What's not included" section listed in the same prominence as "What's delivered."

Without explicit guardrails, scope creep is silent and margin collapses. With them, scope creep becomes a contract conversation, not a relationship-damage incident.

Common guardrails for this archetype:
- ❌ Custom design/graphics work beyond LinkedIn-native formats
- ❌ Video production / editing beyond audio-only voice notes
- ❌ Email-list management or email campaigns
- ❌ Paid-ad management or budget
- ❌ Client's internal-team training
- ❌ Calls outside scheduled monthly cadence (overflow at $X/hour OR routed to async)
- ❌ Outbound to lists not built by the agency (compliance + voice risk)
- ❌ Cross-platform repurposing (Twitter/X, IG, YT) unless explicitly tier-2 add-on

### Rule 4 — Value stack anchors at 3-5× price

Every retainer is anchored against a value stack that sums to 3-5× the price. Each component cited against a market comparison (in-house cost, agency-market rate, transformation value).

The 3-5× multiplier is the trust band. Below 3× = "why am I paying more than the in-house equivalent?" Above 5× = "this reads as sales-page math; I don't trust the numbers."

Components are real services, not "value-adds." A bullet that reads "Email support during business hours — value $500" is anti-value (it implies the rest is rationed). Strip those.

### Rule 5 — Bonuses are objection-keyed

3-5 bonuses, each named, each valued, each tied to one specific objection from the agency-ICP `objection_patterns` list.

If a bonus doesn't counter a specific objection, it's noise. "30-day money-back" doesn't counter an objection — it's a guarantee component, not a bonus. "Voice extraction interview + validated voice profile" counters the "last ghostwriter never sounded like me" objection — that's a bonus.

### Rule 6 — Guarantee is falsifiable

Every retainer carries a guarantee. The guarantee form scales with the agency's confidence + margin headroom:

- **Performance guarantee** (strongest): "If {metric} not hit by {time}, work for free until it is."
- **Result-or-refund guarantee** (medium): "If {metric} not hit by {time}, refund last 30 days."
- **Process-completion guarantee** (weakest): "If {N} of {N+M} deliverables not shipped at {quality bar} in {time}, next month free."

If the operator can't promise a falsifiable outcome, it's a real strategic question — maybe the agency isn't ready to ship this offer yet. Re-run `/build-icp` and `/design-retainer-offer` with sharper case-study evidence.

### Rule 7 — "Who this is for / not for" sections are mandatory

Every retainer doc includes:
- "Who this is for" — the target avatar in plain language with named criteria
- "Who this is NOT for" — the disqualifier set in plain language

The "not for" section seems like it costs deals. It saves margin. Bad-fit clients sign on optimism, churn at month 3, damage case-study density, and burn operator hours. The "not for" section reduces close rate marginally and raises lifetime gross margin materially.

### Rule 8 — Margin honesty check before launch

Before any new retainer offer goes to market, reconcile:

- (hours per client per month) × (loaded hourly rate of the team that delivers it) × (number of clients the team can handle)
versus
- (price per client per month) × (number of clients the team can handle)

Gross margin must be ≥60% for a sustainable agency offer. ≥70% for a healthy one. Below 60%: the offer either prices up, scopes down, or efficiency-encodes the workflow before launching.

Offers launched below 60% margin appear to work for 6 months and then collapse the agency's cash position when the team scales.

## The retainer document template

```markdown
# {Offer Name}

## What this is
{Transformation hypothesis — Rule 1}

## What changes for you in 90 days
{Measurable outcomes from the hypothesis}

## What we deliver each month
{Deliverables with volumes and quality bars — Rule 2}

## What's not included
{Scope guardrails — Rule 3}

## What this is worth
{Value stack with anchor comparisons — Rule 4}

**Total value: ${sum} / month**
**Investment: ${price} / month** (12-month commitment | 6-month commitment | month-to-month at +25%)

## Bonuses included (one-time, on signing)
{Bonuses with values and objection counters — Rule 5}

## Our guarantee
{The falsifiable promise — Rule 6}

## Who this is for
{Target avatar with named criteria — Rule 7}

## Who this is NOT for
{Disqualifier set — Rule 7}

## How to start
{Specific next step. Application link, not "book a call."}
```

## Failure modes (catalogued)

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| Scope creep | Hours/client trending up monthly | Guardrails missing or weak | Re-run `/design-retainer-offer` with explicit not-included list |
| Margin collapse | Revenue grows but cash gets tighter | Pricing set on intuition, not margin math | Run Rule 8 honesty check; price up or efficiency-encode |
| Generic close rate | Pitches close 15-25% with high effort | Transformation hypothesis is generic | Re-run Rule 1 with specific role + verbatim pain language |
| Bad-fit churn at month 3 | New clients sign optimistically, churn around month 3 | "Not for" section missing or soft | Sharpen disqualifier set; gate pre-pitch via application |
| Guarantee theater | "Satisfaction guaranteed" in the offer doc | Operator afraid to promise outcome | Question whether to ship this offer; if yes, ship Process-completion form, not Performance |

## Cross-references

- `skills/design-retainer-offer/SKILL.md` — the build skill
- `skills/design-program-offer/SKILL.md` — same rules apply with cohort + curriculum-specific overlays
- `skills/build-application/SKILL.md` — gates the offer behind qualification
- `reference/templates/retainer-proposal.md` — the proposal template
- `reference/templates/onboarding-doc.md` — the onboarding template
- `INVARIANTS.md` N-3, N-8, A-5 — never invent results, per-client transformation hypothesis required

---

*Framework version 1.0.0 — 2026-05-03*
