---
name: write-linkedin-post
slug: /write-linkedin-post
agent: linkedin-ghostwriter
department: marketing
output_format: linkedin-post-draft
required_profiles:
  brand_voice.agency_voice: 80
  content_strategy.agency_content: 70
  ideal_customer_profile.agency_icp: 60
allowed_scopes:
  - agency-post
output_path:
  agency-post: outputs/{date}/posts/agency/{post-slug}.md
---

# /write-linkedin-post — Agency LinkedIn Post (Founder Account)

> **Purpose.** Write a single publish-ready LinkedIn post for the agency operator's own LinkedIn presence. The post draws from the agency's voice + content strategy + hook library + ICP. Output is the actual post text (not a brief, not a summary), accompanied by metadata that makes the post auditable, repurposable, and feedback-loop-ready.
>
> **Bar.** A B2B founder reading the post in their LinkedIn feed cannot tell it was AI-assisted. The post passes the Blind Output Test before it ships. If the post would not stand alongside the operator's own top-decile prior posts, the spec is too thin or the encoding is wrong — fix one of the two, never lower the bar.

## Scope

This skill is **agency-side only**. It hydrates from `brand_voice.agency_voice`, never from any client subprofile. The companion skill for client-side ghostwriting is `/ghostwrite-client-post` — recursively scoped, gated by voice-drift detection, queued for human review. **Confusing the two is the most common voice-contamination failure mode in the workspace** (per `INVARIANTS.md` N-1, N-6). If `client_slug` is present in the request, this skill refuses and routes to `/ghostwrite-client-post`.

## When to invoke

- Calendar slot fires for an agency-account post (per `content_strategy.agency_content.calendar_4_week_template`)
- Operator requests an off-calendar post tied to a real-time event (deal closed, conversation, news)
- Pier opportunity surfaces — high-engagement post in subject area, 24-hour window to publish a pier-leveraged response
- Repurpose source needed for `/repurpose-to-x` or `/repurpose-to-ig`
- Variant generation requested (operator wants 3 hook alternatives for the same body)

## Required inputs

1. **Agency voice profile** ≥80% (from `/extract-founder-voice` scope=agency-voice)
2. **Agency content strategy** ≥70% (from `/build-content-engine` scope=agency-content) — with at minimum: pillars, post-type library, hook library, calendar slot for this date
3. **Agency ICP** ≥60% (from `/build-icp` scope=agency-icp) — pain language, desire language, objection patterns
4. **Calendar slot context** — which day, which pillar, which post type, which target ICP segment, which conversion bridge. If invoked off-calendar, operator supplies pillar + post-type + topic seed.
5. **Optional: pier context** — link or text of the high-engagement post being leveraged, if applicable
6. **Optional: real-time material** — verbatim DM, call quote, deal context the post references (with permission flags)

If inputs 1-3 are below threshold, halt per `spec/CONTEXT-THRESHOLDS.md` and route the operator to the prerequisite skill.

## Build sequence

### Step 1 — Slot context load (3 min)

Pull from the agency content strategy:

```yaml
slot_context:
  date: YYYY-MM-DD
  weekday: Mon | Tue | Wed | Thu | Fri
  pillar: Authority | Proof | Personality | Offer-bridge | Pier
  post_type: <from content_strategy.agency_content.post_types>
  topic_cluster: <from pillar.topic_clusters>
  target_icp_segment: <which ICP segment this slot speaks to>
  conversion_bridge: <named bridge for this pillar — soft/hard/comment-trigger>
  draft_cue: <1-line topic prompt from calendar OR operator-supplied seed>
  cross_post_compound_position: standalone | day-1-text | day-2-carousel | day-3-video
```

If any field is empty, ask the operator before proceeding. **Do not improvise the pillar.** Improvised pillar = unbalanced calendar over time = the failure mode `marketing-head` warns against.

### Step 2 — Voice profile hydration (3 min)

Load the full agency voice profile into working context. The non-negotiable fields:

