---
name: build-positioning
slug: /build-positioning
agent: foundations-head
department: foundations
output_format: positioning-statement
required_profiles:
  business_context: 50
  ideal_customer_profile.agency_icp: 70
  brand_voice.agency_voice: 60
allowed_scopes:
  - agency-positioning
output_path:
  agency-positioning: company.yaml::business_context.positioning
---

# /build-positioning — Agency Positioning Statement

> **Purpose.** Build the LinkedIn agency's positioning statement — the single sentence + supporting frame that answers *for whom*, *against whom*, *with what mechanism*, *with what proof*. This is the load-bearing string the website hero, the LinkedIn bio, the retainer-proposal opening, the discovery-call frame, and every cold DM's compression all derive from. If positioning drifts, every downstream surface drifts with it.
>
> **Bar.** A B2B founder reading the positioning sentence in 5 seconds knows (a) whether they are the target, (b) why this agency is different from the three others they're comparing against, and (c) what specifically will be done for them. If the sentence could be lifted into another LinkedIn agency's website with two word swaps, it failed.

## When to invoke

- Cycle 1 onboarding — after `/build-icp` (agency-icp) hits ≥70% and `/extract-founder-voice` hits ≥60%
- New offer launch — positioning audit triggered when `product_strategy.service_tier` adds an offer (per `agents/foundations-head.md` failure-mode "positioning drift via offer drift")
- Vertical pivot or segment expansion — agency moves from horizontal to vertical specialization, or adds a second vertical
- Performance signal: discovery-call close rate drops below 25% AND the operator hears "I wasn't sure exactly what you do" or "you sound like the other three I talked to" on closed-lost calls
- Competitor compression — a new player enters the category with overlapping positioning; existing positioning needs sharpening

## Required inputs

1. **`business_context` ≥ 50%** — agency name, model, revenue tier, current category descriptor, current USP attempt, distribution channels, current competitor list (descriptors only per `INVARIANTS.md` N-13)
2. **`ideal_customer_profile.agency_icp` ≥ 70%** — must include verbatim `voice_of_customer.pain_language_patterns`, `voice_of_customer.desire_language_patterns`, `objection_patterns`, and `limiting_belief_diagnosis.dominant_belief`. Without these, positioning slides into generic "we help B2B founders win on LinkedIn"
3. **`brand_voice.agency_voice` ≥ 60%** — `tone_framework`, `persuasion_style`, `authority_positioning`, `phrases_to_use`, `phrases_to_avoid`. Positioning must sound like the operator, not like a marketing-deck slide
4. **3-5 anonymized case-study summaries** from `case_studies.agency_case_studies` (need at least 1 validated transformation with proof anchors per `INVARIANTS.md` N-3)
5. **Operator available for 60-75 minute interview** (Step 1 + Step 5 + Step 6 require their voice)
6. **The 3-screenshot competitive scan:** operator pulls 3 screenshots — competitor-agency website headers — without naming them in artifacts (descriptor-only per `INVARIANTS.md` A-11)

If `ideal_customer_profile.agency_icp` is below 70%, halt and route to `/build-icp` first. Positioning built on a 50% ICP becomes positioning aimed at a phantom.

## The build sequence

### Step 1 — Target avatar lock (10 min)

Pull the agency ICP and lock the *primary avatar* into one sentence using only verbatim language from `voice_of_customer`. The avatar must collapse role + revenue stage + buying trigger + dominant pain into one tight sentence.

Template:
> "A {role} at a {revenue_stage} {industry_descriptor} who is {buying_trigger} and is {dominant_pain_in_verbatim_language}."

Examples (avatar-lock outputs, not real operators):
- *"A founder at a $1M-$10M B2B SaaS company who just watched their last LinkedIn agency churn after 4 months and is tired of paying $4K/month for content their prospects don't read."*
- *"A solo agency operator at $30K-$80K/month who is trapped in fulfillment and has tried two cohorts that taught theory but didn't move their pipeline."*

