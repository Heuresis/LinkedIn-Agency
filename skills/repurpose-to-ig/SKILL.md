---
name: repurpose-to-ig
slug: /repurpose-to-ig
agent: content-strategist
department: marketing
output_format: instagram-carousel-or-reel-script
required_profiles:
  source_post_artifact: exists                   # the published LinkedIn post to repurpose
  brand_voice: 60                                # recursive: agency-voice OR client-voice for the source's subject
allowed_scopes:
  - agency-post-repurpose                        # source post = agency-content; voice load = agency_voice
  - client-post-repurpose                        # source post = client-content; voice load = client_voices[slug]
output_path: outputs/{date}/repurpose/ig/{post-slug}.md
---

# /repurpose-to-ig — LinkedIn Post → Instagram Adaptation

> **Purpose.** Take a single published LinkedIn post and adapt it to Instagram. Two output formats: **carousel** (8-12 slides, image + text per slide) OR **reel script** (15-60 second talking-head). Format choice driven by source post pillar + visual decomposability. NOT both — running both for the same idea on the same week is redundant and trains the IG algorithm to fragment your reach.
>
> **Bar.** A reader on Instagram who has never seen the LinkedIn original should consume the entire carousel (or watch the reel to the CTA frame) on its own merits. If the IG version reads as repurposed exhaust from "real" content, the adaptation has failed.
>
> **The recursive load.** Source post is `agency-content` → load `brand_voice.agency_voice`. Source post is `client-content` → load `brand_voice.client_voices[client_slug]`. Voice contamination across this seam is the same failure mode as N-6 — guarded by the same thresholds.

## When to invoke

