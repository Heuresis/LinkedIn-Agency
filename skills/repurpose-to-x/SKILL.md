---
name: repurpose-to-x
slug: /repurpose-to-x
agent: content-strategist
department: marketing
output_format: x-post-or-thread
required_profiles:
  source_post_artifact: exists                   # the published LinkedIn post to repurpose
  brand_voice: 60                                # recursive: agency-voice OR client-voice for the source's subject
allowed_scopes:
  - agency-post-repurpose                        # source post = agency-content; voice load = agency_voice
  - client-post-repurpose                        # source post = client-content; voice load = client_voices[slug]
output_path: outputs/{date}/repurpose/x/{post-slug}.md
---

# /repurpose-to-x — LinkedIn Post → X (Twitter) Adaptation

> **Purpose.** Take a single published LinkedIn post and adapt it for X. NOT copy-paste. X has different reader expectations: faster scroll, no professional context priming, harder hook bar (3-5 words tighter), threading vs. single-tweet decision. Output is a publish-ready single tweet OR a 5-15 tweet thread.
>
> **Bar.** A reader on X who has never seen the LinkedIn original should engage with the X version on its own merits. If the X version reads as a downgraded LinkedIn post, the repurpose has failed.
>
> **The recursive load.** Source post is `agency-content` → load `brand_voice.agency_voice`. Source post is `client-content` → load `brand_voice.client_voices[client_slug]`. Voice contamination across this seam is the same failure mode as N-6 — guarded by the same thresholds.

## When to invoke