The avatar lock is the input to every other step. If the operator can't name the avatar in <30 seconds without notes, halt and re-run the ICP-narrowing step from `/build-icp` Step 6.

### Step 2 — Competitor frame (15 min)

Position is *relative*. Without a named anti-position, the statement floats. Build the **3-tier competitor frame** — methodology descriptors only, no real names per `INVARIANTS.md` N-13.

| Tier | Descriptor | What they offer | Why the avatar churned from them | Anti-position lever |
|---|---|---|---|---|
| **The commodity tier** | "the volume-first agencies" | Mass DM outreach, content templates, $1.5K-$3K/month, junior account execs | "Felt like a number, content sounded like everyone else's, no senior eyes on the work" | Quality + senior involvement |
| **The boutique tier** | "the founder-led ghostwriting boutiques" | High-touch ghostwriting only, $5K-$12K/month, 1-2 person shops | "Great content but no DM ops, no pipeline accountability, hard to scale beyond founder voice" | Full-stack + measurable pipeline |
| **The solo tier** | "the in-house ghostwriter or solo freelancer" | One person, $2K-$6K/month, narrow scope | "Single point of failure, no system, drift when they get sick or take a vacation" | System + redundancy + SOPs |

For each tier, write the **anti-position lever** — the one dimension on which the agency is structurally superior, not aspirationally superior. Structural means: built into the operating model, provable from case studies, not a claim that requires trust.

The agency's positioning sentence must implicitly reject all three tiers without naming them.

### Step 3 — Mechanism naming (15 min)

A positioning statement without a **named mechanism** is just a value proposition. Every elite agency in this archetype operates a signature method that gets a name — not a description of the method, the *name* of the method.

Mechanism naming protocol:

1. **Extract the operator's actual delivery sequence.** What is the proprietary 4-7 step sequence the agency runs for every client? Pull from `product_strategy.service_tier.offers[*].unique_mechanism` if populated; if not, interview the operator: "Walk me through what your agency does for a new client in their first 30 days that no other agency does in the same way."

2. **Distill to a 2-4 word name.** The name must be:
   - **Memorable** — passes the "could a client say it on a discovery call from memory" test
   - **Descriptive** — implies what it does, not just a vibe word
   - **Specific to this archetype** — could not be lifted by a fitness coach or e-commerce agency
   - **Trademark-clean** — the operator can search and confirm no active trademark conflict
   - **Not a banned vocabulary item** per `spec/BANNED-VOCABULARY.md` (no "Method," "Blueprint," "Framework" alone — must be paired with a specific noun)

3. **Test the name with the avatar.** Read the avatar lock from Step 1. Say the mechanism name to a stand-in for that avatar. They should react with curiosity, not confusion. If they say "what is that?" with interest = pass. If they say "what is that?" with confusion = fail.

4. **Write the 1-paragraph mechanism description** — what the mechanism is, why it exists, what it produces. This becomes the website's "How it works" section seed and the discovery-call frame.

If the operator resists naming the mechanism ("it just feels gimmicky"), surface the cost: positioning without a named mechanism collapses into "we help founders with LinkedIn" — exactly the commodity-tier compression they're trying to escape. Per `agents/foundations-head.md` decision authority, this is a foundations-head call, not a stylistic preference.

### Step 4 — Proof anchor selection (10 min)

The positioning statement carries an implicit promise. The **proof anchor** is the case-study density that makes the promise believable in 5 seconds.

Pull 3-5 cases from `case_studies.agency_case_studies` where:
- `use_in_sales: true`
- The transformation is in the **same vertical or motion** as the avatar from Step 1
- The numbers in `starting_state` → `ending_state` are concrete (followers, calls/month, revenue from LinkedIn)
- Proof assets exist (`proof_assets` paths populated)

Pick the **single tightest case** as the positioning's anchor. Tightest means: shortest time horizon × largest delta × cleanest attribution. This case becomes the proof phrase that lives one click below the positioning sentence on the website and at the top of the retainer proposal.

Format the proof phrase:

