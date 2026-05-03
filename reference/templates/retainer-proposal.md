---
template_name: retainer-proposal
used_by_skill: /design-retainer-offer (proposal generation mode, post-discovery-call)
output_format: client-facing-proposal-doc
audience: prospect (B2B founder / agency operator) post-discovery-call, showed buying intent, has not yet signed
fillable_slots:
  - prospect_first_name
  - prospect_last_name
  - prospect_company
  - prospect_role
  - prospect_company_descriptor          # 1-line: "B2B SaaS infra, Series A, ~25 employees" — anonymized type
  - proposal_date                         # YYYY-MM-DD
  - proposal_expiry_date                  # YYYY-MM-DD — typically date + 14 days
  - operator_name                         # operator-of-record, NOT real personal name in pushed examples
  - account_manager_name                  # AM assigned, anonymized in template examples
  - stated_transformation_verbatim        # pulled verbatim from /summarize-discovery-call
  - stated_transformation_metric          # the specific KPI prospect named
  - stated_transformation_horizon         # the time window prospect named (90 days, 6 months, etc.)
  - current_situation_paragraph           # what they told us, in their words, no embellishment
  - current_situation_quote               # 1-2 sentence verbatim quote from call
  - current_pain_count                    # the specific pain volume (e.g., "1-2 inbound DMs/month from LinkedIn")
  - offer_slug                            # from product_strategy.service_tier.offers[*].offer_slug
  - offer_name                            # public-facing offer name
  - engagement_term_months                # contract term
  - cadence_block                         # what happens weekly/monthly
  - deliverables_block                    # bulleted list of monthly deliverables w/ volumes
  - methodology_name                      # business_context.positioning.named_mechanism.name
  - methodology_one_paragraph             # business_context.positioning.named_mechanism.description
  - methodology_three_pillars             # the 3 mechanism pillars (named, 1-line each)
  - case_study_anchor_1                   # case_studies.agency_case_studies[*] matching vertical/stage
  - case_study_anchor_2                   # second case study (optional but recommended)
  - value_stack_block                     # from /build-value-stack output (6-12 items, anchored)
  - value_stack_total                     # sum
  - value_stack_anchor_ratio              # total / price (must land 3-5x)
  - guarantee_text                        # from /design-guarantee output, verbatim
  - guarantee_form                        # performance | result-or-refund | process-completion
  - investment_monthly                    # USD/month
  - investment_total_term                 # monthly × term
  - payment_terms                         # "monthly auto-debit" | "quarterly upfront" | "month-1 + monthly thereafter"
  - week_1_deliverables                   # what ships week 1
  - week_2_deliverables                   # what ships week 2
  - week_3_deliverables                   # what ships week 3
  - week_4_deliverables                   # what ships week 4
  - ongoing_cadence_paragraph             # what runs from month 2 onward
  - next_step_action                      # single CTA (sign | schedule contract review)
  - next_step_link                        # contract-review booking or signature link
  - operator_signature_block              # signature line (operator + role)
---

# Retainer Proposal Template

## Purpose

This template produces the client-facing proposal document the operator (or AM) sends a prospect after a discovery call where the prospect showed buying intent but did not close on the call. It is the artifact that converts intent into a signed contract. Length: 3-5 pages typical (1,200-2,000 words). Read in 8-12 minutes by the prospect, internalized in one sitting, forwardable to a co-founder or partner without explanation.

The proposal is NOT a sales page. It is a memorialization: this is what we discussed, this is what we propose, this is what it costs, this is the next step. Every section maps back to a moment in the discovery call. The prospect should recognize their own words on every page.

## When operator/AM uses it

- **Primary trigger:** `/summarize-discovery-call` output flags `recommended_next_action: send_proposal` (prospect was Solution-aware or Product-aware on the call, raised pricing or scope-clarification objections only, decision criteria are addressable).
- **Secondary trigger:** prospect explicitly asked at end of call, "send me a proposal."
- **Re-issue trigger:** original proposal expired (past `proposal_expiry_date`) and prospect re-engaged. Re-issue with new dates and any scope/price changes flagged.
- **Do NOT use** when the prospect is Problem-aware or earlier (send a strategic-advisory follow-up, not a proposal — proposal-too-early is a top close-rate killer per N-7).
- **Do NOT use** without a signed application on file (per N-12 — no application = no qualification thread = no proposal).

## The template

