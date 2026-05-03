---
name: build-hook-library
slug: /build-hook-library
agent: hook-writer
department: marketing
output_format: hook-library
required_profiles:
  brand_voice: 70                        # recursive: agency-voice or client-voice
  content_strategy: 60                   # recursive: agency-content or client-content
allowed_scopes:
  - agency-hooks
  - client-hooks
output_path:
  agency-hooks: company.yaml::content_strategy.agency_content.hook_library
  client-hooks: company.yaml::content_strategy.client_content[{client_slug}].hook_library
---

# /build-hook-library — Hook Library

> **Purpose.** Build a 50-100 hook library for a single subject (the agency operator OR a specific client). Each hook is tagged with: hook formula (one of 9 from `reference/frameworks/content/hook-anatomy.md`), pillar fit, voice-fit score, decay risk, last-used date. The library is the raw material every `/write-linkedin-post` and `/ghostwrite-client-post` pulls from.
>
> **Why this skill exists.** Without a pre-built, voice-calibrated, banned-vocab-clean library, posts default to whatever the runtime invents at write-time. Runtime defaults bias hard toward the patterns enumerated in `spec/BANNED-VOCABULARY.md` ("In this post, I'll share...", "Unpopular opinion:", emoji openers). The library shifts the default from generic-LinkedIn-mill to subject-voice-native.
>
> **Bar.** A linkedin-ghostwriter handed this library + a topic should be able to pick a hook in <60 seconds that scores ≥75 on `/score-hook` without rewrite. If they can't, the library is too thin or off-voice.

## When to invoke

- After `/build-content-engine` completes for a new subject (agency-content or client-content)
- Quarterly refresh trigger from `/audit-hook-library-decay` (when ≥40% of proven hooks have decayed below baseline)
- New pillar added — pillar-fit gaps need filling
- Voice profile re-extraction — hooks scored below 70 voice-fit need regeneration
- New client onboarded — full library build for the client_slug

## Required inputs

1. Voice profile (≥70% — from `/extract-founder-voice` for the target scope)
2. Content strategy spec (≥60% — from `/build-content-engine` for the target scope) — pillars and post types must exist
3. **30-50 of the subject's past posts** (engagement data attached) — for past-high-performer reverse-engineering
4. **Voice-extraction transcript** (the raw interview from `/extract-founder-voice`) — for verbatim phrasing
5. **Sales-call transcripts OR DM thread transcripts OR application form responses** (5-15 of each, if available) — for ICP voice-of-customer mining
6. **Belief audit** — the operator's (or client's) 5-10 contrarian-take beliefs that pass the "I'd defend this in a room of 50 strangers" test
7. **Comment-section harvest** — 50-200 substantive comments left on the subject's past posts (the audience's own language)

If inputs 3-7 are absent, the skill produces a Skeleton-tier library tagged `low_confidence` and queues an interview-mode pass to fill gaps.

## The build sequence

### Step 1 — Hook formula refresher (5 min)

Reload the 9 formulas from `reference/frameworks/content/hook-anatomy.md`. The library will hold 5-10 hooks per formula × 9 formulas = 45-90 base library, plus 5-15 evergreen subject-specific hooks = 50-100 total target.

