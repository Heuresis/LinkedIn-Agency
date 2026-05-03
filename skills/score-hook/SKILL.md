---
name: score-hook
slug: /score-hook
agent: hook-writer
department: marketing
output_format: hook-scorecard
required_profiles:
  content_strategy: 60                   # recursive: agency-content or client-content (target scope)
allowed_scopes:
  - agency-hooks
  - client-hooks
output_path: outputs/{date}/hook-score-{post-slug}.md
---

# /score-hook — Hook Scorecard

> **Purpose.** Score a single hook before publishing. 5-axis rubric (per `reference/frameworks/content/hook-anatomy.md`): specificity / stake / pattern-break / voice-fit / decay-resistance. Each axis 0-20, total 100. Hard floor: ≥75 to ship. Below = rewrite OR regenerate.
>
> **Why this skill exists.** A bad hook locks out a great post. The first 7-12 words decide whether the next 12 readers see the next 12K (per `reference/frameworks/content/virality-engine.md`). Shipping an unscored hook treats the highest-leverage block of content as an afterthought. The scorecard makes the cost of a weak hook visible *before* publish, when rewriting is cheap.
>
> **Bar.** A hook scoring ≥75 should land the post in the top 30% of the subject's baseline impressions. Hooks scoring 60-74 will publish but underperform. Hooks <60 will suppress the post regardless of body quality.

## When to invoke

- After `/write-linkedin-post` produces a draft (mandatory pre-publish gate)
- After `/ghostwrite-client-post` produces a draft (mandatory pre-publish gate, before `/voice-drift-detector` runs on the full body)
- During `/build-hook-library` Step 4 — voice-fit scoring component reuses the rubric
- During `/audit-hook-library-decay` — re-scoring proven hooks against current quarter's standards
- Operator manually wants a second-opinion score on a hook they wrote outside the workspace

## Required inputs

1. **The hook text** (the 7-12 word opener)
2. **The post body context** (the full draft post the hook opens — needed for promise-alignment check inside Axis 5)
3. **Voice scope** (agency-hooks or client-hooks — determines which voice profile loads)
4. **Client_slug** (required if scope is client-hooks)
5. **Pillar declaration** (which of the 5 pillars from `content_strategy.{scope}.content_pillars` this post serves)
6. **Formula declaration** (which of the 9 formulas from `reference/frameworks/content/hook-anatomy.md` the hook claims to use)

If input #2 (post body) is missing, the skill produces a partial score (Axes 1-4 only) and flags `Axis 5 unscored — body required`.

## The scoring sequence

### Step 1 — Hook submission and intake

The skill ingests the hook + the post body context + the scope. Pre-flight checks:

- Hook length: must be 5-15 words. Outside this range → auto-fail with reason `length-out-of-range`.
- Hook contains banned-vocab opener (per `spec/BANNED-VOCABULARY.md` openers list)? → auto-fail with reason `banned-opener`. Skip remaining axes; recommend rewrite.
- Scope + client_slug consistent? Client-hooks scope without client_slug → halt, request slug.
- Voice profile for the declared scope loads at ≥70% completeness (per `spec/CONTEXT-THRESHOLDS.md`). Below threshold → halt, recommend `/extract-founder-voice` for that scope.

If all pre-flight passes, proceed to the 5 axes.

### Step 2 — Axis 1: Specificity (0-20)

**The question.** Does the hook name a number, mechanism, person-type, or moment vs. generic claim?

**Scoring scale:**

| Score | What it looks like |
|---|---|
| 18-20 | Multiple specific anchors (e.g., a number AND a role-type AND a time-frame). "I sent 3,200 cold DMs last quarter. 73% of replies came from messages 4-7." |
| 15-17 | One strong specific anchor (a specific number, a specific named role, or a specific time-stamp). "6 months ago, this client was getting 3 inbound calls per quarter." |
| 11-14 | Vague specificity ("a few months ago," "most agencies," "a lot of founders") — directional but not pin-pointable. |
| 6-10 | Abstract claim with no anchor. "LinkedIn growth requires consistency." |
| 0-5 | Pure generality. "Here's something I learned about content." |

**Scoring rules:**

