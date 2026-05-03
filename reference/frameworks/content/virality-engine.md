# The Virality Engine

> What turns a 4K-impression post into a 100K-impression post on LinkedIn in 2026. Five mechanisms. Engineered, not lucky.

## The first-12-impression bet

LinkedIn's algorithm runs an early-distribution test. The first ~12 readers see the post. Their dwell + engagement signal in the first ~60 minutes determines whether the algorithm shows the post to the next ~12K.

Consequence: the entire viral trajectory is decided in the first 60 minutes. The post that gets to 100K had its trajectory locked in before most of the audience saw it.

Engineering this means optimizing for:

- **Click rate on "see more"** — driven by hook (per `hook-anatomy.md`)
- **Dwell time once expanded** — driven by line-break density, single-sentence paragraphs, narrative loops
- **Engagement in first 60 minutes** — comments and shares >>> likes; 1 comment ≈ 12 likes in algorithmic weight

## The 5 mechanisms

### 1. The pier mechanism

**What it is.** Lurking-leverage. Find a high-engagement post (yours or someone else's) in your subject area. Reply with the most insightful comment in the first 30 minutes. Then publish your own post that references the conversation (or the original post's topic) within 24 hours.

**Why it works.** Two layers:
1. The comment on the high-engagement post pulls profile-views from the post's audience to your profile
2. Your follow-up post lands in their feed within 24 hours and reads as continuation of a conversation they already care about

**Operational pattern.**
- Identify 5-10 "pier posts" weekly (high-engagement posts in subject area)
- Comment with substance (50-150 words, not "great post")
- Publish your own related post within 24 hours
- Tag the original-post-author in the comment of YOUR post (not the body — bodies with tags get suppressed)

**Output:** baseline impressions × 1.5-3x on the pier-leveraged post.

### 2. The dwell-time engineering primitive

**What it is.** Format the post for 8+ seconds of dwell-time minimum. LinkedIn measures dwell explicitly (time on expanded post). Higher dwell = wider distribution.

**Operational rules.**
- Single-sentence paragraphs (no >2 sentences in one paragraph)
- Line breaks every 8-15 words
- One specific number or name per ~50 words
- Open loops in the middle ("which leads me to the part nobody talks about...") — keeps readers scrolling
- Body length: 800-1500 characters typical sweet spot for B2B; 200-500 for personal-story posts; 1500-3000 for proof posts

**Anti-patterns:**
- Wall-of-text paragraphs (kills dwell)
- Bullet-list-only posts (skim too fast)
- Emoji walls (distracts, depresses dwell)
- Posts where the payoff lands in the first 100 chars (no reason to keep reading)

### 3. The comment-reply harvest

**What it is.** The first 60 minutes of comments determine algorithmic momentum. Reply to every comment in the first 60 minutes. Each reply = a fresh signal.

**Why it works.** LinkedIn weights comment depth (comment + reply chains) as a strong engagement signal. Your reply both engages the original commenter AND triggers their notifications, often pulling them back to engage again.

**Operational pattern.**
- Block 60 minutes immediately post-publish
- Reply to every comment with substance — not "thanks!" — a question or a specific add
- Re-engage 4-12 hours later with a "second wave" reply-pass for late comments
- Pin the most insightful comment to surface it for new readers

**Output:** comment-reply harvest doubles comment depth which lifts impressions ~30-60%.

### 4. The cross-post compound

**What it is.** Same idea, three formats, three days. Text post Monday → carousel post Tuesday → 60-90 second video Wednesday. Each format reaches a different segment of the audience and the algorithm reads them as related (engagement compounds).

**Operational pattern.**
- Identify the post idea worth compounding (typically the top-25% of pillar posts)
- Day 1: text post (the original idea)
- Day 2: carousel summary (3-5 slide visual breakdown)
- Day 3: video that delivers the same idea face-to-camera with one new specific
- Each post references the prior format in the comments

**Output:** compound impressions 2-3x the single-post baseline; profile-views 4-5x; calls-from-content 3-5x.

### 5. The high-intent comment trigger

**What it is.** End select posts with a specific comment-trigger CTA: "Comment 'PLAYBOOK' for the 12-page document on this." Triggers Class M (Comment-for-lead-magnet) in the DM Intent Taxonomy.

**Why it works.** Comment-driven CTAs convert at 80%+ to DM thread (vs. 8-15% for "DM me" CTAs) AND drive comment volume which lifts the post's algorithmic distribution.

**Operational pattern.**
- Use comment-trigger CTA on ~25% of posts (the offer-bridge pillar posts)
- Trigger word must be specific and unambiguous (not "comment below")
- Auto-DM delivery via ManyChat or equivalent within 1 hour
- Lead-magnet delivered must be over-built relative to the promise (under-promise, over-deliver builds trust for the eventual call)

**Output:** comment volume on triggered posts runs 3-8x non-triggered baseline; DM funnel adds 10-30 high-intent prospects per post.

## Composition: the viral post anatomy

The posts that hit 100K+ impressions almost always combine 3 of the 5 mechanisms. The composite anatomy:

```
[HOOK — 7-12 words, 1 of the 9 hook formulas, scored ≥4 on stop-the-scroll]

[OPENING SENTENCE — picks up the hook, single line, sets the implicit promise]

[BODY 1 — story/mechanism/proof. Single-sentence paragraphs. Line breaks every 10-15 words.]

[OPEN LOOP — "and here's the part most people miss" / "but the real lesson is downstream"]

[BODY 2 — payoff. Specific numbers, names, evidence.]

[NAMED MECHANISM (if Authority pillar) — "The {Name} works because..."]

[CLOSING LINE — one sharp sentence that lands the lesson]

[CTA — soft (Authority/Personality), hard (Offer-bridge), comment-trigger (lead magnet)]
```

## What kills virality

- **Hashtags >2.** Hashtag-heavy posts get suppressed. 0-2 maximum.
- **External links in body.** LinkedIn deprioritizes posts with external URLs in body. Move links to first comment.
- **Mentions/tags >1.** Tag-heavy posts read as engagement-bait and get suppressed.
- **Emoji walls.** 1-3 emoji is acceptable; emoji-as-bullet-list is suppressed.
- **Pure self-promotion.** Posts that talk only about your offer = algorithm-suppressed. The 80/20 ratio (80% value, 20% offer-adjacent) is real.
- **Late publish-time.** Publishing outside Tue/Wed/Thu 8-11am operator-local cuts impressions 40-60%.
- **No comment-reply in first 60 min.** Algorithm reads "stale post," moves on.

## Quarterly refresh cadence

LinkedIn's algorithm shifts. What worked in Q1 may suppress in Q3. The virality engine doc is reviewed quarterly per `reference/platforms/linkedin/algorithm-notes.md`.

## Cross-references

- `skills/build-content-engine/SKILL.md` — sets virality target per subject
- `reference/frameworks/content/hook-anatomy.md` — the hook layer
- `reference/frameworks/content/pillar-architecture.md` — the pillar layer
- `reference/platforms/linkedin/algorithm-notes.md` — current LinkedIn behavior
- `reference/swipe-file/posts-linkedin/` — annotated viral examples

---

*Framework version 1.0.0 — 2026-05-03*