- `communication_style` (e.g., `contrarian-direct`)
- `tone_framework.primary` + `tone_framework.secondary` (e.g., `blunt-warm`)
- `personality_traits` (3-5 traits — every line of the post should be checkable against these)
- `language_patterns` (cadence patterns — sentence-length rhythm, paragraph density, signature constructions)
- `phrases_to_use` (15-25 verbatim — these get reached for first when there's a phrasing choice)
- `phrases_to_avoid` (10-25 — these are auto-blocked, no exceptions)
- `persuasion_style` (`authority-by-proof` / `story-first` / `mechanism-first` / `contrarian-provocation`) — sets the structural bias
- `authority_positioning` (`category-king` / `challenger` / `insider` / `outsider-with-edge`)
- `story_bank` (5-12 stories — index for proof anchors)

Hold these in working context for the whole build. The `phrases_to_use` list is reached for at every phrasing choice; `phrases_to_avoid` is enforced at the lint pass in Step 7.

### Step 3 — ICP framing check (2 min)

Identify the **target reader-state** for this post. Pull from `ideal_customer_profile.agency_icp.voice_of_customer`:

- Which **pain language pattern** is this post intersecting?
- Which **desire language pattern** is the implicit promise of the post?
- Which **objection** (if any) does this post pre-handle?
- What is the reader's **awareness level** when they encounter this post (Unaware / Problem-aware / Solution-aware / Product-aware / Most-aware)? Most agency-account posts target Solution-aware or Product-aware; Personality + Pier posts can reach Problem-aware.
- What is the reader's **dominant limiting belief** (Helpless / Hopeless / Worthless)? This calibrates the emotional axis of the post.

Output a 1-line **reader-state hypothesis**: "Solution-aware B2B founder, Helpless-dominant on the LinkedIn-content axis, scrolling at 9:30pm Tuesday after a flat sales week." This hypothesis is the calibration target for hook + body. Wrong hypothesis = right post for the wrong reader = high impressions, low conversion.

### Step 4 — Hook selection or generation (8 min)

Two paths:

**Path A — pull from the proven library.** If `content_strategy.agency_content.hook_library.proven_hooks` contains a hook that fits the slot's pillar + post-type + topic and the formula has not been used in the last 21 days, prefer the proven hook. Adapt the specifics; preserve the structure.

**Path B — generate via the 9 formulas.** If no proven hook fits or rotation is required (per the no-repeat-formula-3-weeks-running rule), generate via `reference/frameworks/content/hook-anatomy.md`. Pillar → formula mapping defaults:

| Pillar | Default formula(s) | Backup formula(s) |
|---|---|---|
| Authority | Contrarian-reframe (#1), Named-mechanism (#3), Insider-secret (#9) | Pattern-break (#6), List-promise (#5) |
| Proof | Before-after (#4), Specific-stakes (#2) | Named-mechanism (#3), List-promise (#5) |
| Personality | Loop-open (#7), Specific-stakes (#2) | Question-stake (#8) |
| Offer-bridge | Question-stake (#8), Named-mechanism (#3) | Specific-stakes (#2) into Hook-Loop-CTA |
| Pier | Pattern-break (#6), Contrarian-reframe (#1) | List-promise (#5) with stakes |

Generate **3 hook candidates** for this slot. Score each via `/score-hook` against the 5-axis rubric (Stop-the-scroll, Specificity, Voice match, Promise alignment, Filter strength). The selected hook must score ≥4 on every axis except Filter strength (≥3 acceptable). Threshold = combined score ≥75 / 100. Below threshold = regenerate.

The selected hook gets calibrated against the operator's voice profile one more time: does it use a `phrases_to_use` construction where possible? Does it avoid every `phrases_to_avoid`? Does the cadence match the operator's `language_patterns`?

### Step 5 — Body construction (12 min)

Body construction is governed by `reference/frameworks/content/virality-engine.md` — specifically the **first-12-impression bet** (the algorithm decides distribution in the first 60 minutes from the first 12 readers) and the **dwell-time engineering** primitive.

Per pillar, the body shape:

**Authority post body shape:**
- Opening sentence picks up the hook, single line, sets implicit promise
- Mechanism breakdown — 3-5 specific points or 2-3 numbered/named segments. Each point is **mechanism + proof + specificity**.
- Open loop in the middle ("here's the part most people miss" / "but the real lesson is downstream") — keeps dwell past the 8-second threshold
- Named mechanism — if the operator owns or is willing to own the name, surface it. Names compound.
- Contrarian close — one sharp sentence that lands the lesson and contrasts the two paths

**Proof post body shape:**
- Anonymized client/operator profile in 3-5 specific bullets (per `INVARIANTS.md` N-3, never invent — pull from `case_studies.agency_case_studies[*]` with `use_in_content: true`)
- Wrong-diagnosis line ("the problem wasn't X. The problem was Y.")
- The mechanism (numbered/named, each step with the why-anchor)
- After-metrics with specific time window
- Pattern-break insight, then quotable truth, then sharp landing

**Personality post body shape:**
- Time-stamped or scene-immediate opener if loop-open hook
- Past/present contrast structure if transformation arc
- Named misdiagnosis — vulnerability disclosure that creates intimacy ("I thought it was about X. It wasn't. It was about Y.")
- Pattern extension ("I notice this in every operator I work with") — moves the post from anecdote to applicable lesson
- Scene-return closer — closes the loop with the new behavior

**Offer-bridge post body shape:**
- Stake number (raw data, the credibility lock)
- Standard-advice statement → reframe → mechanism breakdown → gap statement
- The offer as a natural continuation of the gap, not a separate sales block
- Comment-trigger CTA + qualification-in-personality close

**Pier post body shape:**
- Provocation framed as a thesis with stakes (not a contrarian pose)
- 3-5 evidence anchors
- Invite-the-fight close (the wrong reader bounces; the right reader argues — both lift comment volume)

**Body-construction non-negotiables (from virality-engine.md):**

- Single-sentence paragraphs throughout (≤2 sentences per paragraph max)
- Line breaks every 8-15 words
- One specific number, name, or proof anchor per ~50 words
- Body length per post type: Personality 200-500 chars, Authority 800-1500, Proof 1500-3000
- Zero hashtags or 1 hashtag max (hashtag-stacks suppressed)
- Zero external links in body — move to first comment
- Tags ≤1 (in comment, never body)
- Emoji 0-3 max (emoji walls suppressed)

### Step 6 — CTA construction (3 min)

CTA shape is determined by pillar:

| Pillar | CTA type | Pattern |
|---|---|---|
| **Authority** | Soft frame | Implicit invitation in the closer ("the post they were already writing in their head"). Comments self-trigger. No explicit ask. |
| **Proof** | Social-proof prompt | "If you've seen this pattern in your own pipeline, drop a line — curious what shape it took for you." |
| **Personality** | Identity prompt | "Anyone else recognize this in themselves?" — calibrated to the operator's voice (avoid "agree or disagree?" and other banned engagement-bait closers). |
| **Offer-bridge** | Application gate OR comment-trigger | "Comment 'PLAYBOOK' for the doc" (comment-trigger, per virality-engine #5) OR "I have 3 spots open this month for the program. Reply 'INFO' for details." (application gate). |
| **Pier** | Engagement provocation | A specific binary question that asks the reader to take a position. Calibrated to invite real disagreement, not generic "thoughts?" |

Banned closers (per `spec/BANNED-VOCABULARY.md`): "Thoughts?", "Agree or disagree?", "What do YOU think?", "Drop a comment below 👇", "Tag someone who needs this!", any closer that leads with an emoji directional arrow. The lint pass catches these.

### Step 7 — Publish-bar gates (mandatory, ordered) (5 min)

Run gates in this exact order. Halt and rework on first failure.

**Gate 1: Banned-vocab sweep.** Regex match the post (hook + body + CTA) against `spec/BANNED-VOCABULARY.md`. Zero hits required. Match = halt + rewrite the offending phrase, re-run gate.

**Gate 2: `/score-hook` ≥75.** The hook score from Step 4 must hold. If the hook was rewritten between Step 4 and Step 7, re-score.

**Gate 3: Blind Output Test.** Per `spec/BLIND-OUTPUT-TEST.md`, generate 3 simulated reviewer profiles matched to the target ICP segment from Step 3:
- Reviewer 1: Skeptical-experienced (B2B founder who has hired and fired 2+ LinkedIn agencies)
- Reviewer 2: Curious-novice (B2B founder evaluating LinkedIn-content agencies for the first time)
- Reviewer 3: Time-pressed-decisive (founder who skims for 15 seconds then decides whether to keep reading)

Each reviewer scores: Source attribution (human or AI?), Specificity (credible?), Voice fit (peer or vendor?), Decision-readiness (next action clear?), Conviction induction (would they take it?). **Pass criteria:** all 3 reviewers say "human operator" on source attribution; ≥4 of 5 axes pass for ≥2 of 3 reviewers. Per `INVARIANTS.md` N-10 — no exemption.

**Gate 4: Personality voice-fit check (≥85).** Read the post against `phrases_to_use`, `phrases_to_avoid`, `tone_framework`, `personality_traits`. Does the post sound like the operator? Score 0-100. Below 85 = rewrite the failing sections in operator voice; re-check. The voice-fit threshold for agency-side posts is 85; for client-side it climbs to the client's `voice_drift_threshold` (typically ≥0.82 cosine similarity).

If any gate fails twice, escalate to operator with a brief explaining which gate, why, and what upstream encoding gap likely caused it.

### Step 8 — Output emission (5 min)

Emit the post draft, the metadata block, and (if requested) variants. Write to `outputs/{date}/posts/agency/{post-slug}.md`.

## Output format

```yaml
---
artifact_type: linkedin-post-draft
scope: agency-post
client_slug: null
post_slug: <kebab-case-from-hook-or-topic>
date: YYYY-MM-DD
pillar: Authority | Proof | Personality | Offer-bridge | Pier
post_type: <post type from content engine>
hook_formula: <1 of the 9 from hook-anatomy.md>
target_virality_mechanism: <1+ of pier | dwell | comment-reply-harvest | cross-post-compound | comment-trigger>
predicted_engagement_band: low | mid-baseline | top-quartile | top-decile-shot
target_icp_segment: <segment from agency_icp>
target_awareness_level: <Unaware | Problem-aware | Solution-aware | Product-aware | Most-aware>
conversion_bridge: <named bridge from content engine>
publish_window: <Tue/Wed/Thu 8:30-10:30am operator-local — or specified override>
gates_passed:
  banned_vocab: true
  hook_score: <NN>/100
  blind_output_test: passed | conditional-pass
  voice_fit_score: <NN>/100
profiles_used: [brand_voice.agency_voice, content_strategy.agency_content, ideal_customer_profile.agency_icp]
frameworks_used: [hook-anatomy.<formula-slug>, virality-engine.<mechanism-slug>, pillar-architecture.<pillar>]
swipe_file_calibrated_against: [<path/to/swipe-file/post>, ...]
confidence: HIGH | MEDIUM | LOW
repurpose_flags:
  x_thread: true | false
  ig_carousel: true | false
  youtube_short: true | false
---

## The post (publish-ready text — copy into LinkedIn as-is)

<HOOK LINE>

<BODY — formatted exactly as it should appear, with the line breaks the post needs>

<CTA LINE>

## First-comment block (if external link, lead-magnet trigger, or reference)

<the link or context that goes in the first comment, NOT the body>

## Pier mechanism context (if applicable)

- Source post: <link or anonymized descriptor>
- Comment placed at: <timestamp>
- Comment text: <verbatim>
- This post publishes within: <hours-from-pier-comment>

## Variants (3 alternative hooks, if requested)
- Variant A — <formula>: <hook line>
- Variant B — <formula>: <hook line>
- Variant C — <formula>: <hook line>

## Operator notes
- Why this hook over alternatives: <1-2 lines>
- Why this CTA shape: <1-2 lines>
- Predicted top-3 commenter archetypes + reply seeds (per virality-engine mechanism #3)

## Post-publish protocol
1. Block 60 minutes for comment-reply harvest; reply substance, no "thanks"
2. Second-wave reply pass at 4-8 hours; pin most insightful comment by hour 24
3. Log 72-hour metrics to `outputs/{date}/posts/agency/{post-slug}.metrics.yaml`
4. If the post hits ≥2 swipe-file promotion signals (>50K impressions, >300 reactions, >50 substantive comments, >20 reshares, attributed business outcome), queue for `/promote-to-swipe-file` after the 30-day window
```

## Verification checklist (run before declaring done)

1. Slot context loaded from calendar — pillar + post-type + bridge are explicit, not improvised
2. Voice profile hydrated — `phrases_to_use` reached for, `phrases_to_avoid` checked against
3. ICP framing hypothesis written — reader-state, awareness level, limiting belief specified
4. Hook scored ≥75/100 against the 5-axis rubric via `/score-hook`
5. Body honors first-12-impression bet — single-sentence paragraphs, line breaks every 8-15 words, specific numbers/names, body length within pillar range
6. CTA shape matches pillar; no banned-vocab closer
7. Banned-vocab regex sweep returns zero hits
8. **Blind Output Test passed** (≥2 of 3 simulated reviewers say "human operator," per `INVARIANTS.md` N-10 and `spec/BLIND-OUTPUT-TEST.md`)
9. Voice-fit score ≥85
10. Output emitted to `outputs/{date}/posts/agency/{post-slug}.md` with full metadata + repurpose flags

## Common failure modes

- **"Hook scored 60/100 — not stop-scroll-able."** → Specificity weak. Add a verbatim number, named mechanism, or named role from the operator's actual data. Generic specificity ("a lot of money," "many founders") never scores high.
- **"Body reads like every other LinkedIn agency post."** → Voice profile hydration shallow OR `phrases_to_use` not reached for. Re-load voice; rewrite using verbatim operator constructions.
- **"Blind Output Test failed — reviewer 2 said AI."** → Specificity in body too thin. Pull a real anecdote, real number, real verbatim phrase from the operator's story bank or sales-call transcripts. Generic = AI signal.
- **"Post hit publish-ready but missed the calendar pillar."** → Slot context skipped at Step 1. Re-run from Step 1; the pillar is a constraint, not a suggestion.
- **"Operator rejected the post — 'doesn't sound like me.'"** → Voice-fit gate ran but threshold was too low. Per `INVARIANTS.md` A-9, log the rejection signal: which line broke voice, append `phrases_to_avoid`, adjust `tone_framework` if pattern recurs.

## Cross-references

- `INVARIANTS.md` N-1, N-4, N-6, N-9, N-10, A-1, A-2, A-3, A-4, A-8, A-9, A-10 — voice scope, banned vocab, draft language, Blind Output Test, Three-Lens Audit, citation, confidence, rejection-as-signal
- `spec/BANNED-VOCABULARY.md` — the lint list
- `spec/BLIND-OUTPUT-TEST.md` — publish-bar gate (mandatory per N-10)
- `spec/CONTEXT-THRESHOLDS.md` — founder-post thresholds
- `reference/frameworks/content/hook-anatomy.md` — 9 formulas + 5-axis rubric + decay cadence
- `reference/frameworks/content/virality-engine.md` — first-12-impression bet, 5 mechanisms, viral-post anatomy
- `reference/frameworks/content/pillar-architecture.md` — pillar-design protocol
- `reference/swipe-file/posts-linkedin/_INDEX.md` — 11 annotated examples indexed by pillar × hook formula
- `reference/swipe-file/posts-linkedin/authority/01-contrarian-reframe-saas-positioning.md` — Authority calibration
- `reference/swipe-file/posts-linkedin/proof/04-before-after-client-transformation.md` — Proof calibration
- `reference/swipe-file/posts-linkedin/personality/06-loop-open-day-in-life.md` — Personality calibration
- `reference/swipe-file/posts-linkedin/offer-bridge/10-hook-loop-cta.md` — Offer-bridge calibration
- `agents/marketing-head.md` — owns this skill's gate enforcement
- `skills/build-content-engine/SKILL.md` — upstream spec this skill consumes
- `skills/extract-founder-voice/SKILL.md` — upstream voice profile
- `skills/score-hook/SKILL.md` (Cycle 2 — TBD) — runs the 5-axis rubric
- `skills/build-hook-library/SKILL.md` (Cycle 2 — TBD) — populates `proven_hooks` for Path A
- `skills/repurpose-to-x/SKILL.md` / `skills/repurpose-to-ig/SKILL.md` — consume this post per `repurpose_flags`
- `skills/ghostwrite-client-post/SKILL.md` — recursive-context companion for client-side posts

## Examples

See `examples/` (Cycle 2 populates):
- `example-agency-post-authority-contrarian-reframe.md`
- `example-agency-post-proof-before-after.md`
- `example-agency-post-personality-loop-open.md`
- `example-agency-post-offer-bridge-comment-trigger.md`

---

*Skill version 1.0.0 — 2026-05-03*