```markdown
# Proposal — {offer_name}
## Prepared for {prospect_first_name} {prospect_last_name} — {prospect_company}

**Role:** {prospect_role}
**Date prepared:** {proposal_date}
**Valid through:** {proposal_expiry_date}
**Prepared by:** {operator_name}, {account_manager_name}

---

## 1. What you said you wanted

On our call, you described what you're after in your own words:

> "{stated_transformation_verbatim}"

Concretely, that means: **{stated_transformation_metric}** within **{stated_transformation_horizon}**.

This proposal is built around that single outcome. Every deliverable, every cadence, every piece of the value stack below traces back to it. If at any point you read something that does not connect to that outcome, flag it — it should not be in this document.

---

## 2. Where you are right now

{current_situation_paragraph}

You said it most cleanly here:

> "{current_situation_quote}"

The current baseline we're working from: **{current_pain_count}**. That is the number this engagement moves.

---

## 3. The engagement we propose

**Offer:** {offer_name}
**Term:** {engagement_term_months} months
**Cadence:** {cadence_block}

**What we deliver each month:**

{deliverables_block}

Everything outside this list is out of scope unless we explicitly add it (with associated cost). This protects both sides — you know what you're paying for; we know what we're shipping. Scope clarity is the #1 reason mid-engagement friction kills retainers, and it is the easiest one to prevent.

---

## 4. How it works — {methodology_name}

{methodology_one_paragraph}

The mechanism rests on three pillars:

{methodology_three_pillars}

This is what makes our delivery different from a generic content shop, a generic DM service, or an in-house hire trying to do both. The mechanism is named, repeatable, and the same one that produced the case study below.

---

## 5. What this looks like when it works — case study anchor

{case_study_anchor_1}

{case_study_anchor_2}

These are anonymized to protect the clients. We can walk through unredacted versions on a contract-review call if useful — not in writing, by request.

---

## 6. What this is worth

{value_stack_block}

**Total value: ${value_stack_total} / month**
**Investment: ${investment_monthly} / month** (anchor ratio: {value_stack_anchor_ratio}x)

The value stack is the negotiating floor. If you ever ask for a discount, we don't lower the price — we remove a stack item commensurate with the discount. Every item above is a real deliverable we ship under this offer; nothing in the stack is filler we'd never actually do.

---

## 7. Our guarantee

**{guarantee_form} guarantee:**

{guarantee_text}

Most agencies in this category do not offer a guarantee. We do because the mechanism in section 4 is the same one that produced the case study in section 5. If we cannot reproduce it for you, we owe you something concrete — not a "we tried our best."

---

## 8. The investment

| Line | Amount |
|---|---|
| Monthly retainer | ${investment_monthly} |
| Term | {engagement_term_months} months |
| Total contract value | ${investment_total_term} |
| Payment terms | {payment_terms} |

No setup fees. No surprise overages within scope. Out-of-scope work is quoted in writing before any work starts.

---

## 9. Timeline — first 30 days

| Week | What ships |
|---|---|
| Week 1 | {week_1_deliverables} |
| Week 2 | {week_2_deliverables} |
| Week 3 | {week_3_deliverables} |
| Week 4 | {week_4_deliverables} |

**Month 2 onward:** {ongoing_cadence_paragraph}

---

## 10. Next step

{next_step_action}

{next_step_link}

If you have questions before that step, the fastest path is a 15-minute scope-clarification call — reply to this email and we'll book it within 24 hours.

---

{operator_signature_block}

*Proposal valid through {proposal_expiry_date}. After that date, pricing and availability subject to current capacity.*
```

## Fill-in guidance

### `prospect_first_name`, `prospect_last_name`, `prospect_company`, `prospect_role`
Pull from the application form (per N-12, application is on file). Use the prospect's actual professional name as it appeared on the call — their LinkedIn name. Do NOT shorten ("Mike" if they go by "Michael" on LinkedIn). Do NOT add titles ("Dr.", "Mr.") unless they used them.

**Common mistake:** auto-pulling a casual nickname from a calendar event. Always cross-check against LinkedIn / application.

### `prospect_company_descriptor`
1-line firmographic descriptor used in internal references and in the document if the company name alone is ambiguous. Example: "B2B SaaS infra, Series A, ~25 employees." NOT used in the body of the document — kept in metadata for AM context.

### `stated_transformation_verbatim`
Pulled verbatim from `/summarize-discovery-call` output `stated_transformation` field. **Verbatim means verbatim.** Filler words ("um," "you know") may be removed; substantive phrasing is not edited. If the prospect said "I just want more inbound, like, way more, that doesn't suck my Tuesdays," the quote reads "I just want more inbound — way more — that doesn't suck my Tuesdays."

**Common mistake:** "cleaning up" the quote into agency-grade prose. This destroys the recognition moment in section 1 and the prospect reads section 1 as generic.

**What NOT to put:** an inferred transformation. If the prospect did not state a transformation on the call, send a strategic-advisory follow-up instead. Proposal-without-stated-transformation = N-8 violation.

### `stated_transformation_metric`, `stated_transformation_horizon`
The specific number and timeframe the prospect named. If they said "I want 10 calls a month within 90 days," metric = "10 qualified discovery calls/month," horizon = "90 days." If they hedged ("eventually I'd like to grow LinkedIn"), the prospect is not Solution-aware enough for a proposal — escalate, do not auto-fill.