| # | Formula | Pillar fit | Decay profile |
|---|---|---|---|
| 1 | Contrarian-reframe | Authority | Slow decay (durable when grounded in real belief) |
| 2 | Specific-stakes | Personality + Proof | Very slow (numbers don't decay) |
| 3 | Named-mechanism | Authority + Proof | Slow (names compound; only refreshed when mechanism evolves) |
| 4 | Before-after | Proof | Slow (transformation arcs evergreen) |
| 5 | List-promise | Authority + Proof | Medium (formula-heavy; "5 things I learned" template fatigues fast) |
| 6 | Pattern-break | Authority + Pier | Fast (imperative voice over-used; cap at ~10% of library) |
| 7 | Loop-open | Personality + Proof | Slow (narrative-shaped; structurally evergreen) |
| 8 | Question-stake | Authority + Personality | Medium (specificity-dependent; generic questions decay fast) |
| 9 | Insider-secret | Authority | Fast (curiosity-gap template fatigues; cap at ~10%) |

The formula-to-pillar table sets the target distribution downstream in Step 6.

### Step 2 — Hook source mining (45 min)

Pull raw material from five sources. Each source produces hook seeds — fragments that become hooks in Step 3.

**Source 1 — Past-high-performer reverse-engineering.** Pull the top-decile posts from input #3 by engagement (impressions × engagement-rate). For each, extract the hook (first 7-12 words) verbatim. Tag: `proven_hook`, `formula_used`, `last_engagement_score`. These become the proven-hook anchor of the library — they already worked once for this subject.

**Source 2 — Voice-extraction transcript pull.** Comb the interview transcript (input #4) for 8-15 verbatim phrases the subject uses in spoken language that have hook-shape: opinions stated as fact, contrarian beliefs, specific-stakes anecdotes. Pull verbatim. These become the voice-native seed pool.

**Source 3 — ICP voice-of-customer phrasing.** Pull 10-20 verbatim phrases from input #5 (sales calls, DMs, applications) where the ICP describes their own pain, desire, fear, or aspiration. Cross-reference against `ideal_customer_profile.{scope}.voice_of_customer.pain_language_patterns`. These become Question-stake (#8) and Loop-open (#7) hook seeds — direct mirrors to the reader's internal monologue.

**Source 4 — Contrarian-take reframes from the belief audit.** Each of the 5-10 beliefs from input #6 becomes 2-3 hook variants in formula #1 (Contrarian-reframe) or formula #6 (Pattern-break). Belief: "Most agencies optimize for output volume; the ones that retain optimize for client-side rate-of-rewrite." → Hook variant: "Most LinkedIn agencies measure their work in posts shipped. The ones that keep clients past month 4 measure it in posts the client publishes without rewriting a word."

**Source 5 — Comment-section harvest.** From input #7, pull comments that ask a question, state an objection, or describe their own situation. The 5-12 most-recurring patterns become the Question-stake (#8) and Insider-secret (#9) hook seeds — these are the questions the audience is already asking themselves.

Output of Step 2: a `seeds.md` working file with 80-150 hook fragments, each tagged by source.

### Step 3 — Hook drafting per formula (90 min)

For each of the 9 formulas, generate 5-10 hooks. Pull from the seed pool in Step 2. Where the seeds don't yield 5 hooks for a formula, write fresh hooks against the formula's anatomy from `reference/frameworks/content/hook-anatomy.md`.

Drafting rules:

- **Verbatim-first.** Where a seed is already hook-shaped (Source 1 + Source 2), use the verbatim text. Edit only for length (cap at 12 words).
- **No banned vocab.** Cross-check every draft against `spec/BANNED-VOCABULARY.md` — auto-discard any draft that opens with a banned phrase or contains a banned hyperbolic claim.
- **Specificity over cleverness.** Numbers, role-types, named mechanisms, time-stamps. Generic-language hooks ("Here's what I learned about...") get cut even before voice-fit scoring.
- **Pillar tag on every hook.** Every hook gets one or more of the 5 pillars from `content_strategy.{scope}.content_pillars` — Authority / Proof / Personality / Offer-bridge / Pier.
- **Mechanism slot.** Where the hook references a mechanism (formula #3 named-mechanism, formula #1 contrarian-reframe), the mechanism name must come from `business_context.positioning.unique_selling_proposition` or `product_strategy.service_tier.offers[*].unique_mechanism`. Inventing a new mechanism name in a hook = drift.
- **Calibration target.** For each formula, study the corresponding annotated example in `reference/swipe-file/posts-linkedin/`:
  - Formula #1 (Contrarian-reframe) → `reference/swipe-file/posts-linkedin/authority/01-contrarian-reframe-saas-positioning.md`
  - Formula #2 (Specific-stakes) → opens of `reference/swipe-file/posts-linkedin/offer-bridge/10-hook-loop-cta.md`
  - Formula #4 (Before-after) → `reference/swipe-file/posts-linkedin/proof/04-before-after-client-transformation.md`
  - Formula #7 (Loop-open) → `reference/swipe-file/posts-linkedin/personality/06-loop-open-day-in-life.md`
- **Cap each formula at 10 drafts.** More dilutes the library; fewer than 5 leaves a pillar under-served.

Output of Step 3: 50-100 hook drafts, each tagged with formula, pillar(s), and source.

### Step 4 — Voice-fit scoring (30 min)

Each hook scored 0-100 on whether it sounds like the subject. The scoring runs against the voice profile (input #1):

- **Cadence match (0-30):** sentence rhythm, comma density, single-clause vs. compound. Cross-check against `brand_voice.{scope}.language_patterns`.
- **Phrase fit (0-30):** does this hook contain phrases from `phrases_to_use`? Does it avoid `phrases_to_avoid`? Auto-fail at <10 if it contains a `phrases_to_avoid` entry.
- **Persona alignment (0-20):** does the stance (contrarian, consultative, technical, storytelling) match `brand_voice.{scope}.persuasion_style` and `personality_traits`?
- **Authority position (0-20):** does the hook position the subject at their declared `authority_positioning` (category-king / challenger / insider / outsider-with-edge)?

Scoring threshold:
- ≥80 → ship-ready
- 60-79 → minor edit, re-score
- <60 → rewrite from a different seed

Hooks below 60 after one rewrite attempt get cut. Better to ship a 60-hook library at 85+ voice-fit than a 100-hook library at 65 average.

### Step 5 — Decay-risk tagging (20 min)

Each hook tagged for decay risk based on the formula's decay profile (Step 1 table) plus the hook's specificity:

| Risk | When to tag | Sunset trigger |
|---|---|---|
| `evergreen` | Formula #2/3/4/7 + verbatim subject phrasing + concrete number/name | Performance drops >40% below baseline for 60+ days |
| `medium-decay` | Formula #5/8 + specificity-dependent | Performance drops >30% below baseline for 30 days |
| `fast-decay` | Formula #6/9 + imperative or curiosity-gap shape | Performance drops >25% below baseline for 14 days OR formula appears 3+ times in 30 days |
| `seasonal` | Hook references a current event, quarterly trend, or platform behavior | 90-day hard sunset regardless of performance |

Distribution target: ≥50% evergreen, ≤20% fast-decay, ≤10% seasonal. A library skewed to fast-decay produces the engagement spike → decay → rebuild cycle that burns hook-writer time.

### Step 6 — Pillar assignment (15 min)

Each hook tagged to one of the 5 pillars from `content_strategy.{scope}.content_pillars`. Multi-pillar hooks (a Contrarian-reframe that fits both Authority and Pier) get a primary pillar + secondary tag.

Target distribution (matches the agency-content default mix from `/build-content-engine` Step 1):
- Authority: 25-30% of hooks
- Proof: 20-25%
- Personality: 15-25%
- Offer-bridge: 10-20%
- Pier: 5-10%

For client-content, the distribution shifts based on the client's solution-awareness target (from `ideal_customer_profile.client_icps[slug].market_sophistication.awareness_level`):
- Solution-aware audience → heavier Proof + Authority hooks
- Problem-aware audience → heavier Personality + Pier hooks
- Most-aware audience → heavier Offer-bridge hooks

Pillar gaps surface here. If Offer-bridge has only 3 hooks but the target is 8, return to Step 3 with `formula in {2, 3, 8}` and `pillar=Offer-bridge` as the constraint.

### Step 7 — Library YAML emission

Hydrate `content_strategy.{scope}.hook_library` in `company.yaml`:

```yaml
hook_library:
  hook_taxonomy_path: "reference/frameworks/content/hook-anatomy.md"
  build_date: YYYY-MM-DD
  build_scope: agency-hooks | client-hooks
  client_slug: null | "<slug>"
  total_hooks: <integer>
  proven_hooks:                          # Source 1 anchor — verbatim past-high-performers
    - hook_text: "<verbatim text>"
      formula: contrarian-reframe | specific-stakes | named-mechanism | before-after | list-promise | pattern-break | loop-open | question-stake | insider-secret
      pillar_primary: Authority | Proof | Personality | Offer-bridge | Pier
      pillar_secondary: <pillar or null>
      voice_fit_score: 0-100
      decay_risk: evergreen | medium-decay | fast-decay | seasonal
      last_used_date: YYYY-MM-DD | null
      historical_engagement_baseline: <impressions number or null>
      banned_vocab_clean: true | false
      source: past-performer
  fresh_hooks:                           # Sources 2-5 — drafted hooks not yet shipped
    - hook_text: "<text>"
      formula: <one of 9>
      pillar_primary: <pillar>
      pillar_secondary: <pillar or null>
      voice_fit_score: 0-100
      decay_risk: <tag>
      last_used_date: null
      source: voice-transcript | icp-voc | belief-audit | comment-harvest
      banned_vocab_clean: true | false
  banned_hooks:                          # Hooks dead this quarter — explicit do-not-use
    - hook_text: "<text>"
      reason: banned-vocab | decayed-formula | platform-suppressed
      sunset_date: YYYY-MM-DD
  formula_distribution:                  # Library composition audit
    contrarian-reframe: <count>
    specific-stakes: <count>
    named-mechanism: <count>
    before-after: <count>
    list-promise: <count>
    pattern-break: <count>
    loop-open: <count>
    question-stake: <count>
    insider-secret: <count>
  pillar_distribution:
    Authority: <count>
    Proof: <count>
    Personality: <count>
    Offer-bridge: <count>
    Pier: <count>
  decay_distribution:
    evergreen: <count>
    medium-decay: <count>
    fast-decay: <count>
    seasonal: <count>
  next_audit_due: YYYY-MM-DD             # +90 days from build_date
```

### Step 8 — Banned-vocabulary sweep

Every hook in the library runs through the linter pattern from `spec/BANNED-VOCABULARY.md`. The sweep checks:

- Hook openers list (banned section #1) — auto-fail any hook that opens with a banned phrase
- Body phrases list (banned section #2) — flag any hook containing a banned generic transition or hyperbolic claim
- Anti-positioning list (workspace-specific bans) — flag any hook using LinkedIn-guru framing the operator's positioning rejects
- Allowed-with-care list — hooks using "Quick story:" or "Here's the thing:" as openers get auto-flagged for rewrite (these phrases are body-only, never opener)

Sweep output:

```
hook_library_lint_result:
  total_hooks_scanned: <n>
  banned_vocab_clean: <count>
  banned_vocab_flagged: <count>
  flagged_hooks:
    - hook_text: "<text>"
      banned_pattern: "<the matching phrase>"
      banned_section: "openers | body | anti-positioning | allowed-with-care"
      action: auto-rewrite | manual-review | delete
```

Flagged hooks queue for rewrite. The library does not emit until the linter returns `banned_vocab_clean ≥ 95%` of total hooks. The remaining ≤5% are explicit allowed-with-care exceptions logged in `outputs/{date}/hook-library-exceptions.log` with operator sign-off.

## Validation gate (mandatory before declaring done)

Three checks:

1. **The 5-hook-pull test.** Pick 5 random pillar+formula combinations. For each, the library must yield ≥2 hooks scoring ≥75 voice-fit, ≥1 in decay-risk `evergreen` or `medium-decay`. Pass: all 5 combinations satisfied.

2. **The downstream-skill consumer test.** Hand the library + a topic to a linkedin-ghostwriter. Ask: pick a hook for an Authority-pillar post on this topic in <60 seconds. Pass: hook chosen scores ≥75 on `/score-hook`, no rewrite required.

3. **The pillar-coverage test.** Cross-check pillar distribution against `/build-content-engine` pillar weights. Any pillar under-served by >5 hooks vs. target = library gap. Return to Step 3.

If any check fails, return to the failing step. Library is not shipped until all three pass.

## Output format

```yaml
---
artifact_type: hook-library
scope: agency-hooks | client-hooks
client_slug: null | "<slug>"
profile_completeness: 0-100
build_date: YYYY-MM-DD
validation_gate: passed | failed | not-yet-run
profiles_used: [brand_voice, content_strategy, ideal_customer_profile, business_context]
frameworks_used: [hook-anatomy, virality-engine, banned-vocabulary]
confidence: HIGH | MEDIUM | LOW
total_hooks: <integer>
banned_vocab_clean_pct: 0-100
---

{the populated hook_library YAML from Step 7}

## Build session brief
{1-page narrative — sources mined, formula gaps surfaced, pillar weights chosen, decay-risk distribution rationale}

## Validation gate result
{the three gate checks with pass/fail per check}

## Linter sweep result
{from Step 8 — banned-vocab clean count, flagged hooks, exceptions log}

## Next actions
{usually: schedule next /audit-hook-library-decay, hand library to /write-linkedin-post or /ghostwrite-client-post for first batch}
```

## Failure modes and guards

- **Library skewed to one formula.** If 40%+ of hooks are formula #1 (contrarian-reframe) because the operator is naturally contrarian, the library produces visible repetition. Guard: cap each formula at 15 hooks; force diversification.
- **Voice-fit average below 80.** Below 80 average = downstream posts will require rewrite. Guard: re-mine Source 2 (voice transcript), reduce hook count, raise floor.
- **Banned-vocab leakage.** Hooks containing "Here's the truth about" or "Stop scrolling" pass voice-fit but fail brand. Guard: Step 8 lint runs after Step 4, not before — voice-fit doesn't override banned status.
- **Pillar starvation.** Offer-bridge gets 3 hooks because the operator is uncomfortable with sales-adjacent framing. Guard: pillar-coverage test in Validation Gate forces return to Step 3 until target met.
- **Stale proven-hooks.** A hook that hit 80K impressions 18 months ago may have decayed. Guard: `last_engagement_score` requires a measurement within the last 90 days; older = re-test before promoting.
- **Client library contaminated by agency voice.** Ghostwriter mines agency voice transcripts when building a client library. Guard: Step 2 Source 2 must reference `brand_voice.client_voices[slug]`, never `brand_voice.agency_voice` for client-hooks scope. Per `INVARIANTS.md` N-1, N-6.
- **Belief-audit fakes.** Operator lists "contrarian beliefs" that are mainstream-with-spin. Guard: belief audit must pass the "I'd defend this in a room of 50 strangers including my biggest critics" test. Beliefs that don't pass are content-mill openers, not real beliefs.

## Cross-references

- `reference/frameworks/content/hook-anatomy.md` — the 9 formulas + scoring rubric
- `reference/frameworks/content/virality-engine.md` — first-12-impression bet (hooks decide it)
- `reference/swipe-file/posts-linkedin/authority/01-contrarian-reframe-saas-positioning.md` — formula #1 anchor
- `reference/swipe-file/posts-linkedin/proof/04-before-after-client-transformation.md` — formula #4 anchor
- `reference/swipe-file/posts-linkedin/personality/06-loop-open-day-in-life.md` — formula #7 anchor
- `reference/swipe-file/posts-linkedin/offer-bridge/10-hook-loop-cta.md` — formula #2 used as opener
- `spec/BANNED-VOCABULARY.md` — the linter pattern source
- `spec/CONTEXT-THRESHOLDS.md` — gate at brand_voice ≥70, content_strategy ≥60
- `agents/marketing-head.md` — owns the gate between this skill and downstream ghostwriting
- `agents/hook-writer.md` — the executing agent persona
- `INVARIANTS.md` N-1 (load client voice), N-4 (banned vocab), N-6 (no voice contamination)
- `skills/build-content-engine/SKILL.md` — produces the pillars this library hydrates against
- `skills/score-hook/SKILL.md` — runtime scoring rubric used per hook in Step 4
- `skills/audit-hook-library-decay/SKILL.md` — quarterly refresh trigger that feeds back into this skill
- `skills/write-linkedin-post/SKILL.md` — primary library consumer
- `skills/ghostwrite-client-post/SKILL.md` — primary client-scope library consumer

## Examples

See `examples/`:
- `example-agency-hook-library-hybrid-archetype.md` — agency-scope library for a hybrid DFY+program operator
- `example-client-hook-library-saas-founder.md` — client-scope library for a B2B SaaS founder client
- `example-client-hook-library-services-founder.md` — client-scope library for a B2B services founder client

---

*Skill version 1.0.0 — 2026-05-03*
