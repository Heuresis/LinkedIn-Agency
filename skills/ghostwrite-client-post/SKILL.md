---
name: ghostwrite-client-post
slug: /ghostwrite-client-post
agent: linkedin-ghostwriter
department: marketing
output_format: client-post-draft
required_profiles:
  brand_voice.client_voices[{client_slug}]: 85
  content_strategy.client_content[{client_slug}]: 70
  ideal_customer_profile.client_icps[{client_slug}]: 60
allowed_scopes:
  - client-post
output_path:
  client-post: outputs/{date}/posts/clients/{client_slug}/{post-slug}.md
---

# /ghostwrite-client-post — Ghostwritten LinkedIn Post (Client Account)

> **Purpose.** Write a single LinkedIn post on behalf of a CLIENT. The post is ghostwritten under the client's name, lands in the client's audience's feed, and represents the client's voice — not the agency's. Output is publish-ready, has passed voice-drift cosine-similarity check against the client's last 20 validated posts, has passed the Blind Output Test calibrated to the client's persona, and lands in the operator/account-manager review queue per `INVARIANTS.md` A-6.
>
> **Bar.** A reader inside the client's audience cannot tell the post was ghostwritten. The client themselves reads the post and ships it with <10% rewrite. If either fails, the encoding is wrong (voice profile thin, hook library thin, content sourcing protocol leaky) — fix the encoding, never lower the bar.
>
> **The single most common churn driver in LinkedIn ghostwriting is "this doesn't sound like me."** This skill is the line of defense.

## Scope — recursive context, locked

This skill is **client-side only**. Every profile load is recursively scoped to a single `client_slug`:

- `brand_voice.client_voices[{client_slug}]` — never `agency_voice`, never another client
- `ideal_customer_profile.client_icps[{client_slug}]` — never `agency_icp`
- `content_strategy.client_content[{client_slug}]` — never `agency_content`
- `case_studies` — only entries flagged for this `client_slug` AND `use_in_content: true`

**Cross-contamination is the failure mode this skill exists to prevent** (per `INVARIANTS.md` N-1, N-6). If the request omits `client_slug`, the skill refuses. If `client_slug` does not match an active client subprofile, the skill refuses and surfaces the missing onboarding step. If profile completeness for the named slug is below threshold, the skill halts and routes to `/extract-founder-voice scope=client-voice client_slug=<slug>` per `spec/CONTEXT-THRESHOLDS.md`.

## When to invoke