### `current_situation_paragraph`
3-5 sentences describing where the prospect is today. Pull from `/summarize-discovery-call` output `current_state` field. Use their language, not ours. If they described their current state as "we're getting maybe one inbound a week from LinkedIn, mostly from old posts, nothing systematic," that's the language. Do NOT translate to "limited LinkedIn lead generation infrastructure."

### `current_situation_quote`
1-2 sentence direct quote that captures the pain most cleanly. The single best sentence the prospect said about their current state. Used as the anchor the prospect re-reads on page 1. If `/summarize-discovery-call` did not surface a clean quote, the call was not transcribed cleanly enough for a proposal — request the AM re-run with corrected transcript.

### `offer_slug`, `offer_name`, `engagement_term_months`
Pull from `product_strategy.service_tier.offers[{slug}]` — the canonical offer record produced by `/design-retainer-offer`. Do NOT customize the offer name per prospect; the offer is a productized retainer, not a bespoke service. Customization happens in scope, not in branding.

### `cadence_block`
1-2 sentence summary of weekly + monthly cadence. Example: "Weekly content + DM ops shipping every weekday; monthly strategy call (1st Friday); monthly client report (1st of the month)."

### `deliverables_block`
Bulleted list pulled from `product_strategy.service_tier.offers[{slug}].deliverables_per_month`. Each bullet has the deliverable, the volume, and (optional) the quality bar. Example:
- 20 ghostwritten LinkedIn posts in your voice (4 per pillar mix per `/build-content-engine` output)
- 300 personalized cold DMs (intent-classified per Cold / Warm / Liked-post / Comment-magnet taxonomy)
- 1 monthly strategy call (45 min)
- 1 monthly attribution-clean client report

**Common mistake:** padding with vague items ("ongoing strategic support," "team availability"). These read as filler and weaken the stack. Every bullet is a counted, shippable thing.

### `methodology_name`, `methodology_one_paragraph`, `methodology_three_pillars`
Pull from `business_context.positioning.named_mechanism`. The name is the operator's own 2-4 word mechanism (e.g., "Recursive ICP," "Stratified DM Operations," "Voice-Drift Detection"). If `named_mechanism.name` is empty, the proposal cannot ship — run `/build-positioning` first. A proposal without a named mechanism reads as a generic services pitch and closes accordingly.

The three pillars are 1-line each, with a verb-led structure: "Encode the operator's voice into a profile no AI can fake." NOT three abstract nouns.

### `case_study_anchor_1`, `case_study_anchor_2`
Pulled from `case_studies.agency_case_studies[*]` filtered by vertical match to `prospect_company_descriptor` (or stage match if vertical is rare). Format per case study:
- 1-line client descriptor (anonymized — "a Series A B2B SaaS founder in DevTools," NEVER a real name per A-11)
- 1-paragraph before-state, 1-paragraph what-we-did, 1-paragraph result with cited metrics
- Cite the proof asset path (internal ref, not URL): "proof: case_studies.agency_case_studies[devtools-saas-q3-2025]"

**Hard rule per N-3:** if the case study does not exist in `case_studies.agency_case_studies` with `proof_assets`, it does NOT go in the proposal. No exceptions. Inventing case-study language is the fastest legal-exposure path for the operator.

If only one case study matches, ship one. Two is preferred but not required. Zero matching case studies = the offer should not be pitched into this vertical without the vertical-profile workflow per N-11.

### `value_stack_block`, `value_stack_total`, `value_stack_anchor_ratio`
Pull from `/build-value-stack` output. 6-12 items per `reference/frameworks/offer/value-stack-construction.md` Rule 1. Each item has a deliverable + dollar value + cited anchor (competitor pricing, replacement cost, or transformation value). Anchor ratio must land 3-5x; if outside that band, the value stack is wrong (padded above 5x reads as sales-page math, below 3x reads as overpriced) — return to `/build-value-stack` and recalibrate.

### `guarantee_text`, `guarantee_form`
Pull from `/design-guarantee` output verbatim. Three forms in order of strength: performance (strongest) → result-or-refund (medium) → process-completion (weakest, default). The form chosen must match the case study density: only use performance guarantee if `case_studies.agency_case_studies` has ≥3 entries with the matching outcome. Below that, drop to result-or-refund.

**Common mistake:** softening the guarantee into "satisfaction guaranteed" language. This is not a guarantee per `reference/frameworks/offer/guarantee-strength-tiers.md` — it is decorative copy and weakens trust.

### `investment_monthly`, `investment_total_term`, `payment_terms`
Pull from `product_strategy.service_tier.offers[{slug}].price_point_monthly` and `.contract_term_months`. Do NOT discount the price in the proposal. If the prospect needs a discount, the value stack drops an item — not the price. Payment terms are operator-set; common are "monthly auto-debit" and "Month-1 paid + monthly thereafter."

