# Proof — Before-After — Client Transformation

> **Source profile:** B2B agency operator (LinkedIn marketing services), ~14K followers at time of post. Anonymized per workspace canon. Client name + identifying details substituted with archetype descriptors.
>
> **Performance signals:** ~52K impressions, ~430 reactions, ~84 substantive comments, ~30 reshares. Author attributed two retainer signs ($8K/mo each) directly to this post in the next 21 days.

## The post

```
6 months ago, this client was getting 3 inbound calls per quarter from LinkedIn.

Last month: 14 calls in one week.

I want to walk you through what we actually changed, because most of the "LinkedIn growth" content out there is theater.

Here's the operator profile (anonymized):

→ B2B SaaS founder, post-Series A, ~$4M ARR
→ Posting 1-2x/month, mostly product updates
→ Average post impressions: ~800
→ Inbound calls from LinkedIn: 3 per quarter (mostly recruiters)

The problem wasn't volume. He was already posting.

The problem was that every post was structured for the wrong reader.

He was writing for "potential users of his product."

His actual buyer was a different person — VP Engineering at a 50-200 person company who had a specific painful experience that his product solved, but who was reading LinkedIn at 10pm to decompress, not to evaluate vendors.

So we did three things.

ONE: rewrote his content pillars from "what our product does" to "the operational pains of being a VP Eng at scale-up phase."

TWO: built a hook library from his actual sales call transcripts. Not what he wished his buyer would say. What his buyer actually said on the call when explaining the pain.

THREE: changed his posting cadence from 1-2x/month to 5x/week, but with batched-Sunday-write so it didn't eat his weekday brain.

90 days later: 12 calls in November. 14 in early December. The two best calls had founders who had been silently reading his posts for 4 months without engaging once.

That's the part that breaks people's mental model of how LinkedIn works.

The buyer who closes is rarely the one who comments. The comments come from peers and recruiters. The buyer is reading silently, deciding whether you're someone worth talking to.

If you're writing posts for the people who comment, you're optimizing for the wrong feedback loop.

Write for the silent reader who's deciding whether to ever DM you. The 14 calls a week comes from there.
```

## Why it worked

**Hook formula used.** Before-after (formula #4 in `hook-anatomy.md`).
- "{Time period} ago, {bad state}. Today, {good state}."
- Specific number contrast (3 calls/quarter → 14 calls/week) creates immediate stakes.

**Hook scoring:**
- Stop-the-scroll: 5 (specific number contrast, specific time frame)
- Specificity: 5 (every number is specific; quarter vs. week emphasizes the magnitude)
- Voice match: 4 (depends on subject voice; structure carries)
- Promise alignment: 5 (body delivers the actual mechanism, not vague platitudes)
- Filter strength: 5 (B2B founders + LinkedIn-curious self-identify; everyone else bounces)

**Body mechanics:**
- "Anonymized" callout in the body builds operator credibility (we are an agency that protects client identity)
- Three-step mechanism with named steps (ONE, TWO, THREE) reads as proprietary
- The "12 calls in November / 14 in early December" specificity is the closing credibility lock
- The closing reframe ("write for the silent reader") is the contrarian truth — quotable, share-worthy

**Virality mechanism leveraged:**
- **Cross-post compound** — author followed up 24h later with a carousel breaking down the 3 mechanisms; followed by a video on day 3 demoing the "hook from sales call transcripts" technique
- **Comment-reply harvest** — author replied to every commenter in first 90 min asking specifically about THEIR current posting structure
- **Comment-trigger CTA implicit** — multiple commenters asked for the framework; author DM'd the cohort directly, converting 2 to discovery calls

## Performance signals

| Metric | This post | Author's typical baseline | Multiple |
|---|---|---|---|
| Impressions | ~52K | ~5K | 10x |
| Reactions | ~430 | ~70 | 6x |
| Substantive comments | ~84 | ~10 | 8x |
| Reshares | ~30 | ~3 | 10x |
| Retainer signs attributed (21d) | 2 ($8K each) | typical = 0 from any single post | — |

## The structural blueprint

```
[HOOK — Before-after, specific number contrast, time-specific]

[CONTEXT-STAKE LINE — "I want to walk you through what we actually changed, because [contrarian frame]"]

[ANONYMIZED CLIENT PROFILE — 3-5 bullet points, specific numbers, specific role]

[THE WRONG-DIAGNOSIS — "The problem wasn't X. The problem was Y."]

[ROLE-SHIFT — Reframe of who the buyer actually is, in specific language]

[THE THREE MOVES — Numbered, named, specific. Each with the "why" anchor.]

[THE AFTER METRICS — Specific numbers, specific time]

[THE PATTERN-BREAK INSIGHT — "That's the part that breaks people's mental model of [topic]"]

[THE QUOTABLE TRUTH — One short paragraph that's screenshot-worthy]

[THE LANDING — Direct prescription, sharp]
```

## What NOT to copy

- Client identity. The post anonymizes the client by archetype. Real identity must come from the case-study profile with `use_in_content: true` flag (per `INVARIANTS.md` N-3).
- The specific numbers. Substitute real numbers from `case_studies.agency_case_studies[*]`.
- The mechanism specifics. The "rewrite pillars / build hook library from sales calls / Sunday-batch cadence" mechanism is what THIS agency does. The subject substitutes their own actual mechanism.

## Cross-references

- `reference/frameworks/content/hook-anatomy.md` — formula #4
- `reference/frameworks/content/virality-engine.md` — mechanisms 3, 4
- `skills/build-content-engine/SKILL.md` — Proof pillar
- `INVARIANTS.md` N-3 — never invent results