- Specific dollar figures, headcount, conversion percentages = +3 each
- Specific time-stamps (HH:MM, "yesterday," "Q3 2024") = +2
- Named roles ("VP Engineering at scale-up," "B2B SaaS founder post-Series A") = +2
- Generic role-types ("entrepreneurs," "leaders," "professionals") = -3
- Hedge words in the specificity slot ("around," "roughly," "kind of") = -1 each
- Round-number-only specificity ("100K followers," "$1M ARR") = +1 (better than nothing, but pattern-matched as content-mill)

**Cross-check:** the calibration anchor for ≥17 specificity is `reference/swipe-file/posts-linkedin/proof/04-before-after-client-transformation.md` — "6 months ago, this client was getting 3 inbound calls per quarter. Last month: 14 calls in one week."

### Step 3 — Axis 2: Stake (0-20)

**The question.** Does the reader feel something at risk if they scroll past?

**Scoring scale:**

| Score | What it looks like |
|---|---|
| 18-20 | Reader's loss aversion fires — scrolling past costs them something they care about. "Most B2B founders write content that explains what their product does. The ones who get inbound calls write content about what their buyer is afraid of." Reader feels they might be the wrong-side founder. |
| 15-17 | Clear but moderate stake. Reader recognizes themselves in either side of a contrast. |
| 11-14 | Implied stake but not load-bearing. Reader can scroll without flinching. |
| 6-10 | No real stake — informational only. "Here are 5 things I learned about LinkedIn." |
| 0-5 | Anti-stake — reader actively repelled. Self-promotional or vague-positive opener. |

**Scoring rules:**

- Hook that names a misdiagnosis the reader is currently making = +5 (Loss Aversion + Pride hits)
- Hook that promises a payoff the reader has been chasing = +3 (Aspiration)
- Hook that quotes a specific number-loss the reader could replicate = +3 ("I lost $40K by..." — reader projects "could be me")
- Hook that names a fear the reader has but doesn't say out loud = +5
- Hook that opens with a vague positive ("Excited to share..." — already banned) = -10 even before linter catches it
- Hook with only a "here's a tip" frame = -3 (no stake, just utility)

**Cross-check:** the calibration anchor for ≥17 stake is `reference/swipe-file/posts-linkedin/authority/01-contrarian-reframe-saas-positioning.md` — the contrarian-reframe puts every B2B SaaS founder reading it on one side or the other of the wrong-vs-right divide.

### Step 4 — Axis 3: Pattern-break (0-20)

**The question.** Does the hook break feed-pattern in the first 5 words?

**Scoring scale:**

| Score | What it looks like |
|---|---|
| 18-20 | First 5 words violate the LinkedIn-feed default expectation. Time-stamp opener ("6:42 AM. A client just texted..."), specific failure-number ("I lost $87,000 on Facebook ads..."), or contrarian declaration ("Most agencies measure the wrong thing."). |
| 15-17 | First 5 words pattern-break against the expectation but use a familiar-but-strong opener (Before-after time-frame, named-mechanism intro). |
| 11-14 | First 5 words are competent but feel like LinkedIn content. The reader has seen this shape before. |
| 6-10 | First 5 words pattern-match LinkedIn-mill content. Reader's brain registers "scroll past, I've seen this." |
| 0-5 | First 5 words are a banned pattern. Auto-fail at Axis 1 pre-check; should not reach this axis. |

**Scoring rules:**