### `week_1_deliverables` through `week_4_deliverables`
Specific shipping milestones from the onboarding sequence. Pull from `/onboard-client` Step 5 + Step 6 outputs (or the offer's onboarding spec). Example:
- Week 1: kickoff call, voice-extraction interview, ICP build kickoff
- Week 2: client subprofile completion, posting-calendar build, first 3 ghostwritten posts in approval queue
- Week 3: first posts shipped (post #1-3), DM lead-list sourced (200-500 contacts), DM intent classification
- Week 4: posts #4-7 shipped, first 50 DMs sent, first qualified replies, first weekly summary delivered

**Common mistake:** vague timeline ("we'll get started ramping"). Specificity in weeks 1-4 is the trust-building moment. Vague = proposal closes worse.

### `ongoing_cadence_paragraph`
2-3 sentences on what the steady-state month looks like from month 2 onward. Example: "From month 2 onward, weekly cadence holds: 5 posts shipped, ~75 DMs sent, weekly Monday summary, weekly voice-drift audit. Monthly: strategy call 1st Friday, client report delivered 1st of the month, scope review every quarter."

### `next_step_action`, `next_step_link`
Single CTA. Either "Sign the contract here" (with link to contract-signature flow) OR "Book a 30-minute contract-review call" (with link to calendar). NEVER both — choice paralysis. The default is signature link unless the prospect explicitly asked for a contract-review call.

**Common mistake:** offering "any other questions, just email." This is not a CTA, it's a soft exit. The next step is concrete or the proposal underperforms.

### `operator_signature_block`
The operator's name + role (e.g., "Founder & CEO") + agency name. NOT the AM's signature unless the AM is the contract signatory for this client. NOT a date (the date is in the header). NOT a contact phone number (per channel-of-record discipline — channel was set in onboarding, not in proposal).

## Quality bar

Before sending, the proposal passes the Blind Output Test per `spec/BLIND-OUTPUT-TEST.md` against this artifact-specific criterion:

**Pass criterion:** Show the proposal to 2 of 3 simulated reviewers in the prospect's role (B2B founder OR agency operator, matching `agency_icp` segment). Ask: "If you read this proposal after a discovery call you'd taken, would you (a) recognize what you said you wanted in section 1, (b) be able to explain the methodology in section 4 to your co-founder in 30 seconds, (c) understand exactly what you're paying for and what you're not?" Pass = 2 of 3 yes on all three. Fail = identify the gap (usually verbatim drift in section 1, generic methodology in section 4, or padded value stack in section 6) and iterate.

Specific checks:
1. Section 1 contains a verbatim quote from the prospect (not paraphrased)
2. Methodology in section 4 is named (not generic "we use proven frameworks")
3. Case study in section 5 cites a real entry in `case_studies.agency_case_studies` per N-3
4. Value stack ratio lands 3-5x per `reference/frameworks/offer/value-stack-construction.md`
5. Guarantee form matches case-study density per `reference/frameworks/offer/guarantee-strength-tiers.md`
6. Timeline weeks 1-4 are specific (not "we'll begin onboarding")
7. Single CTA in section 10 (not multiple paths)
8. No banned vocabulary per `spec/BANNED-VOCABULARY.md`
9. No real competitor names per N-13 (only methodology descriptors per A-11)
10. Per-prospect transformation hypothesis is named per N-8 (the proposal is not generic)

Fail any check = halt, do not send, route to operator for review.

## Cross-references

- `skills/design-retainer-offer/SKILL.md` — produces the offer the proposal sells
- `skills/summarize-discovery-call/SKILL.md` — produces the verbatim quotes for sections 1-2
- `skills/build-positioning/SKILL.md` — produces the named_mechanism for section 4
- `skills/build-value-stack/SKILL.md` — produces section 6
- `skills/design-guarantee/SKILL.md` — produces section 7
- `skills/onboard-client/SKILL.md` — produces the week 1-4 timeline in section 9
- `reference/frameworks/offer/transformation-hypothesis.md` — section 1 construction logic
- `reference/frameworks/offer/value-stack-construction.md` — section 6 construction logic
- `reference/frameworks/offer/guarantee-strength-tiers.md` — section 7 form selection
- `reference/templates/onboarding-doc.md` — the doc the prospect signs once this proposal converts
- `INVARIANTS.md` N-3 (no invented case studies), N-8 (no pitch without per-client hypothesis), N-11 (no un-encoded vertical), N-12 (no proposal without application), N-13 (no competitor names), A-11 (anonymized descriptors only)
- `spec/BLIND-OUTPUT-TEST.md` — the verification protocol for proposal Quality bar

---

*Template version 1.0.0 — 2026-05-03*