- Calendar slot fires for a client account (per `content_strategy.client_content[slug].calendar_4_week_template`)
- Client surfaces real-time material via the content-sourcing protocol (DM, voice note, call quote, deal context) that maps to a calendar slot or a real-time pier opportunity
- Operator/AM requests an off-calendar client post tied to a real event in the client's pipeline
- Client requests a specific post type they want to ship this week
- Variant generation requested (operator wants 2-3 hook alternatives tuned to the client's voice for the same body)

## Required inputs

1. **`client_slug`** — declared in the request, matching an active client subprofile
2. **Client voice profile** ≥85% (from `/extract-founder-voice` scope=client-voice for this slug) — the threshold for client-side is higher than agency-side because cross-contamination risk is higher
3. **Client content strategy** ≥70% (from `/build-content-engine` scope=client-content for this slug)
4. **Client ICP** ≥60% (from `/build-icp` scope=client-icp for this slug)
5. **Calendar slot context** — pillar, post-type, topic, target ICP segment, conversion bridge — pulled from this client's calendar
6. **Content sourcing material** — raw input from the client per their `content_sourcing_protocol`. Without raw client material, the post is ghostwriter-extrapolation, which drifts from client voice within 3 posts.
7. **Client's last 20 validated posts** — the cosine-similarity benchmark for the voice-drift gate
8. **Client's vertical file** — `reference/verticals/{client_vertical}.md` must exist (per `INVARIANTS.md` N-11). If vertical is un-encoded, halt and trigger `/build-vertical-profile` first.

## Build sequence

### Step 1 — Recursive context lock (3 min)

The first action of this skill is a **scope-lock check**. The agent loads `client_slug` from the request and binds it to the working context. Every subsequent profile load uses the path `<profile>.<branch>[<client_slug>]` — never the agency-level path.

Output a lock-confirmation line at the top of working context:

```
SCOPE LOCK: client-post
client_slug: <slug>
brand_voice loaded: client_voices[<slug>] ✓ (NOT agency_voice)
icp loaded: client_icps[<slug>] ✓ (NOT agency_icp)
content strategy loaded: client_content[<slug>] ✓ (NOT agency_content)
agency_voice access: BLOCKED for this build
```

If the working context attempts to read `agency_voice` at any later step, the skill halts with `RECURSIVE_SCOPE_VIOLATION`. Per `INVARIANTS.md` N-1, A-6.

### Step 2 — Client voice hydration (5 min)

Load the full client voice subprofile. Hold these fields in working context for the entire build:

- `client_slug`, `vertical`, `role` (the client's stated role — the ghostwritten post writes from this role's perspective, not the agency operator's)
- `voice_extraction_status` — must be `extracted-validated` or `sharpened` (not `interview-pending` or `extracted-draft`)
- `communication_style` (e.g., `technical-with-edge` for a B2B SaaS founder, `storytelling-warm` for an executive coach, etc. — every client is calibrated independently)
- `tone_framework.primary` + `tone_framework.secondary`
- `personality_traits` (3-5 traits — every line checkable against these)
- `language_patterns` (the client's actual cadence — sentence length, paragraph density, signature constructions)
- `phrases_to_use` (10-25 verbatim from the client's posts, calls, DMs, voice notes)
- `phrases_to_avoid` (10-25 — including the Q9 answers from voice extraction: "what would make you say 'this isn't me'")
- `pet_peeves` (what this client refuses to say or sound like — auto-blocked)
- `persuasion_style` (the client's structural bias — `authority-by-proof` / `story-first` / `mechanism-first` / `contrarian-provocation`)
- `story_bank` — only stories with `proof_anchors` cleared by the client (no inventing stories the client has never told)
- **`decision_frames` the client uses** — how the client thinks about pricing, hiring, qualification, churn. Posts ghostwritten in client voice must respect these frames; a frame mismatch = "this isn't how I think about that."
- `voice_drift_threshold` (typically ≥0.82 cosine similarity vs. validated baseline)

The agency's `phrases_to_use` and the agency operator's signature constructions are explicitly NOT loaded for this build. Per N-6.

### Step 3 — Client ICP framing (3 min)

The target reader is the **client's ICP**, not the agency's ICP. Pull from `ideal_customer_profile.client_icps[<slug>].voice_of_customer`:

- The client's ICP pain language — verbatim from the client's sales calls, DMs, application forms (not the agency's pains, not the agency's ICP's pains)
- The client's ICP desire language
- The client's ICP awareness level for this slot's topic
- The client's ICP dominant limiting belief
- The client's ICP objections — for posts that pre-handle one

Output a 1-line **client-reader-state hypothesis**: "VP Engineering at a 50-200 person scale-up, Solution-aware on the operational-pain axis the client's product solves, scrolling at 10pm Tuesday after a flat week of incidents." This is the calibration target for hook + body. Wrong hypothesis = right post for the wrong reader = high impressions on the client's account but no inbound calls in the client's pipeline.

### Step 4 — Client content strategy load (3 min)

Pull this slot's spec from `content_strategy.client_content[<slug>]`:

```yaml
slot_context:
  date: YYYY-MM-DD
  weekday: Mon | Tue | Wed | Thu | Fri
  pillar: <from this client's pillars — NOT the agency's>
  post_type: <from this client's post-type library>
  topic_cluster: <from this client's pillar topic_clusters>
  target_icp_segment: <from this client's ICP segments>
  conversion_bridge: <from this client's conversion bridges — bridges to the CLIENT's offer, not the agency's offer>
  draft_cue: <topic prompt — from this client's calendar OR client-supplied seed>
  cross_post_compound_position: standalone | day-1-text | day-2-carousel | day-3-video
  ghostwriter_assigned: <internal role — for the approval queue routing>
```

The pillar mix for this client is whatever was set in this client's `/build-content-engine` run — it is NOT the agency's pillar mix. Solution-aware client ICPs run heavier Proof + Authority; Problem-aware client ICPs run heavier Personality + Pier.

### Step 5 — Hook selection or generation (8 min)

Two paths, identical in structure to `/write-linkedin-post` but scoped to the client:

**Path A — pull from this CLIENT's proven library.** `content_strategy.client_content[<slug>].hook_library.proven_hooks`. If a proven hook fits the slot and rotation rules allow, prefer it. Adapt specifics; preserve structure.

**Path B — generate via the 9 formulas.** Per `reference/frameworks/content/hook-anatomy.md`. Pillar → formula mapping is the same default (Authority → Contrarian-reframe / Named-mechanism / Insider-secret, etc.) BUT the formula's filling — the verbatim phrasing, the cadence, the proof anchor — comes from the client's voice profile, never the agency operator's defaults.

Generate **3 hook candidates**. Score each via `/score-hook` against the 5-axis rubric. **Critical: the Voice-match axis is scored against the CLIENT's voice profile, not the agency operator's.** A hook can score perfectly on the agency operator's voice and fail entirely on the client's. Threshold: combined score ≥75 / 100 against the client's voice. Below threshold = regenerate.

The selected hook gets calibrated against the client's voice profile one more time:
- Does it use a `phrases_to_use` from the client's list (not the agency's)?
- Does it avoid every `phrases_to_avoid` and `pet_peeves` from the client's list?
- Does the cadence match the client's `language_patterns`?
- Does the implicit promise respect the client's `decision_frames`?

### Step 6 — Body construction (15 min)

Body construction follows `reference/frameworks/content/virality-engine.md` first-12-impression bet and dwell-time engineering, identical to `/write-linkedin-post`. Pillar-shape defaults are the same. The substitution is in the **voice + proof + frame**:

- **Mental models:** the post uses the client's mental models, not the agency operator's. If the client thinks of the buyer as "the practitioner who will get paged at 2am," the post uses that frame; the agency operator's "the founder who hates ghostwriting agencies" frame is irrelevant here.
- **Proof anchors:** the post uses the client's case studies, the client's metrics, the client's customer quotes — never the agency's. Per `INVARIANTS.md` N-3, no inventing. If the client's proof bank is thin, the post structure shifts to one that needs less proof (Personality / Pier) until the proof bank fills.
- **Vocabulary:** the post uses the client's vocabulary at every choice point. The ghostwriter's defaults — phrases the ghostwriter likes, transitions the ghostwriter relies on, opinions the ghostwriter holds — are explicitly suppressed.
- **Decision frames:** if the post takes a position (Authority / Pier), the position is one the client actually holds. Inventing a "contrarian take" the client wouldn't sign their name to is fast-track to client churn.
- **Story shape:** if Personality pillar, the story comes from the client's story bank — proof_anchors verified, story_type matched. The ghostwriter does not extrapolate stories the client has never told.

**Body-construction non-negotiables (per virality-engine.md, identical to `/write-linkedin-post`):** single-sentence paragraphs (≤2 max), line breaks every 8-15 words (calibrated to client's `language_patterns`), one specific number/name per ~50 words pulled from this client's data, body length per pillar adjusted to client's typical post length, ≤1 hashtag, no external links in body, ≤1 tag (in comment), ≤3 emoji — UNLESS client's voice profile explicitly notes a different convention they hold.

### Step 7 — Pre-publish gates (mandatory, ordered) (10 min)

Run gates in this exact order. Halt and rework on first failure. **No gate is skippable for client-bound work.**

**Gate 7a: Banned-vocab sweep.** Regex match against `spec/BANNED-VOCABULARY.md`. Zero hits required. Match = halt + rewrite the offending phrase, re-run gate.

**Gate 7b: `/score-hook` ≥75 against this CLIENT's voice.** Hook scored against the client's voice profile, not the agency's. If the hook was rewritten between Step 5 and Step 7, re-score.

**Gate 7c: Voice-drift cosine-similarity check.** Per `skills/voice-drift-detector/SKILL.md` (Cycle 2 — TBD). Compute cosine similarity between:
- The candidate post embedding
- The centroid embedding of this client's last 20 validated posts (the validated baseline)

The score must be ≥ this client's `voice_drift_threshold` (typically 0.82). Below threshold = halt; the post has drifted toward generic-LinkedIn or toward the agency operator's voice. Mandatory per `INVARIANTS.md` N-2 — no client artifact ships without this check.

If drift is below threshold:
- Surface the specific lines that pulled the score down (highest-divergence sentences vs. baseline)
- Rewrite those lines using verbatim client phrasing from `phrases_to_use` or directly from the client's last-20 corpus
- Re-run cosine check
- If still below threshold after 2 rewrites, halt and escalate — the voice profile or the ghostwriter's calibration is the issue, not this single post

**Gate 7d: Blind Output Test (3 simulated reviewers).** Per `spec/BLIND-OUTPUT-TEST.md`. The simulated reviewers are matched to the client's intended reader, NOT the agency's reader:
- Reviewer 1: Skeptical-experienced — a peer in the client's ICP segment who has read the client's prior posts
- Reviewer 2: Curious-novice — a target reader new to the client's content
- Reviewer 3: Time-pressed-decisive — a target reader who skims for 15 seconds then decides

The Source-attribution question becomes: **"Does this read like the client wrote it, or like a ghostwriter wrote it?"** Pass criteria: ≥2 of 3 reviewers say "the client wrote this." Per `INVARIANTS.md` N-10 and the client-side amplification of the standard test.

**Gate 7e: Operator/account-manager review queue.** Per `INVARIANTS.md` A-6 — never auto-publish without human review for client-bound artifacts. Queue entry includes: the post draft, voice-drift score, Blind Output Test detail, this client's last 5 validated posts (calibration reference), conversion bridge, 2-3 hook variants. Reviewer is the operator OR the account-manager for this client (per `agents/account-manager.md` (Cycle 2 — TBD) routing). Reviewer approves (→ client final approval → publish), requests revision (→ loop to Step 5/6), or rejects (→ escalate to voice profile re-extraction if pattern recurs across 2+ posts in 30 days).

### Step 8 — Output emission (3 min)

Emit the post draft, the metadata block, the variants, and the approval queue entry. Write to `outputs/{date}/posts/clients/{client_slug}/{post-slug}.md`.

## Output format

```yaml
---
artifact_type: client-post-draft
scope: client-post
client_slug: <slug>
post_slug: <kebab-case-from-hook-or-topic>
date: YYYY-MM-DD
client_role: <client's role from voice profile>
client_vertical: <client's vertical>
pillar: Authority | Proof | Personality | Offer-bridge | Pier
post_type: <post type from this client's content engine>
hook_formula: <1 of the 9 from hook-anatomy.md>
target_virality_mechanism: <1+ of pier | dwell | comment-reply-harvest | cross-post-compound | comment-trigger>
predicted_engagement_band: low | mid-baseline | top-quartile | top-decile-shot
target_icp_segment: <segment from this client's ICP — NOT agency ICP>
target_awareness_level: <Unaware | Problem-aware | Solution-aware | Product-aware | Most-aware>
conversion_bridge: <bridge to this CLIENT's offer — NOT the agency's offer>
publish_window: <client's calendar window>
recursive_scope_lock: client_voices[<slug>] (verified — agency_voice NOT loaded)
gates_passed:
  banned_vocab: true
  hook_score: <NN>/100 (vs this client's voice)
  voice_drift_score: <0.NN> (threshold: <0.NN>)
  blind_output_test: passed | conditional-pass
  approval_queue_entry: created
profiles_used: [brand_voice.client_voices[<slug>], content_strategy.client_content[<slug>], ideal_customer_profile.client_icps[<slug>]]
frameworks_used: [hook-anatomy.<formula-slug>, virality-engine.<mechanism-slug>, voice-drift-detector]
swipe_file_calibrated_against: [<path/to/swipe-file/post>, ...]
last_20_validated_posts_referenced: true
ghostwriter_assigned: <internal role>
reviewer_assigned: <operator | account-manager-slug>
review_status: pending-operator-or-am-review
confidence: HIGH | MEDIUM | LOW
---

## The post (publish-ready text — for client final approval, then publish under client account)

<HOOK LINE>

<BODY — formatted exactly as it should appear on the client's LinkedIn>

<CTA LINE>

## First-comment block (if external link, lead-magnet trigger, or reference)

<the link or context that goes in the first comment, NOT the body>

## Voice-drift detail

- Cosine similarity score: <0.NN>
- Threshold: <0.NN>
- Centroid baseline: last 20 validated posts (collected through <date>)
- Highest-divergence sentence (for reviewer eye): <sentence + score>
- Lowest-divergence sentence (the most on-voice line): <sentence + score>

## Blind Output Test result

- Reviewer 1 (Skeptical-experienced peer in client ICP): <verdict per axis>
- Reviewer 2 (Curious-novice target reader): <verdict per axis>
- Reviewer 3 (Time-pressed-decisive target reader): <verdict per axis>
- Aggregate: <hard-pass | conditional-pass>

## Approval queue entry
- Queued for: <reviewer> at <timestamp>; SLA: <window>; decision required: approve | request-revision | reject
- Reference materials attached: client's last 5 validated posts, voice profile link, pillar spec, conversion bridge spec

## Variants (2-3 alternatives, tuned to this client's voice)
- Variant A — <formula>: <hook line>
- Variant B — <formula>: <hook line>
- Variant C — <formula> (optional): <hook line>

## Ghostwriter notes
- Why this hook (vs. this client's voice): <1-2 lines>
- Verbatim client phrases reached for; pet-peeves auto-blocked; decision-frames respected
- Predicted top-3 commenter archetypes (in client's audience) + reply seeds for harvest

## Post-publish protocol (after client final approval)
1. Block 60 minutes for comment-reply harvest — replies in client voice, NOT ghostwriter voice
2. Reply to every comment in first 60 minutes (substance, no "thanks"); second-wave at 4-8 hours; pin best by hour 24
3. Log 72-hour metrics to `outputs/{date}/posts/clients/{client_slug}/{post-slug}.metrics.yaml`
4. If post hits ≥2 swipe-file promotion signals, queue for `/promote-to-swipe-file` (anonymized) after 30-day window
5. Add this post to next weekly `/voice-drift-detector` audit batch
```

## Verification checklist (10 items, run before declaring done)

1. **Recursive scope correct**: `client_voices[<slug>]` loaded; `agency_voice` NOT loaded for this build (per `INVARIANTS.md` N-1, N-6, A-6)
2. **Banned-vocab sweep**: zero hits against `spec/BANNED-VOCABULARY.md`
3. **Hook score ≥75/100** against this CLIENT's voice profile (not the agency's), per `reference/frameworks/content/hook-anatomy.md` 5-axis rubric
4. **Voice-drift score** ≥ this client's `voice_drift_threshold` (typically 0.82 cosine vs. last 20 validated posts) — mandatory before publish per `INVARIANTS.md` N-2
5. **Blind Output Test passed**: ≥2 of 3 simulated reviewers say "the client wrote this," per `spec/BLIND-OUTPUT-TEST.md` and `INVARIANTS.md` N-10
6. **ICP framing matches this client's ICP**, not the agency's — reader-state hypothesis written from `client_icps[<slug>].voice_of_customer`
7. **Pillar matches this client's calendar slot**, not the agency's pillar mix
8. **CTA matches this client's offer**, not the agency's offer (the conversion bridge points to the client's pipeline)
9. **Approval queue entry generated**, routed to operator or assigned account-manager per `agents/account-manager.md` (Cycle 2 — TBD), never auto-publish per `INVARIANTS.md` A-6
10. **No real names** of any non-client entities accidentally included (anonymized methodology descriptors only per `INVARIANTS.md` A-11) — and no AI/Claude attribution anywhere per N-14

## Common failure modes

| Symptom | Diagnosis → Action |
|---|---|
| "Sounds like the agency operator, not the client" | Wrong voice scope; recursive context error; `agency_voice` leaked in. → Halt; re-run from Step 1 with explicit `client_slug`; verify SCOPE LOCK line; audit agent's persona overlay if recurring. |
| "Hook scored 60/100 against client voice" | Hook library too thin; formulas filled with ghostwriter phrasing not client phrasing. → Run `/build-hook-library` client-scoped; manually pull verbatim from client's last 20 posts in the meantime. |
| "Voice drift score 0.74 (below 0.82 threshold)" | Ghostwriter using template defaults; or client voice profile stale (>90 days since validation). → Halt; rewrite highest-divergence lines using client verbatim; if drift persists across 2+ posts in 30 days, retrain ghostwriter and queue voice re-extraction. |
| "Blind Output Test failed (2 of 3 detected ghostwriter)" | Body specificity weak; generic mechanism instead of client's actual mechanism; client mental models violated. → Pull verbatim client phrasing from call transcripts/DMs/voice notes; rewrite mechanism using client's actual frame; re-run. |
| "Approval queue empty after 48 hours" | Reviewer SLA breach; AM unassigned; routing misconfigured. → Escalate to `marketing-head`; verify `agents/account-manager.md` routing; never auto-publish to "unblock" — drift compounds silently. |
| "Client rejected — 'I'd never say that about [topic]'" | `decision_frames` violated; post took a position client doesn't hold. → Per A-9, log rejection: append violated frame to `phrases_to_avoid`; rebuild from a stance-respecting angle. |
| "Client surfaced no raw material — ghostwriter improvised" | Content-sourcing protocol leaky. → Halt this post; trigger `/client-content-sourcing-pulse`; ghostwriter-extrapolation past 3 posts = drift compounds + churn risk rises sharply. |

## Cross-references

- `INVARIANTS.md` N-1, N-2, N-3, N-4, N-6, N-9, N-10, N-11, N-13, N-14, A-2, A-3, A-4, A-5, A-6, A-9, A-10, A-11, A-12 — recursive scope, voice-drift mandatory, no inventing, banned vocab, never auto-publish, vertical-encoded, anonymization, citation, Blind Output Test, dependency chain, subprofile load-bearing, rejection-as-signal, weekly drift audit
- `spec/BLIND-OUTPUT-TEST.md` — publish-bar gate (mandatory per N-10, calibrated to client persona)
- `spec/BANNED-VOCABULARY.md` — the lint list
- `spec/CONTEXT-THRESHOLDS.md` — thresholds (client-voice ≥85, client-icp ≥60, client-content ≥70)
- `reference/frameworks/content/hook-anatomy.md` — 9 formulas + 5-axis rubric (Voice-match scored against client)
- `reference/frameworks/content/virality-engine.md` — first-12-impression bet, 5 mechanisms
- `reference/swipe-file/posts-linkedin/_INDEX.md` — calibration targets indexed by pillar × hook formula
- `reference/swipe-file/posts-linkedin/authority/01-contrarian-reframe-saas-positioning.md` — Authority calibration (structure transfers; voice substitutes)
- `reference/swipe-file/posts-linkedin/proof/04-before-after-client-transformation.md` — Proof calibration (strict N-3 — no inventing)
- `reference/swipe-file/posts-linkedin/personality/06-loop-open-day-in-life.md` — Personality calibration
- `reference/swipe-file/posts-linkedin/offer-bridge/10-hook-loop-cta.md` — Offer-bridge calibration (CTA → client offer, not agency)
- `reference/verticals/{client_vertical}.md` — must exist per `INVARIANTS.md` N-11
- `agents/marketing-head.md` — owns gate enforcement; escalation path
- `agents/account-manager.md` (Cycle 2 — TBD) — owns per-client approval queue routing per `INVARIANTS.md` A-6
- `skills/extract-founder-voice/SKILL.md` — upstream voice profile (`scope=client-voice`)
- `skills/build-content-engine/SKILL.md` — upstream content engine spec for this client
- `skills/build-icp/SKILL.md` — upstream client ICP
- `skills/voice-drift-detector/SKILL.md` (Cycle 2 — TBD) — runs cosine check at Gate 7c; weekly audit per A-12
- `skills/build-hook-library/SKILL.md` + `skills/score-hook/SKILL.md` (Cycle 2 — TBD) — client-scoped hook library + 5-axis rubric
- `skills/post-approval-tracker/SKILL.md` — the queue this artifact lands in; `skills/write-linkedin-post/SKILL.md` — agency-side companion (never confuse scopes)

## Examples

See `examples/` (Cycle 2 populates): `example-client-post-saas-founder-authority-named-mechanism.md`, `example-client-post-coach-personality-loop-open.md`, `example-client-post-services-founder-proof-before-after.md`, `example-client-post-recursive-scope-violation-recovery.md` (failure + fix).

---

*Skill version 1.0.0 — 2026-05-03*