- Time-stamp opener ("6:42 AM," "Tuesday at 11pm") = +3
- Specific number opener ("I sent 3,200...", "We lost $40K...") = +4
- Direct quote opener ("'I'm pulling the program. Refund my last $4K.'") = +5
- Question-as-opener (when the question is the reader's actual internal question) = +3
- "Most {role}s" opener (Contrarian-reframe formula #1) = +2 (familiar but proven)
- "Here's something..." opener = -2 (pattern-matched)
- "I want to share..." opener = -5 (banned-adjacent)
- "Three things I learned..." opener = -3 (formula-fatigue)
- Emoji as first character = -10 (banned)
- "POV:" or "Hot take:" or "Unpopular opinion:" = -10 (banned)

**Cross-check:** the calibration anchor for ≥18 pattern-break is `reference/swipe-file/posts-linkedin/personality/06-loop-open-day-in-life.md` — "6:42 AM." as the literal first content of the post stops scroll cold.

### Step 5 — Axis 4: Voice-fit (0-20)

**The question.** Does the hook sound like the subject? (Pull from voice profile.)

This axis loads the voice profile for the declared scope:
- Scope agency-hooks → `brand_voice.agency_voice`
- Scope client-hooks → `brand_voice.client_voices[client_slug]`

**Scoring scale:**

| Score | What it looks like |
|---|---|
| 18-20 | Hook contains 2+ verbatim phrases from `phrases_to_use`, matches the cadence pattern from `language_patterns`, and aligns with `persuasion_style`. Could be quoted as a direct excerpt from the subject's past content with no attribution change. |
| 15-17 | Hook matches voice in cadence + persona but doesn't pull verbatim phrases. |
| 11-14 | Hook is voice-adjacent — same general tone but reads as written-by-someone-else-trying-to-sound-like-them. |
| 6-10 | Hook contradicts a `personality_traits` declaration or uses a phrase from `phrases_to_avoid`. |
| 0-5 | Hook contains a phrase from `phrases_to_avoid`. Auto-fail at Axis 4. |

**Scoring rules:**

- Each verbatim phrase from `phrases_to_use` present = +3 (cap at +9 for 3 phrases)
- Cadence match (sentence rhythm + comma density + clause length matches `language_patterns`) = +5
- Stance match (contrarian / consultative / technical / storytelling matches `persuasion_style`) = +3
- Authority position match (hook positions subject as their declared `authority_positioning`: category-king / challenger / insider / outsider-with-edge) = +3
- Each `phrases_to_avoid` entry present = -10
- Hook positions subject in opposite stance from `persuasion_style` (e.g., consultative voice with contrarian-attack opener) = -5
- Generic-LinkedIn voice with no subject specificity = -5

**Cross-check (client-hooks scope):** the hook must NOT pattern-match `brand_voice.agency_voice.phrases_to_use` if scope is client-hooks. Cross-contamination = -10. Per `INVARIANTS.md` N-6.

### Step 6 — Axis 5: Decay-resistance (0-20)

**The question.** Will this hook still work in 6 months — or is it riding a temporary wave?

**Scoring scale:**

| Score | What it looks like |
|---|---|
| 18-20 | Hook references evergreen mechanism, transformation arc, or named principle. Will work as well in Q3 as it does today. |
| 15-17 | Hook is durable but mildly seasonal (references current quarter's behavior, current platform feature). |
| 11-14 | Hook is medium-decay — formula is over-used in current cycle but still works for now. |
| 6-10 | Hook is fast-decay — imperative voice ("Stop doing X"), curiosity-gap ("Here's what they won't tell you"), or template-heavy. Will fatigue within 30-60 days. |
| 0-5 | Hook references a 30-day-old current event, a one-week trending topic, or a platform UI feature that may not exist next quarter. |

**Scoring rules:**

- Hook references named mechanism owned by subject = +5 (mechanism names compound, don't decay)
- Hook is formula #2 (Specific-stakes), #3 (Named-mechanism), #4 (Before-after), or #7 (Loop-open) = +3 (slow-decay formulas per `/build-hook-library` Step 1 table)
- Hook is formula #6 (Pattern-break) or #9 (Insider-secret) = -3 (fast-decay formulas; over-used in current LinkedIn cycle)
- Hook references a current event, trending topic, or seasonal moment = -5 (90-day hard sunset)
- Hook references the operator's mechanism / belief / IP that they will defend for years = +5
- Hook is structurally identical to one of the subject's last 10 posts = -5 (intra-subject repetition)
- Hook is structurally identical to a hook in `content_strategy.{scope}.hook_library.banned_hooks` = -10

**Cross-check (promise alignment, sub-axis).** Within Axis 5, additionally evaluate: does the post body deliver on what the hook promises? If the hook says "73% of replies came from messages 4-7" but the body never explains what messages 4-7 do, the hook over-promises. Penalize -5 on Axis 5 for hook-body misalignment. (This sub-check is what folds promise-alignment from the original 5-axis rubric in `hook-anatomy.md` into the decay-resistance axis — over-promising hooks decay fastest as audience trust erodes.)

### Step 7 — Banned-vocabulary check (terminal gate)

The full hook text runs through the `spec/BANNED-VOCABULARY.md` linter at this stage as a terminal gate, even if it passed pre-flight in Step 1. The pre-flight catches openers; this catches body phrases that may have slipped in:

- Any phrase from "Body phrases (banned)" list (Generic transitions, Hyperbolic claims, Generic appeals to community/expertise) → auto-fail
- Any phrase from "Workspace-specific bans (anti-positioning)" list → auto-fail
- Any "Allowed-with-care" phrase used as opener (e.g., "Quick story:" opening a hook) → auto-fail (these are body-only)

Auto-fail on this gate = total score forced to 0, regardless of axis scores. Reason logged: `BANNED_VOCABULARY_DETECTED — {phrase}`. Recommended action: full rewrite, not edit.

### Step 8 — Score emission and rewrite suggestions

Sum the 5 axes (each 0-20) for total 0-100. Apply the gates:

| Total Score | Verdict | Action |
|---|---|---|
| 90-100 | Ship-ready, top-tier | Publish; flag as candidate for promotion to `proven_hooks` |
| 80-89 | Ship-ready | Publish; monitor performance |
| 75-79 | Ship-ready (floor) | Publish; track performance closely; rewrite if it underperforms |
| 60-74 | Hold | Rewrite recommended; offer 3 stronger variants |
| 0-59 | Reject | Full rewrite; do not publish |

**Rewrite-suggestion protocol (when score < 75):**

Generate 3 alternative hooks. Each alternative:

1. Targets the lowest-scoring axis from the original (e.g., if Axis 1 Specificity scored 8, rewrite for specificity)
2. Pulls from a different formula than the original (per `hook-anatomy.md` 9 formulas) to give the operator structural choice
3. Pre-scores against all 5 axes; only emit alternatives that score ≥80

If three alternatives ≥80 cannot be generated against the same post body, escalate: the post body itself may not have a hookable angle. Recommended action — the operator (or content-strategist) revisits whether the post idea is strong enough for this pillar slot.

## Output format

```yaml
---
artifact_type: hook-scorecard
scope: agency-hooks | client-hooks
client_slug: null | "<slug>"
hook_text: "<the scored hook>"
post_slug: "<slug for the post this hook opens>"
formula_declared: <one of 9 formulas>
pillar_declared: Authority | Proof | Personality | Offer-bridge | Pier
score_date: YYYY-MM-DD
profile_completeness: 0-100
profiles_used: [brand_voice, content_strategy, ideal_customer_profile]
frameworks_used: [hook-anatomy, banned-vocabulary]
confidence: HIGH | MEDIUM | LOW
verdict: SHIP-READY | HOLD | REJECT | AUTO-FAIL-BANNED
total_score: 0-100
---

# Hook Scorecard

## The hook
"{the hook text}"

## The 5-axis breakdown

| Axis | Score | Notes |
|---|---|---|
| 1. Specificity | 0-20 | {what landed, what didn't} |
| 2. Stake | 0-20 | {what the reader's loss-aversion / aspiration trigger looks like} |
| 3. Pattern-break | 0-20 | {what the first 5 words do against feed-default} |
| 4. Voice-fit | 0-20 | {phrases-to-use hits, cadence match, persona alignment} |
| 5. Decay-resistance | 0-20 | {evergreen vs. seasonal, promise-alignment sub-check} |
| **Total** | **0-100** | |

## Verdict
{SHIP-READY ≥75 / HOLD 60-74 / REJECT <60 / AUTO-FAIL-BANNED}

## Banned-vocabulary check
{passed / failed — if failed, the matched phrase + section}

## Rewrite suggestions (only if HOLD or REJECT)
1. "{alternative hook 1}" — formula: {x}, pre-score: {n}
2. "{alternative hook 2}" — formula: {y}, pre-score: {n}
3. "{alternative hook 3}" — formula: {z}, pre-score: {n}

## Performance prediction
{baseline impressions multiplier estimate based on score band — e.g., "Score 82 hooks historically deliver 1.4-1.8× baseline impressions for this subject"}

## Cross-references
{which voice profile loaded, which content_strategy scope, which post body context}
```

## Failure modes and guards

- **Scoring inflation.** Hook-writer scores their own hook generously. Guard: every score ≥85 must cite the specific verbatim phrase / mechanism / number that justifies the high band — no narrative-only justification at the top end.
- **Specificity false-positive.** Hook contains a number that's not actually specific to the subject ("over 100 founders" — generic). Guard: Axis 1 only counts specifics that are credibly subject-owned (sourced from `case_studies` or `voice_of_customer` evidence).
- **Voice-fit on a contaminated profile.** Scoring against a client voice profile that's actually populated with agency voice patterns. Guard: if scope is client-hooks, cross-check that `brand_voice.client_voices[slug]` was extracted via `/extract-founder-voice client-voice slug={slug}` and not inherited from agency-voice.
- **Banned-vocab missed at Axis 1, caught at Step 7.** Edge case where opener passes (no banned-opener pattern) but body of hook contains banned phrase. Guard: Step 7 is terminal; never skipped.
- **Score 75-79 false confidence.** Hooks at the floor get treated as ship-ready when they're actually marginal. Guard: 75-79 scores ship with `monitor closely` flag; if post underperforms baseline, hook gets `decay_risk: fast-decay` retro-tag in `hook_library`.
- **Promise-alignment skipped because body unavailable.** Operator scores hook in isolation. Guard: skill emits partial score (Axes 1-4 + Axis 5 capped at 15) and flags `Axis 5 promise-alignment unscored`.
- **Hook scores 90+ but post still flops.** Hook isn't the only variable; post body, dwell-time, time-of-publish, comment-reply harvest all matter. Guard: post-publish, log actual performance back to the scorecard for cycle-N+1 calibration.

## Cross-references

- `reference/frameworks/content/hook-anatomy.md` — the source of the 5-axis rubric and 9 formulas
- `reference/frameworks/content/virality-engine.md` — the first-12-impression bet that hooks decide
- `reference/swipe-file/posts-linkedin/authority/01-contrarian-reframe-saas-positioning.md` — calibration anchor for 17+ on Specificity, Stake, Voice-fit
- `reference/swipe-file/posts-linkedin/proof/04-before-after-client-transformation.md` — calibration anchor for 18+ on Specificity (number contrast)
- `reference/swipe-file/posts-linkedin/personality/06-loop-open-day-in-life.md` — calibration anchor for 18+ on Pattern-break (time-stamp opener)
- `reference/swipe-file/posts-linkedin/offer-bridge/10-hook-loop-cta.md` — calibration anchor for 18+ on Stake (specific-stakes opener feeding offer)
- `spec/BANNED-VOCABULARY.md` — the linter pattern source for Step 1 pre-check and Step 7 terminal gate
- `spec/CONTEXT-THRESHOLDS.md` — voice profile ≥70 floor for scoring against
- `agents/marketing-head.md` — escalation when hook fails 3 rewrites and post body is in question
- `agents/hook-writer.md` — the executing agent persona
- `INVARIANTS.md` N-4 (banned vocab — terminal), N-6 (no voice contamination across scopes)
- `skills/build-hook-library/SKILL.md` — Step 4 of that skill reuses this scoring rubric
- `skills/write-linkedin-post/SKILL.md` — pre-publish gate consumer
- `skills/ghostwrite-client-post/SKILL.md` — pre-publish gate consumer
- `skills/audit-hook-library-decay/SKILL.md` — quarterly re-scoring of `proven_hooks`

## Examples

See `outputs/{date}/` for live scorecards. Calibration examples in `examples/`:
- `example-scorecard-authority-contrarian-reframe.md` — 88/100 ship-ready
- `example-scorecard-personality-loop-open.md` — 92/100 top-tier
- `example-scorecard-rejected-banned-opener.md` — 0/100 auto-fail with rewrite suggestions
- `example-scorecard-hold-low-specificity.md` — 68/100 hold with 3 alternatives

---

*Skill version 1.0.0 — 2026-05-03*
