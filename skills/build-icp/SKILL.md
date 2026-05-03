---
name: build-icp
slug: /build-icp
agent: icp-builder
department: foundations
output_format: icp-profile-yaml
required_profiles:
  ideal_customer_profile: 0  # zero floor — this skill BUILDS the profile
allowed_scopes:
  - agency-icp               # the agency's own ICP (who the agency sells to)
  - client-icp               # a client's ICP (who the client sells to) — recursive
output_path:
  agency-icp: company.yaml::ideal_customer_profile.agency_icp
  client-icp: company.yaml::ideal_customer_profile.client_icps[{client_slug}]
---

# /build-icp — Ideal Customer Profile Build

> **Purpose.** Build an elite-tier ICP (≥75% completeness) for either the agency or for a specific client. ICP is the heaviest-weighted profile (20%) because every downstream conversion artifact — every post, every DM, every call-prep doc, every retainer pitch — calibrates to it. Wrong ICP = wasted everything downstream.
>
> **Bar.** The ICP must be specific enough that a reader can name 5 real companies that fit and 5 real companies that look like fit but don't. If it can't, it's too broad.

## When to invoke

- New operator onboarding (build `agency-icp`)
- New client signed → during onboarding (build `client-icp` for that client_slug)
- Performance signal: discovery call close-rate <25%, or post engagement is high but inbound calls don't convert → ICP refinement
- Vertical pivot or new segment expansion (build a new ICP for the new segment)

## Required inputs

1. **Either:** 10-20 closed-won deals (with company names, roles, deal sizes, time-to-close, what they said on the call) — preferred path.
2. **Or:** 3-5 hypothesis customers + the operator's pattern-matching from prior client work — fallback path.
3. **5-10 closed-lost deals** with reasons (the contrast set).
4. **3-5 sales call recordings or DM thread transcripts** (verbatim language source).
5. **Operator available for 60-90 minute interview.**

If only fallback path is available, mark output `icp_status: drafted` (not `validated`). Re-run after first 5 closed deals.

## The build sequence

### Step 1 — Closed-won pattern extraction (15 min)

Across the closed-won deals, extract patterns on every dimension. Don't generalize prematurely — list specifics.

| Dimension | Per-deal data | Pattern (only if 7+ of 10 share it) |
|---|---|---|
| Role | "Founder", "Head of Demand Gen", ... | |
| Company stage | "Solo + 2 contractors", "20-person", ... | |
| Revenue stage | "$1.2M ARR", "$8M ARR", ... | |
| Industry | "B2B SaaS / DevOps", "B2B Services / agency", ... | |
| Trigger event | "Last agency churned", "In-house writer quit", "Just raised", ... | |
| Decision-making | "Solo decision", "Co-founder consult", "VP Sales sign-off", ... | |
| Sales motion | "Inside sales", "Founder-led", "Channel-led" | |
| Time-to-close | "12 days", "47 days", ... | |

Patterns become the ICP firmographics + behavioral patterns. Outliers become the "looks like fit but isn't" set.

### Step 2 — Closed-lost contrast (10 min)

For the 5-10 closed-lost deals, ask: which dimension above did they fail? They looked right on dimensions 1-N but were actually disqualified on dimension X.

This becomes the **disqualifier set** — the explicit hard-DQ criteria the application form will gate on.

### Step 3 — Voice-of-customer extraction (20 min)

From the 3-5 sales call transcripts and DM threads, extract verbatim language for:

- **`pain_language_patterns`** — how the ICP describes their pain in their own words. Not "they need more leads" — verbatim: *"I'm tired of paying $4K/month for a content operator who needs me to send them every idea."*
- **`desire_language_patterns`** — how they describe what they want. Verbatim.
- **`objection_patterns`** — how they raise concerns. Verbatim, not paraphrased.

Verbatim language is what makes Voice 2 and Content Strategy 4 sing. Paraphrased language produces generic posts.

### Step 4 — The 12-question ICP interview (40 min)

Run this with the operator. Each question is engineered to surface a specific compartment dimension.

