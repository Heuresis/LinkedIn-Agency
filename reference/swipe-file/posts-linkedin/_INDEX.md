# Swipe File — LinkedIn Posts

> Real high-performer posts from public LinkedIn (B2B operators 2024-2026), source-anonymized per workspace canon, annotated with the structural mechanism that drove the post's performance.
>
> **What "anonymized" means here.** The text is paraphrased to preserve the structural mechanism while stripping operator name, brand name, specific client name, specific dollar figures, and any geographic/industry detail that would identify the source. The mechanism is the value; the source is incidental.
>
> **What "high-performer" means here.** Each post passed at least two of these signals when collected: (a) >50K impressions, (b) >300 reactions, (c) >50 substantive comments, (d) >20 reshares, (e) author publicly attributed material business outcomes (book sales, calls, signups) to the specific post.

## Organization

```
posts-linkedin/
├── _INDEX.md                          ← this file
├── authority/                         ← Authority pillar examples
│   ├── 01-contrarian-reframe-saas-positioning.md
│   ├── 02-named-mechanism-content-engine.md
│   └── 03-insider-secret-discovery-call.md
├── proof/                             ← Proof pillar examples
│   ├── 04-before-after-client-transformation.md
│   └── 05-specific-stakes-failure-lesson.md
├── personality/                       ← Personality pillar examples
│   ├── 06-loop-open-day-in-life.md
│   └── 07-personal-to-business.md
├── pier/                              ← Pier (algorithm-leverage) pillar examples
│   ├── 08-contrarian-provocation.md
│   └── 09-pattern-break-listicle.md
└── offer-bridge/                      ← Offer-bridge pillar examples
    ├── 10-hook-loop-cta.md
    └── 11-thought-proof-cta.md
```

## How to read these files

Every example file follows this structure:

1. **The post** (anonymized text, formatted as it would appear on LinkedIn)
2. **Why it worked** (mechanism breakdown — hook formula used, virality mechanism leveraged, voice elements that landed)
3. **Performance signals** (the pattern that signaled this was a high-performer, with anonymized magnitude)
4. **The structural blueprint** (what to extract for use in `/write-linkedin-post` or `/ghostwrite-client-post`)
5. **What NOT to copy** (the elements specific to the original operator that won't transfer)

## How `/write-linkedin-post` and `/ghostwrite-client-post` use these

When generating a post for a specific pillar, the skill loads 2-3 examples from that pillar's directory + the hook-anatomy framework + the subject's content engine spec. The examples are **calibration targets**, not templates. The output should match the structural mechanism while carrying the subject's voice, ICP language, and named mechanism.

## Adding new examples

When the operator (or a client) ships a high-performer post, the workspace logs it to `outputs/{date}/posts/` with metadata. After a 30-day performance window, posts that hit ≥2 of the signals listed above are promoted to this swipe file via `/promote-to-swipe-file`. Anonymization runs on promotion — the operator confirms before commit.

## Index of pillars × hook formulas

| File | Pillar | Hook formula | Body shape | Virality mechanism |
|---|---|---|---|---|
| 01 | Authority | Contrarian-reframe | Mechanism-then-payoff | Pier + dwell |
| 02 | Authority | Named-mechanism | Listicle-with-named-frame | Comment-trigger CTA |
| 03 | Authority | Insider-secret | Loop-open with payoff | Comment-reply harvest |
| 04 | Proof | Before-after | Story arc with numbers | Cross-post compound |
| 05 | Proof | Specific-stakes | Failure-lesson narrative | Dwell + comment-reply |
| 06 | Personality | Loop-open | Day-in-life narrative | Comment-reply harvest |
| 07 | Personality | Specific-stakes | Personal-to-business bridge | Pier mechanism |
| 08 | Pier | Pattern-break | Provocation + evidence | Comment-trigger (debate) |
| 09 | Pier | List-promise | Listicle-with-stakes | Cross-post compound |
| 10 | Offer-bridge | Question-stake | Hook-loop-CTA | Comment-trigger CTA |
| 11 | Offer-bridge | Named-mechanism | Thought-proof-CTA | Comment-trigger CTA |

---

*Swipe file version 1.0.0 — 2026-05-03*