- Top-decile LinkedIn post (>2x median impressions for the subject) — repurpose-to-X to compound reach
- Authority pillar post with a named mechanism — X audience indexes hard on named-mechanism content
- Pier post or contrarian-reframe post that landed — X's quote-tweet ecology amplifies contrarian angles
- After a cross-post compound day-1 (per `virality-engine.md` mechanism #4) — X version becomes day-4 of the compound
- A client requested cross-platform distribution and the X presence is part of their retainer scope
- Specifically NOT for: low-engagement LinkedIn posts (repurposing a flop wastes the X audience), purely visual LinkedIn carousels (different format pipeline), posts whose punch is entirely LinkedIn-context-dependent (e.g., "the LinkedIn algorithm just changed")

## Required inputs

1. **The source LinkedIn post artifact.** Either the post body + metadata, OR the path to `outputs/{date}/posts/{post-slug}.md`. Must include: hook, body, CTA, pillar, hook formula used, scope (agency vs client + client_slug if client).
2. **Source post engagement data** (if available). Impressions, comments, save rate. Used to decide whether the post warrants repurposing AND to inform the X version's hook tightening.
3. **Voice profile for the source's subject.** Agency voice if `agency-post-repurpose`. The specific client's voice if `client-post-repurpose` — load from `brand_voice.client_voices[client_slug]`.
4. **Subject's X account context** (if extractable): handle, follower count, last 20 X posts, top-3 performing X posts. Used in Step 7 for platform-tone calibration. If the subject has no X presence yet, default to LinkedIn voice with the X-specific tightening rules below.

## Pre-flight gate (mandatory)

Before any drafting:

- Confirm the source artifact exists and includes hook, body, pillar, hook formula, scope.
- Confirm the matching voice profile clears 60. Agency-scope = `brand_voice.agency_voice ≥ 60`. Client-scope = `brand_voice.client_voices[client_slug] ≥ 60`.
- Confirm the source post is not a banned-vocab artifact (in case it slipped past the original linter — re-run `spec/BANNED-VOCABULARY.md` regex over the source body before adaptation; if the source itself contains banned phrases, halt and escalate to operator/account-manager — repurposing a contaminated post will spread the contamination).
- Declare context tier inline: `Context: Solid (72%) — agency_voice 78, source-post artifact loaded, x-account context partial.`

## The build sequence

### Step 1 — Source post intake (5 min)

Pull the source artifact and extract the working set:

```yaml
source:
  scope: agency-post-repurpose | client-post-repurpose
  client_slug: null | "<slug>"
  post_slug: ""
  pillar: ""                                     # Authority | Proof | Personality | Offer-bridge | Pier
  hook_formula: ""                               # one of the 9 from hook-anatomy.md
  hook_text: ""                                  # verbatim opening 7-12 words
  body_word_count: 0
  named_mechanism: null | ""
  cta_type: ""                                   # soft | hard | comment-trigger | none
  cta_text: ""
  engagement_signal: { impressions, comments, saves }
  source_voice_phrases_present: []               # verbatim subject-voice phrases — lock these for X version
```

`source_voice_phrases_present` is the load-bearing field. Phrases the subject already used in the LinkedIn original must survive into the X version when they fit — this is what keeps the repurposed version still sounding like the subject.

### Step 2 — Single-tweet vs. thread decision (3 min)

Use this decision tree. Default to the MORE constrained format when in doubt — a tight single tweet outperforms a meandering 8-tweet thread.

**Single tweet** (one tweet, ≤280 chars) when:
- The hook + one specific can stand alone in <280 chars
- The post's payoff is a single insight or a single number, not a multi-step mechanism
- Pillar is Pier, Offer-bridge (short CTA-focused), or a tight Personality moment
- The source body word count is <250 words
- No more than one named mechanism is in play

**Thread** (5-15 tweets) when:
- The mechanism unfolds across 3+ distinct beats (each beat needs its own tweet)
- The source post is a Proof case study with before/after/mechanism segments
- The source uses a list-promise hook with 4+ list items (each item ≈ one tweet)
- The source body word count is >300 words AND the value is in the breakdown, not the headline
- The source post has a named mechanism that needs a dedicated tweet to define

**Never thread** when:
- The source is a 100-word personal-story post — threading dilutes the punch
- The source's only payoff is the hook itself (a contrarian-reframe one-liner) — the body was already filler

Output the decision with one-line justification:
> `decision: thread (8 tweets) — source is a Proof pillar case study with named mechanism, 4 distinct mechanism beats, justifies thread architecture`

### Step 3 — Hook re-tightening (10 min)

LinkedIn hooks survive at 7-12 words because the reader is in a "professional content" mindset and the algorithm gives ~1.5 seconds of dwell. X gives ~0.7 seconds. Cut to a first-5-word pattern-break minimum.

**Tightening rules:**

- LinkedIn 7-12 words → X 4-9 words. Same hook formula (per `reference/frameworks/content/hook-anatomy.md`), tighter language.
- Strip role-name prefixes LinkedIn tolerates ("As a B2B founder..." → cut). X needs no credential framing.
- Drop adjective layers ("absolutely critical realization" → "realization"). X rewards bare nouns and verbs.
- Lead with a number, name, or verb — never a connective adverb or hedge.
- First 5 words must contain the pattern-break. Read just those 5 — does the reader know whether to keep reading?
- Convert rhetorical question hooks to flat declaratives when possible — declaratives outperform questions on X by ~30%.

**Worked example (anonymized):** LinkedIn original (12 words): "Most B2B founders who write LinkedIn content focus on what their product does." X tightened (7 words): "Most B2B founders write about their product." Then T2: "The ones who get inbound calls write about what their buyer is afraid of."

Run the candidate hook through `/score-hook` (rubric in `reference/frameworks/content/hook-anatomy.md`). Required: ≥4 on Stop-the-scroll, Specificity, Voice match, Promise alignment; ≥3 on Filter. Below pass on any axis = rewrite. Same gate as the LinkedIn original used.

### Step 4 — Body compression (15 min)

LinkedIn body uses paragraphs with line breaks every 10-15 words for dwell engineering (per `virality-engine.md` mechanism #2). X uses tweet-per-beat. Restructure, don't condense.

**Single-tweet adaptation:** strip the body to the single highest-signal sentence. Shape: `[hook clause] + [one specific] + [takeaway]`. Preserve ≥1 number/name/specific from the source. Length 180-260 chars.

**Thread adaptation:** T1 = re-tightened hook. T2 = pickup line (sets implicit promise). T3...T(N-2) = body beats, ONE beat per tweet, ONE specific per tweet. T(N-1) = named mechanism OR synthesis line. T(N) = close + CTA.

**Body-beat rules:** every body tweet does ONE thing (defines a term, drops a number, names a contrast, gives an example). Multi-purpose tweets dilute. 80-260 chars per body tweet; consistently hitting 270+ = should have been split. Each tweet is self-complete (no paragraph carry-over). Numbered prefixes (1/, 2/, 3/) are filler below 7-tweet threads — skip them.

### Step 5 — Engagement-mechanic adaptation (5 min)

LinkedIn and X reward different engagement primitives. Verbatim CTA-translation collapses conversion.

**X engagement primitives** (pick exactly one per X artifact):
- **Bookmark-bait** — "Save this for when you ship your next launch." Bookmarks compound: X surfaces bookmark-heavy posts to followers of the bookmarker.
- **Quote-tweet-bait** — contrarian thesis or specific number that invites quote-tweet reactions. Drives 3-5x impressions of like-only posts.
- **Reply-as-conversation** — pose a question that invites a specific take, not yes/no engagement-bait.
- **Bio-link / pinned-tweet redirect** — for offer-bridge content. Never raw URL in body (X deprioritizes link-heavy posts); route to first-reply or pinned tweet.
- **Thread-final-tweet CTA** — threads only. Last tweet earns the CTA after value lands. Hard CTAs on T1 destroy completion rate.

**CTA translation table:**

| LinkedIn CTA | X equivalent |
|---|---|
| "Comment 'X' for the doc" | "Reply 'X' and I'll send the doc" OR "Bookmark + repost; I'll DM the doc" |
| "DM me 'INFO'" | Pinned tweet redirect + "DMs open" in bio; no in-tweet ask |
| Soft CTA ("if this resonates...") | Bookmark-bait close ("Save this. You'll need it when X happens.") |
| Hard CTA to Calendly | Bio-link redirect; never raw URL in body |
| Comment-trigger lead magnet | Reply-trigger lead magnet OR quote-tweet incentive |

Mixing two primitives = the reader doesn't know what to do = neither fires.

### Step 6 — Single-tweet draft OR thread draft (15-25 min)

**Single tweet shape:** `[Hook clause: 4-9 words, verb-led]` → `[Specific: number/name/mechanism]` → `[Takeaway OR bookmark-bait]`. Length 180-260 chars; hard cap 280.

**Thread shape:** T1 hook (tight, pattern-break, declarative when possible) → T2 pickup (the hook's implicit promise made explicit) → T3-T(N-2) beats (one per tweet, source-justified, cap at 15 total) → T(N-1) named mechanism or synthesis → T(N) close + the single chosen engagement primitive.

**Optional T(N+1) quote-tweet-bait reply.** A separate tweet posted as reply to T(N), offering a contrarian extension or specific number that invites quote-tweets. Use sparingly; works best on Authority + Pier source pillars.

For client-scope, the entire thread loads from `brand_voice.client_voices[client_slug]` — every tweet's phrasing, cadence, word choice matches the client's voice. `phrases_to_avoid` are banned in the X artifact the same way they are in client-bound LinkedIn artifacts.

### Step 7 — Voice fit re-check (5 min)

Subject's X voice is NOT the same as their LinkedIn voice. Same person; different platform-tone. Check three things:

**1. Cadence.** LinkedIn = paragraph-cadenced; X = line-cadenced. Even a single tweet should read X-native (short clauses, direct address, no soft transitions). If the draft reads with LinkedIn paragraph-cadence ("I want to share something with you today...") — rewrite.

**2. Formality.** LinkedIn voice runs ~10-15% more formal than X voice for the same subject (B2B context priming). Loosen contractions, drop honorifics, allow direct verbs. If the subject's X account exists, sample-match against their last 20 tweets: first-person? swearing? intentional lowercase? Mirror.

**3. Banned phrases (X-specific stack on top of `spec/BANNED-VOCABULARY.md`):** "I want to share with you...", "Here's a quick story...", "In this thread, I'll cover..." (the X analog of "in this post, I'll share"), any pre-hook hedging ("So, I've been thinking about..."), long-form throat-clearing.

Run the entire X artifact through the banned-vocab regex. LinkedIn list applies verbatim; X-specific additions stack. Match = halt + rewrite per `INVARIANTS.md` N-4. If `client-post-repurpose`, also load `brand_voice.client_voices[client_slug].phrases_to_avoid` and re-run with those entries — the client's avoid-list is sacred across every channel.

### Step 8 — Output emission (3 min)

Hydrate the output artifact at `outputs/{date}/repurpose/x/{post-slug}.md`:

```yaml
---
artifact_type: x-post-or-thread
scope: agency-post-repurpose | client-post-repurpose
client_slug: null | "<slug>"
source_post_path: "outputs/{date}/posts/{post-slug}.md"
source_pillar: ""
source_hook_formula: ""
adapted_format: single-tweet | thread
thread_length: 0                                 # 0 if single-tweet, else 5-15
hook_score: { stop_scroll, specificity, voice_match, promise, filter }
engagement_primitive: bookmark-bait | quote-tweet-bait | reply-trigger | bio-link-redirect | thread-final-cta
banned_vocab_sweep: passed | failed
voice_fit_check: passed | failed
predicted_engagement_band: low | mid | high
profile_completeness: 0-100
profiles_used: [brand_voice.{scope}, content_strategy.{scope}]
frameworks_used: [hook-anatomy, virality-engine, banned-vocabulary]
confidence: HIGH | MEDIUM | LOW
build_date: YYYY-MM-DD
---

## The X artifact

[either the single tweet OR the numbered thread T1...TN]

## Optional quote-tweet-bait (if applicable)

[T(N+1) reply tweet]

## Source → X mapping notes

- Hook: source `{hook_text}` → X `{tightened_hook}` (formula preserved: {formula})
- Body restructure: {note on how the LinkedIn paragraphs became tweet beats}
- CTA translation: source `{cta_text}` → X `{primitive}`
- Voice tone shift: {note on the LinkedIn-to-X tone calibration applied}

## Predicted engagement (heuristic, not promise)

- Single-tweet baseline: {band} based on subject's last 20 tweets
- Thread baseline: {band}
- Quote-tweet ceiling: {band} if Pier/Authority pillar
- Confidence: {HIGH/MED/LOW} — based on voice profile completeness and X-account context availability

## Next actions

- Schedule for {best X posting window for subject — usually weekday 9-11am or 7-9pm subject-local}
- If thread, pre-load reply T(N+1) draft for posting 30-60 min after thread to leverage initial engagement
- Track engagement at 60 min and 24 hr; log delta vs. baseline to feed the cycle-N+1 hook library refresh
```

## Validation gate (mandatory before declaring done)

Three checks. All three must pass.

1. **Standalone test.** Show the X artifact (no LinkedIn context) to a reviewer who has never seen the source post. Ask: "Would you stop scrolling on this in your X feed?" Pass: yes, with one specific reason cited (the hook, the number, the contrarian thesis).

2. **Voice match test.** Compare the X artifact against the subject's last 20 X posts (or, if no X presence, against the LinkedIn voice profile with X-platform tone allowance). Pass: indistinguishable from the subject in cadence, vocabulary, and stance.

3. **Banned-vocab + score-hook gate.** The platform-adapted hook scored ≥4 on the four primary axes (Stop-scroll, Specificity, Voice, Promise) and ≥3 on Filter. The full artifact passed the banned-vocab regex (LinkedIn list + X-specific additions + client's phrases_to_avoid if `client-post-repurpose`).

If any of the three fails, return to Step 3 (hook), Step 4 (body restructure), or Step 7 (voice). Two attempts max before escalating to the marketing-head with a brief on the specific failure pattern.

## Failure modes and guards

- **LinkedIn-cadence X post.** Reads like a LinkedIn post with line breaks deleted. Guard: Step 7 cadence check; no contractions + 12+ word sentences throughout = halt and rewrite tweet-by-tweet from source bullets.
- **Hook formula preserved but voice lost.** The contrarian-reframe formula survived; the subject's signature phrasing didn't. Guard: `source_voice_phrases_present` (Step 1) must surface ≥30% of its entries into the X artifact.
- **Thread that should have been a single tweet.** 6-tweet thread where T2-T5 are filler. Guard: if any thread tweet would survive deletion without the reader noticing, the thread should have been shorter. Re-run Step 2.
- **CTA double-up.** Bookmark-bait AND reply-trigger AND bio-link mention. Reader doesn't know the next action. Guard: exactly one engagement primitive per artifact (Step 5).
- **Repurposing a LinkedIn flop.** Source impressions <50% of subject median = X version inherits the weakness. Guard: Step 1 engagement check; below threshold = recommend skip or run `/diagnose-bottleneck` on the source first.
- **Client voice contamination.** Client-scope artifact accidentally loaded `agency_voice` instead of `client_voices[slug]`. Guard: pre-flight gate makes scope explicit; frontmatter logs the loaded voice; voice-drift cosine sample runs at Step 7.

## Cross-references

- `reference/frameworks/content/hook-anatomy.md` — hook formulas + scoring rubric (the X-tightened hook still uses one of the 9 formulas)
- `reference/frameworks/content/virality-engine.md` — first-12-impression bet + dwell-time engineering (X has its own analog: the first 30 seconds of bookmark/reply velocity)
- `agents/marketing-head.md` — owns the gate between content-engine outputs and repurpose artifacts
- `spec/BANNED-VOCABULARY.md` — regex list (X-specific additions stack in Step 7)
- `spec/CONTEXT-THRESHOLDS.md` — `/repurpose-to-x` row
- `INVARIANTS.md` N-1, N-4, N-6, N-9 — voice loading, banned vocab, voice contamination, no "final" claims
- `skills/build-content-engine/SKILL.md` — the source post was produced from this engine
- `skills/write-linkedin-post/SKILL.md` (Cycle 2 — TBD) — the agency-content source post producer
- `skills/ghostwrite-client-post/SKILL.md` (Cycle 2 — TBD) — the client-content source post producer
- `skills/repurpose-to-ig/SKILL.md` — sister skill for Instagram adaptation
- `skills/score-hook/SKILL.md` — the hook scoring runtime invoked in Step 3 + validation gate

## Examples

See `examples/` (to be populated):
- `example-agency-thread-from-authority-post.md` — agency-content Authority pillar post → 9-tweet thread
- `example-agency-single-tweet-from-pier-post.md` — agency-content Pier pillar post → single tweet
- `example-client-thread-from-proof-post.md` — client-content Proof pillar case study → 11-tweet thread
- `example-client-single-tweet-from-personality-post.md` — client-content Personality moment → single tweet

---

*Skill version 1.0.0 — 2026-05-03*