1. **"Describe your last 3 closed-won clients in 1 sentence each — by what they were *trying to do* when they hired you, not what they bought."** (psychographic desires)
2. **"Of those 3, what was the moment in the prior 90 days that made them think 'I need to fix this now'?"** (buying triggers)
3. **"What's the one number on their dashboard that, if it doesn't move in 60 days, makes them fire you?"** (success metric they actually watch)
4. **"What did each of them try BEFORE you that didn't work, and why didn't it?"** (solution awareness, prior agency baggage)
5. **"What's the smartest thing they each said on the discovery call that told you they were the right fit?"** (signal of qualified leads)
6. **"What's the dumbest objection you keep hearing from people who DON'T close?"** (signal of disqualifiers)
7. **"What does an ideal-fit lead's LinkedIn profile look like? Headline, company size, recent posts, who they engage with?"** (firmographic + research-behavior signal)
8. **"Who do they currently follow on LinkedIn? Whose content do they comment on?"** (research behavior, where to find them)
9. **"What's a phrase they use that nobody outside this segment uses?"** (verbatim language tag)
10. **"What's their dominant fear about working with another agency?"** (limiting belief, dominant_belief)
11. **"What does their internal Slack look like the week before they sign you?"** (decision-making process, who's involved)
12. **"What's the one thing they would have changed about how you sold them, and why didn't they say it?"** (silent objection patterns)

### Step 5 — Limiting belief diagnosis (5 min)

From Q4, Q5, Q6, Q10 answers, classify the dominant belief:

- **Helpless** ("I don't know how to do this myself"): the ICP needs `capability` transformation. Content + DMs lean toward demonstrating-the-mechanism + showing-how.
- **Hopeless** ("I've tried everything"): the ICP needs `safety` transformation. Content + DMs lean toward proof + risk-reversal + named-mechanism.
- **Worthless** ("I'm not the kind of person whose posts go viral"): the ICP needs `status` transformation. Content + DMs lean toward identity-shift + positioning + before-after.

For B2B founders considering LinkedIn agencies, the typical mix is **Helpless 40% + Hopeless 50% + Worthless 10%**. But validate per-ICP — segments differ.

### Step 6 — Output the profile

Hydrate the YAML schema (`ideal_customer_profile.agency_icp` for agency, or `ideal_customer_profile.client_icps[slug]` for client):

```yaml
{scope_path}:
  demographics: { age_range, gender_split, geography, income_range, education }
  firmographics: { role, company_stage, revenue_stage, industry, sales_motion }
  psychographics:
    pain_points: []                    # 7-12 verbatim from Step 3
    desires: []                        # 7-12 verbatim from Step 3
    fears: []                          # 5-7 from Q10
    aspirations: []                    # 5-7 from Q1, Q3
    beliefs: []                        # what they currently believe (especially what's wrong)
    objections: []                     # min 12 from Q6 + Step 3
  behavioral_patterns:
    buying_triggers: []                # from Q2 — specific events
    decision_making_process: ""        # from Q11
    research_behavior: []              # from Q7, Q8
    trust_signals_required: []         # from Q5, Q12
  market_sophistication:
    awareness_level: ""                # from Q4 — what they've already tried tells you
    solution_awareness: ""             # Naive | Aware | Skeptical | Exhausted (LinkedIn-services market is Skeptical→Exhausted by default)
    market_maturity: "Mature"
    competitive_density: "High"
  voice_of_customer:
    actual_customer_language: []       # verbatim from Step 3
    pain_language_patterns: []
    desire_language_patterns: []
    objection_patterns: []
  limiting_belief_diagnosis:
    dominant_belief: ""                # Helpless | Hopeless | Worthless from Step 5
```

For client-icp scope, also populate:
```yaml
content_resonance_evidence: []         # which client posts in this ICP performed best
dm_reply_evidence: []                  # which DM threads to this ICP replied at high rate
```

## Specificity gate (mandatory before declaring done)

The ICP fails if it could describe a generic "B2B founder" or "B2B service business owner." It passes if:

1. **Name 5 real companies that fit.** Operator can name them in <60 seconds without straining.
2. **Name 5 real companies that look like fit but aren't.** Operator can name and explain the disqualifier in <90 seconds.
3. **Read 3 random posts from the operator's content.** For each, predict whether this ICP would engage. Then check actual engagement on those 3 posts. Prediction must match reality on 2 of 3.

If specificity gate fails: re-run Step 2 (closed-lost contrast) and Step 6 with sharper firmographics.

## Output format

```yaml
---
artifact_type: icp-profile
scope: agency-icp | client-icp
client_slug: null | "<slug>"
profile_completeness: 0-100
build_date: YYYY-MM-DD
specificity_gate: passed | failed | not-yet-run
icp_status: drafted | validated | proven-by-results
profiles_used: [ideal_customer_profile]
frameworks_used: [icp-12q, voice-of-customer-extraction, limiting-belief-triad]
confidence: HIGH | MEDIUM | LOW
gap_flags: [list]
---

{the populated YAML profile from Step 6}

## Specificity gate result
{the 5 fits, the 5 lookalike-non-fits, the 3-post engagement-prediction check}

## Disqualifier set
{the explicit DQ criteria from Step 2 — these feed `/build-application`}

## Next actions
{what to invoke next: typically `/build-content-engine` or `/build-outbound-engine`}
```

## Failure modes and guards

- **The "professional B2B decision-maker" trap.** If the output reads like a marketing-deck slide, it failed. Re-run with sharper trigger-event focus.
- **Confusing the agency-ICP with the client-of-client-ICP.** The agency-ICP is who the agency sells *to* (B2B founders who hire LinkedIn agencies). The client-of-client-ICP is who *the client* sells to (B2B SaaS buyers, etc). Both need separate builds. Skill scope is set at invocation.
- **Skipping voice-of-customer extraction.** Tempting because Step 3 is interview-heavy. But verbatim language is the difference between content that converts and content that bounces. Required.
- **Calibrating to a single closed-won deal.** Patterns require 7+ of 10. If the operator only has 3-5 closed deals, mark `icp_status: drafted` and require re-validation after deal 10.

## Cross-references

- `reference/frameworks/foundations/icp-12q.md` — full interview rationale per question
- `reference/frameworks/foundations/voice-of-customer-extraction.md` — verbatim-extraction protocol
- `reference/frameworks/foundations/limiting-belief-triad.md` — Helpless / Hopeless / Worthless framework
- `reference/templates/icp-build-interview.md` — operator-facing template
- `INVARIANTS.md` A-5 — dependency chain (ICP precedes Voice precedes Content)
- `skills/build-application/SKILL.md` — consumes the disqualifier set this skill produces
- `skills/build-outbound-engine/SKILL.md` — consumes voice-of-customer for DM construction

## Examples

See `examples/`:
- `example-agency-icp-hybrid-agency.md` — agency selling DFY + program to other agency operators
- `example-client-icp-b2b-saas-founder.md` — client of the agency: B2B SaaS founder
- `example-client-icp-b2b-services-founder.md` — client of the agency: B2B services founder

---

*Skill version 1.0.0 — 2026-05-03*