- Top-decile LinkedIn post with strong visual decomposability (lists, before/after, named mechanism with steps)
- Authority pillar post with a named mechanism — IG carousel format compounds named-mechanism content via save rate
- Proof pillar case study with before/after metrics — carousel before/after slides hit IG save rate hard
- Personality pillar story moment — reel format if the subject is comfortable on camera
- A client retainer scope includes IG cross-distribution (specifically allow-listed in `product_strategy.service_tier.deliverables_per_month`)
- Specifically NOT for: dense long-form analysis posts (won't survive 12 slides without dilution), posts whose payoff is platform-context-dependent (e.g., LinkedIn algorithm commentary), posts where the subject has zero IG presence and no plan to build one (publishing IG content into a void wastes the production cost)

## Required inputs

1. **The source LinkedIn post artifact.** Body + metadata + hook + CTA + pillar + hook formula + scope. Same intake shape as `/repurpose-to-x`.
2. **Source post engagement data.** Used to gate-decide whether to repurpose at all (don't repurpose flops — see failure modes).
3. **Voice profile for the source's subject.** Agency or client voice per scope. ≥60% completeness gate.
4. **Subject's IG account context.** Handle, follower count, last 12 IG posts, top-3 performing IG posts (by save rate, not likes), brand visual style (color palette, typography, template patterns) if any. If no IG presence, default to clean editorial brand visual baseline + LinkedIn voice with IG-tone allowance (more conversational, see Step 6).
5. **Visual asset constraints** (for carousel): subject's brand template if one exists; subject's headshot/B-roll library if reel; designer/editor availability for the visual brief.

## Pre-flight gate (mandatory)

Before any drafting:

- Confirm the source artifact exists with hook, body, pillar, hook formula, scope.
- Confirm voice profile clears 60. Agency-scope = `brand_voice.agency_voice ≥ 60`. Client-scope = `brand_voice.client_voices[client_slug] ≥ 60`.
- Re-run `spec/BANNED-VOCABULARY.md` regex over the source body. Source contamination = halt + escalate (per `/repurpose-to-x` same protocol — repurposing carries contamination across channels).
- Declare context tier inline: `Context: Solid (75%) — agency_voice 82, source-post artifact loaded, ig-account context full.`

## The build sequence

### Step 1 — Source post intake (5 min)

Pull the source artifact and extract the working set (same shape as `/repurpose-to-x` Step 1):

```yaml
source:
  scope: agency-post-repurpose | client-post-repurpose
  client_slug: null | "<slug>"
  post_slug: ""
  pillar: ""                                     # Authority | Proof | Personality | Offer-bridge | Pier
  hook_formula: ""
  hook_text: ""
  body_word_count: 0
  named_mechanism: null | ""
  list_items_count: 0                            # if listicle / numbered structure, count items
  has_before_after_data: boolean                 # specific numbers from a transformation
  cta_type: ""
  cta_text: ""
  engagement_signal: { impressions, comments, saves }
  source_voice_phrases_present: []
```

The `list_items_count` and `has_before_after_data` fields drive the Step 2 format decision. Posts with 5+ list items naturally decompose into carousels. Posts with before/after numbers naturally decompose into split-frame slides or reel B-roll.

### Step 2 — Carousel vs. reel decision (5 min)

Use this decision tree. Pick exactly one. Running both for the same source post in the same week trains the IG algorithm to under-distribute both.

**Carousel (8-12 slides)** when:
- Source post is list-able: numbered points, sequential steps, named mechanism with components, before/after structure
- Pillar is Authority (mechanism breakdown), Proof (case study), or Offer-bridge (educational + CTA)
- The subject's IG audience indexes on save rate over watch time (carousels dominate save rate)
- The subject is camera-shy or unavailable for filming
- The source post has strong "snippetable" sentences that work as slide headers
- A list-promise hook source post (formula #5) almost always becomes a carousel

**Reel script (15-60 sec talking-head)** when:
- Source post is hook + insight + specific that can be delivered face-to-camera in <60 seconds
- Pillar is Personality, Pier, or a sharp Authority take with one mechanism
- The subject is comfortable on camera and has an existing reel cadence (existing reel views >3x carousel views)
- The source's emotional charge is high (loop-open hook, specific-stakes hook) — emotion lands harder in voice/face than text
- The source post is <300 words (reel attention budget is small)

**Neither (skip the repurpose)** when:
- The source post is purely textual reasoning with no visual or auditory dimension
- The subject has zero IG presence and no roadmap to build one (carousel/reel published into void)
- The source pillar is `Pier` and the value was the LinkedIn-comment-section conversation — IG has no equivalent surface

Output the decision with one-line justification:
> `decision: carousel (10 slides) — source is Authority pillar with named mechanism + 6 components; list-able; saves > watch on subject's IG audience`

### Step 3 — Carousel architecture (if carousel chosen) (20 min)

A carousel is 8-12 slides. Skip-rate is brutal — readers swipe past slide 2 if slide 1 doesn't hook AND slide 2 doesn't deliver the next promise. Treat each slide as a tweet-with-an-image.

**10-slide default architecture:** S1 hook slide (LinkedIn hook IG-tightened, title-card design, ≤8 words) → S2 pickup slide (implicit promise made explicit, ≤12 words) → S3-S8 mechanism/list/case-study slides (ONE point per slide, ≤15 words text, one visual element each) → S9 synthesis slide (named mechanism OR takeaway, ≤12 words) → S10 CTA slide (offer-bridge OR save-this, one direct ask).

**Optional 11th "save-this" slide** for Authority + Proof pillar carousels: "save this for [specific moment]." Save rate is the strongest IG distribution signal in 2026.

**Per-slide design rules:**
- ≤15 words text per slide (hard cap; sweet spot 6-12)
- ONE visual element per slide (icon, image, chart, before/after split). No collage clutter.
- Slide 1 typography ≥3x other slides — reads at thumbnail size
- High-contrast palette matching subject's brand identity (load from `business_context.brand_assets` or client equivalent)
- Body font 24pt+ mobile readability; no watermark bigger than smallest text; negative space around text (cluttered slides depress save rate)

**Per-slide text rule:** slide text must be self-complete. "Most B2B founders" leaves the reader stranded. "Most B2B founders write about their product. (swipe →)" completes a thought, sets the next.

For each slide deliver: `position`, `type` (hook/pickup/mechanism/proof/synthesis/cta/save-this), `text` (≤15 words), `visual_brief` (what the designer renders), and `reads_as_thumbnail` boolean for slide 1.

### Step 4 — Reel script (if reel chosen) (20 min)

15-60 second talking-head reel. First 3 seconds decide whether the viewer stays. Last frame (CTA) decides whether the viewer acts.

**Reel architecture:**

- **0:00–0:03 — HOOK FRAME.** Subject delivers a 4-7 word pattern-break line direct to camera. NO intro ("Hey guys" = instant scroll). NO logo card. Face fills the frame. Text overlay shows the hook line in 24pt+ (audio is muted on most IG views — overlay mandatory).
- **0:03–0:10 — SETUP.** One sentence that picks up the hook and sets the implicit promise. Subject can move (medium shot, b-roll, whiteboard). Overlay sync to spoken word.
- **0:10–0:35 — BEATS 1 + 2.** Two distinct mechanism beats, 10-15 sec each. Cut between subject + b-roll (screenshot, document, drawing, hand-gesture close-up). One specific number, name, or example per beat. Overlay on every beat.
- **0:35–0:50 — BEAT 3 / SYNTHESIS.** Named mechanism OR synthesis line. Subject delivers slowly with eye contact. Overlay holds 3+ seconds.
- **0:50–0:60 — CTA FRAME.** NOT verbal CTA — IG users skip the last 5 seconds on first view. CTA is VISUAL: frozen frame with offer/handle/redirect text, supported by the caption (Step 5). Hold 3-5 seconds.

**Reel rules:** total length 35-55 sec typical (15-25 sec for Personality moments, never >60). Vertical 9:16. Text overlay on every beat (no audio reliance). Subject's face on screen ≥60%. Cuts every 3-5 sec. B-roll cues specified for editor (not improvised on shoot day). Music: trending audio if brand allows; else neutral instrumental at low volume.

**Reel script artifact** delivers: `total_duration_sec` (15-60), `hook_frame` (duration, spoken line, overlay, framing), an array of `beats` (each with `beat_number`, `duration_sec`, `spoken`, `overlay`, `shot_type`, `b_roll_cue`), and `cta_frame` (duration, overlay text, optional spoken, framing).

### Step 5 — Caption draft (10 min)

The caption is where the IG-vs-LinkedIn voice difference lands hardest.

**Caption anatomy:** (1) **Hook in the first sentence** — IG cuts the caption at "...more" after ~125 chars. Sentence 1 must earn the click. Same hook formula as the post, IG-conversational. (2) **Body** (2-5 short paragraphs, line-broken every 1-2 sentences, IG-voice per Step 6). (3) **ONE clear CTA at end** — "Save this for your next launch" OR "DM PLAYBOOK and I'll send the doc" OR "Tap link in bio." Pick one. (4) **3-5 hashtags MAX, niche** — `#linkedinghostwriting` not `#marketing`; `#b2bfounders` not `#business`. Generic hashtags pull non-ICP traffic that depresses long-term distribution. Place at end OR in first comment (test which works for subject).

**Caption length:** 600-1200 chars sweet spot. <300 chars under-deliver context; >2000 chars get distribution-penalized. No emoji walls (1-3 emoji acceptable; emoji-as-bullet-list = banned per `spec/BANNED-VOCABULARY.md`).

### Step 6 — Voice fit (5 min)

IG voice for the same subject runs ~15-20% more casual than their LinkedIn voice. Same person, more conversational: contractions allowed and encouraged; more direct address ("you"/"your" frequency up); sentence fragments allowed ("Here's the thing. Saved 14 hours."); first-person dominant; stories framed personally even when the LinkedIn version was analytical; intentional lowercase mirrored only if the subject's IG account already uses it.

**What does NOT loosen:** subject's `phrases_to_avoid` (sacred across every channel); anti-positioning bans (no "LinkedIn guru" mocking, no "10X your" hype); banned vocab from `spec/BANNED-VOCABULARY.md`; any specific number, mechanism name, or transformation claim from the source (specifics survive the tone shift).

For client-scope, also load `brand_voice.client_voices[client_slug].phrases_to_avoid` and `pet_peeves`. A client who refuses to swear on LinkedIn refuses on IG; mirror their actual cross-platform behavior.

Run the entire IG artifact (slide text + caption OR reel overlays + caption) through the banned-vocab regex. Match = halt + rewrite per `INVARIANTS.md` N-4.

### Step 7 — Visual brief (10 min)

The visual brief is the contract with production (designer for carousel, editor for reel). Without it, visual production drifts and the artifact ships off-brand.

**Carousel visual brief** delivers: `template` (subject's brand template OR neutral editorial baseline), `color_palette` (hex array), `typography` (headline_font, body_font, headline_min_pt 24), per-slide `slide_specs` (`slide_position`, `visual_element` icon/illustration/before-after/screenshot/chart, `element_description`, `text_placement`, `negative_space_pct` ≥30), `brand_lock_elements` (logo_position, watermark_size, color_continuity), and `delivery_format` (PNG 1080x1080 or 1080x1350).

**Reel visual brief / shot list** delivers: `shoot_setup` (location, 9:16 framing, lighting, lavalier audio, background), `shot_list` (per shot: number, duration_sec, shot_type close-up/medium/b-roll, subject_action, b_roll_description), `text_overlay_specs` (font, color, background_treatment, sync_timing per word or phrase), `cta_frame_design` (layout, text, visual_emphasis), `music` (trending audio name or neutral instrumental at 20% volume under voice), `edit_pace` (cut every 3-5 sec), `delivery_format` (MP4 1080x1920, ≤60 sec).

If the carousel ships and slide 7 is missing the before/after split, the brief was thin — re-write the brief, not the slides.

### Step 8 — Output emission (5 min)

Hydrate the output artifact at `outputs/{date}/repurpose/ig/{post-slug}.md`:

```yaml
---
artifact_type: instagram-carousel-or-reel-script
scope: agency-post-repurpose | client-post-repurpose
client_slug: null | "<slug>"
source_post_path: "outputs/{date}/posts/{post-slug}.md"
source_pillar: ""
source_hook_formula: ""
adapted_format: carousel | reel
slide_count: integer | null
reel_duration_sec: integer | null
hook_score: { stop_scroll, specificity, voice_match, promise, filter }
caption_length_chars: integer
hashtag_count: integer
banned_vocab_sweep: passed | failed
voice_fit_check: passed | failed
predicted_save_rate_band: low | mid | high
predicted_completion_rate_band: low | mid | high
profile_completeness: 0-100
profiles_used: [brand_voice.{scope}, content_strategy.{scope}, business_context.brand_assets]
frameworks_used: [hook-anatomy, virality-engine, banned-vocabulary]
confidence: HIGH | MEDIUM | LOW
build_date: YYYY-MM-DD
---

## The IG artifact

[either the slide-by-slide carousel breakdown OR the timestamped reel script]

## Caption

[600-1200 char caption with hook first sentence + body + single CTA + 3-5 niche hashtags]

## Visual brief

[carousel brief OR reel shoot brief — Step 7 yaml]

## Source → IG mapping notes

- Hook: source `{hook_text}` → IG `{tightened_hook}` (formula preserved: {formula})
- Format choice: {carousel | reel} — justification from Step 2 decision tree
- Voice tone shift: {note on the LinkedIn-to-IG casualness calibration applied}
- CTA translation: source `{cta_text}` → IG `{ig_cta}`

## Predicted engagement (heuristic, not promise)

Save rate baseline {band} from subject's IG history + carousel norms. Reel completion rate {band} from length + hook strength. Profile-visit conversion {band}. Confidence {HIGH/MED/LOW} from voice completeness + IG-account context + production capacity.

## Production handoff

Designer (carousel) or editor (reel) assigned. Shoot/production deadline set. Approval workflow: operator review (agency-scope) OR client + account-manager review (client-scope).

## Next actions

Schedule at subject's top IG-insights hour. Pre-write 3-5 reply-comments for first-hour engagement harvest (carousel) OR pin top reply (reel). Track save rate at 24h + 7d; log delta vs. subject's baseline to feed cycle-N+1 calibration.
```

## Validation gate (mandatory before declaring done)

Three checks. All three must pass.

1. **Standalone test.** Show the IG artifact (no LinkedIn context, no caption first) to a reviewer who has never seen the source post. For carousel: would they swipe past slide 1? For reel: would they swipe past 0:03? Pass: yes, with one specific reason cited (the slide 1 hook, the reel hook frame).

2. **Voice match test.** Compare the IG artifact (slide text or reel overlays + caption) against the subject's last 12 IG posts. For client-scope, compare against `brand_voice.client_voices[client_slug]` with IG-tone allowance. Pass: indistinguishable from the subject in cadence, vocabulary, and tone — with the IG casualness shift visible vs. LinkedIn.

3. **Banned-vocab + score-hook gate.** The platform-adapted hook (slide 1 text OR reel hook frame line) scored ≥4 on the four primary axes (Stop-scroll, Specificity, Voice, Promise) and ≥3 on Filter via `/score-hook` (per `reference/frameworks/content/hook-anatomy.md` rubric). The full artifact (slides/script + caption + visual brief copy) passed the banned-vocab regex (LinkedIn list + client's `phrases_to_avoid` if `client-post-repurpose`).

If any of the three fails, return to Step 3/4 (architecture), Step 5 (caption), or Step 6 (voice). Two attempts max before escalating to the marketing-head with a brief on the specific failure pattern.

## Failure modes and guards

- **Slide-text bullet sprawl.** Slides averaging 25+ words = readers swipe out. Guard: hard cap 15 words per slide; if the source idea won't fit, add slides in Step 3 rather than enlarge them.
- **Reel verbal CTA at the end.** IG users skip the last 5 seconds on first watch. Guard: Step 4 mandates VISUAL CTA frame; verbal track is supplemental, not load-bearing.
- **Caption that buries the hook.** Sentence 1 is throat-clearing, real hook is sentence 4 — IG cuts at "...more" and sentence 4 never gets read. Guard: Step 5 rule that sentence 1 IS the hook.
- **Hashtag inflation.** 15 hashtags including generic ones to chase reach pulls non-ICP traffic. Guard: Step 5 hard cap 5 hashtags, all niche.
- **Carousel with no save-this affordance** on Authority/Proof pillars. Save rate underperforms. Guard: Step 3 optional 11th slide for those pillars.
- **Reel filmed without shot list.** Subject improvises, editor receives unstructured footage, the 45-sec cut loses the source structure. Guard: Step 7 visual brief is a hard prerequisite for production.
- **Format pick driven by operator preference, not source fit.** Guard: Step 2 decision tree is binding; subject preference is a tiebreaker only when the tree is ambiguous.
- **Repurposing into IG void.** Subject has 80 IG followers and no growth plan = production cost wasted. Guard: pre-flight gate confirms IG is in scope (clients: allow-listed in `product_strategy.service_tier.deliverables_per_month`; agency: stated IG growth thesis).
- **Client voice contamination.** Artifact accidentally loaded `agency_voice` instead of `client_voices[slug]`. Guard: pre-flight makes scope explicit; frontmatter logs the loaded voice; voice-drift cosine sample runs at Step 6.

## Cross-references

- `reference/frameworks/content/hook-anatomy.md` — hook formulas + scoring rubric (IG-tightened hook still uses one of the 9 formulas)
- `reference/frameworks/content/virality-engine.md` — LinkedIn-side mechanisms; IG analogs are carousel-save-rate engine + reel-completion-rate engine
- `agents/marketing-head.md` — owns the gate between content-engine outputs and repurpose artifacts
- `spec/BANNED-VOCABULARY.md` — regex applies to caption + slide text + reel overlays verbatim
- `spec/CONTEXT-THRESHOLDS.md` — `/repurpose-to-ig` row
- `INVARIANTS.md` N-1, N-4, N-6, N-9 — voice loading, banned vocab, contamination, no "final" claims
- `skills/build-content-engine/SKILL.md` — produces the source post this skill consumes
- `skills/write-linkedin-post/SKILL.md` (Cycle 2 — TBD) — agency-content source post producer
- `skills/ghostwrite-client-post/SKILL.md` (Cycle 2 — TBD) — client-content source post producer
- `skills/repurpose-to-x/SKILL.md` — sister skill for X (Twitter) adaptation
- `skills/score-hook/SKILL.md` — hook scoring runtime invoked in Step 3/4 + validation gate
- `reference/platforms/instagram/` — IG-specific algorithm notes (currently empty; rules in this skill default-apply)

## Examples

See `examples/` (to be populated):
- `example-agency-carousel-from-authority-post.md` — agency-content Authority pillar post → 10-slide carousel
- `example-agency-reel-from-personality-post.md` — agency-content Personality moment → 35-sec reel
- `example-client-carousel-from-proof-post.md` — client-content Proof pillar case study → 11-slide carousel with save-this slide
- `example-client-reel-from-pier-post.md` — client-content Pier post contrarian take → 25-sec reel

---

*Skill version 1.0.0 — 2026-05-03*