> "We took a {vertical} {role} from {starting_state metric} to {ending_state metric} in {time_horizon_months} months."

If no case meets the bar (no `use_in_sales: true` in the avatar's vertical), positioning ships with a **provisional proof tier** — operator's prior credentials + first-hand mechanism explanation — and is flagged for re-anchor as soon as the first vertical-matched case lands. Do not invent numbers per `INVARIANTS.md` N-3.

### Step 5 — Positioning statement draft (15 min)

The positioning statement uses a **4-clause template** — for-whom + against-whom + named-mechanism + proof-anchored outcome. Each clause is load-bearing.

Master template:
> "{Agency name} is the {category descriptor} for {avatar from Step 1}. Unlike {competitor-tier descriptor from Step 2}, we run {named mechanism from Step 3} — the same system that {proof phrase from Step 4}."

Build **three variants** at different lengths:

```yaml
positioning_variants:
  one_sentence_master:
    text: ""                              # the master 4-clause sentence (35-55 words)
    surfaces: [website-hero, retainer-proposal-opening, discovery-call-frame]
  one_line_compressed:
    text: ""                              # 12-18 words, drops competitor clause
    surfaces: [linkedin-bio, email-signature, podcast-intro]
  two_word_category:
    text: ""                              # the category descriptor alone (3-5 words)
    surfaces: [linkedin-headline-suffix, business-card, conference-name-tag]
```

The three variants must compress *consistently* — the one-line and the two-word forms are extractions from the master, not separate inventions. If they conflict, the master is wrong.

### Step 6 — Test the positioning (15 min)

Run the **5-question avatar test** against the master sentence. The operator role-plays the avatar; positioning fails if any question gets a non-passing answer.

| Question (avatar voice) | Pass criterion |
|---|---|
| 1. "Who is this for?" | Avatar names themselves within 5 seconds without re-reading |
| 2. "How is this different from {competitor tier}?" | Avatar names the structural difference (not the aspirational claim) within 10 seconds |
| 3. "What is {named mechanism}?" | Avatar can paraphrase the mechanism in their own words after one reading; doesn't have to be exact |
| 4. "Has this worked for someone like me?" | Avatar points to the proof anchor; the proof matches their vertical/motion |
| 5. "Why should I trust this isn't another {commodity-tier descriptor}?" | Positioning + proof + mechanism together produce trust; if not, mechanism naming is too vague or proof anchor is too weak |

If 2+ questions fail, return to the failed step (Q1→Step 1, Q2→Step 2, Q3→Step 3, Q4→Step 4, Q5→Step 3 or Step 4). Do not declare done with failures.

### Step 7 — Surface adaptations (5 min)

The positioning statement does not ship raw to every surface. It adapts. Build the two highest-leverage adaptations now:

**LinkedIn bio adaptation** (220 char max for headline + ~2,000 char for About):
- Headline: `{role-the-operator-claims} | {two-word category descriptor from Step 5} | {single proof phrase compressed}`
- About first 3 lines (above the fold): hook + one-line compressed positioning + named mechanism reference
- About body: full master positioning + 3 case-study summaries + soft CTA to lead magnet or DM

**Retainer-proposal opening adaptation** (the first 200 words of the proposal):
- Line 1: avatar-mirroring sentence (use verbatim pain language from `voice_of_customer.pain_language_patterns`)
- Line 2-3: master positioning sentence
- Line 4-5: named mechanism + 1-paragraph mechanism description from Step 3
- Line 6-8: proof anchor case + measurable outcome from Step 4
- Line 9: bridge to the offer scope ("Here's what we'd do for {prospect company name} ...")

Both adaptations cite-back to the master positioning. Edits to the master cascade automatically — operator must not edit a surface adaptation in isolation per `agents/foundations-head.md` failure-mode guards.

## Output format

```yaml
---
artifact_type: positioning-statement
scope: agency-positioning
profile_completeness:
  business_context: 0-100
  ideal_customer_profile.agency_icp: 0-100
  brand_voice.agency_voice: 0-100
build_date: YYYY-MM-DD
avatar_test_result: passed | failed | partial
proof_anchor_status: vertical-matched | provisional | absent
profiles_used: [business_context, ideal_customer_profile.agency_icp, brand_voice.agency_voice, case_studies.agency_case_studies]
frameworks_used: [4-clause-positioning, 3-tier-competitor-frame, mechanism-naming-protocol, 5-question-avatar-test]
confidence: HIGH | MEDIUM | LOW
gap_flags: [list]
---

## Positioning YAML (writes to business_context.positioning)

positioning:
  category_positioning: ""                 # the two-word category descriptor (Step 5 variant 3)
  unique_selling_proposition: ""           # the master 4-clause sentence (Step 5 variant 1)
  one_line_compressed: ""                  # the LinkedIn-bio-friendly form (Step 5 variant 2)
  named_mechanism:
    name: ""                               # 2-4 word name (Step 3)
    description: ""                        # 1-paragraph description
    sequence_summary: []                   # the 4-7 step delivery sequence
  competitor_frame:
    commodity_tier_descriptor: ""
    boutique_tier_descriptor: ""
    solo_tier_descriptor: ""
    anti_position_levers: []               # 3 structural differentiators
  proof_anchor:
    case_slug: ""                          # references case_studies.agency_case_studies[*]
    proof_phrase: ""                       # the 1-line proof from Step 4
    proof_assets_paths: []
  competitive_advantages: []               # 5-7 from the build, each with evidence

## Positioning narrative (1-page brief)
{the master sentence + the why-this-works narrative + how it derives from ICP voice-of-customer + how it rejects each competitor tier}

## LinkedIn bio adaptation
headline: ""
about_first_3_lines: ""
about_body: ""

## Retainer-proposal opening adaptation
{the 9-line opening from Step 7}

## Avatar test result
{question-by-question pass/fail with operator's role-played responses}

## Next actions
{usually: /build-content-engine to align pillars to positioning, /design-retainer-offer to align offer to positioning, /extract-founder-voice if voice ≤80% blocks bio adaptation polish}
```

## Verification checklist

1. **Avatar specificity gate.** Master sentence names the avatar with role + revenue stage + buying trigger. If any of the three is absent, fail.
2. **Competitor frame complete.** All 3 tiers have descriptors + anti-position levers. No real names per `INVARIANTS.md` N-13.
3. **Named mechanism passes name-test.** 2-4 words, memorable, descriptive, trademark-clean, not in `spec/BANNED-VOCABULARY.md`.
4. **Proof anchor exists or is explicitly flagged provisional.** No invented numbers per `INVARIANTS.md` N-3.
5. **Three variants compress consistently.** One-line and two-word forms are extractions from the master, not separate inventions.
6. **5-question avatar test ≥ 4 of 5 pass.** Below 4 of 5 = halt, return to failed step.
7. **LinkedIn bio + retainer-proposal opening both written and consistent with master.** Per `agents/foundations-head.md` failure-mode "positioning drift via offer drift."
8. **Voice match ≥80%** — the master sentence uses `phrases_to_use` patterns and avoids `phrases_to_avoid` from `brand_voice.agency_voice`.
9. **Banned vocabulary check passed** per `spec/BANNED-VOCABULARY.md`. No "game-changer," "synergize," "10X," "next-level."
10. **Blind Output Test run** per `spec/BLIND-OUTPUT-TEST.md` — would 2 of 3 simulated B2B founders matching the avatar say "this came from an agency that knows my world"? Required since the positioning ships to client-facing surfaces (website, retainer proposals).

## Common failure modes

| Failure | Fix |
|---|---|
| Positioning sounds like a marketing-deck slide ("transforming B2B leaders through authentic content") | Re-run Step 1 with verbatim ICP voice-of-customer language. Slide-language = no `voice_of_customer` hydration. |
| Mechanism name is vague ("the LinkedIn Authority Method") | Re-run Step 3 — name must be specific to the archetype, not liftable to any agency. Avoid "Method/Blueprint/Framework" alone. |
| Master sentence works, but LinkedIn bio still sounds generic | Surface adaptation (Step 7) was skipped or done by the operator outside the cascade. Re-derive bio from master, not from scratch. |
| Proof anchor is in wrong vertical | Re-run Step 4 with vertical-matched case. If none exists, mark `proof_anchor_status: provisional` and flag for re-anchor at next vertical-matched closed case. |
| Operator says "I don't want to name competitors" | Methodology descriptors only per `INVARIANTS.md` N-13. The competitor frame is structural, not name-based. Re-explain. |
| Positioning passes the test in private but operator drifts back to old phrasing on calls | Built but not internalized. Schedule the 30-day positioning-rehearsal protocol (operator delivers the master sentence on next 10 discovery calls verbatim before allowed to riff). |
| 5-question avatar test gets 3 of 5 pass | Halt. Failures concentrated on Q3 → Step 3. Failures on Q4 → Step 4. Failures on Q5 → both. Do not declare done. |

## Cross-skill routing

- **Feeds into:** `/design-retainer-offer` (offer must align with positioning's category + named mechanism), `/build-content-engine` (pillars must reinforce positioning's avatar + mechanism), `/build-application` (qualification logic gates by avatar fit), `/build-call-prep` (call frame inherits positioning's master sentence and proof anchor)
- **Consumes:** `/build-icp` (agency-icp ≥70%), `/extract-founder-voice` (agency-voice ≥60%), `/define-mission` (mission supplies the durable why; positioning supplies the market-facing what — they must not contradict)
- **Triggers re-run when:** new offer added (`product_strategy.service_tier` mutation), vertical pivot (ICP shift), close rate drops below 25%, competitor compression detected by `/strategic-advisory`
- **Owned by:** `agents/foundations-head.md` per skills_owned declaration

## Cross-references

- `agents/foundations-head.md` — owning agent + decision authority + failure-mode guards
- `INVARIANTS.md` N-3 (no invented results), N-13 (no competitor names), A-1 (Three-Lens Audit precedes generation), A-2 (cite profiles), A-11 (anonymized descriptors)
- `ENCODING.md` Profile 1 (business_context.positioning), Profile 3 (ideal_customer_profile.agency_icp), Profile 2 (brand_voice.agency_voice), Profile 5 (case_studies)
- `spec/CONTEXT-THRESHOLDS.md` — `/build-positioning` row (business_context 50, agency_icp 70, agency_voice 60)
- `spec/BANNED-VOCABULARY.md` — phrase filter for the master sentence
- `spec/BLIND-OUTPUT-TEST.md` — required pre-ship test for client-facing surfaces
- `skills/build-icp/SKILL.md` — supplies avatar + voice-of-customer
- `skills/extract-founder-voice/SKILL.md` — supplies tone, persuasion style, phrases_to_use
- `skills/define-mission/SKILL.md` — supplies the operator-facing why; positioning is its market-facing complement
- `skills/design-retainer-offer/SKILL.md` — consumes positioning's named mechanism for offer architecture
- `reference/frameworks/foundations/4-clause-positioning.md` — (Cycle 2 — TBD) full template rationale
- `reference/frameworks/foundations/mechanism-naming-protocol.md` — (Cycle 2 — TBD) naming-test rubric
- `reference/swipe-file/positioning/` — (Cycle 2 — TBD) anonymized exemplar positioning statements

## Examples

See `examples/`:
- `example-positioning-hybrid-agency.md` — (Cycle 2 — TBD) positioning for a hybrid DFY+program agency targeting B2B SaaS founders
- `example-positioning-vertical-saas.md` — (Cycle 2 — TBD) vertical-locked positioning for a DevOps-vertical agency
- `example-positioning-program-only-pivot.md` — (Cycle 2 — TBD) positioning for an agency pivoting from DFY to program-only

---

*Skill version 1.0.0 — 2026-05-03*
