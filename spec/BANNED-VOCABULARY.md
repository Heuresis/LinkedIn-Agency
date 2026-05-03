# Banned Vocabulary

> Anti-slop filter. Phrases the workspace will not produce in any client-bound or program-bound artifact. Each banned phrase pattern-matches the LinkedIn-guru content the operator's positioning explicitly rejects.
>
> **Enforcement.** Hard-block. The workspace's output linter scans every artifact for these patterns; matches halt the artifact for rewrite per `INVARIANTS.md` N-4.
>
> **Refresh cadence.** Quarterly. As LinkedIn writing evolves, the dead-phrase list shifts.

## Hook openers (banned)

These hook openers signal "AI-generated LinkedIn content" or "guru content" and depress engagement / damage brand:

- "🚀 Excited to share..."
- "🚀 Let's dive in..."
- "In this post, I'll share..."
- "In this post, I'll cover..."
- "Did you know that..."
- "Here's the truth about..."
- "Let me tell you a story..."
- "Quick story: ..."
- "Here's what happened..."
- "I'll be honest..."
- "Buckle up." / "Hold on to your hats."
- "POV:"
- "Unpopular opinion:"
- "Hot take:"
- "🔥 Hot take:"
- "Game-changer alert:"
- "This blew my mind."
- "I'm not going to lie..."
- "Real talk:"
- "Here's the thing:" (when used as opener — fine in the body)
- Opener that begins with any emoji

## Body phrases (banned)

These phrases creep into LinkedIn content and pattern-match generic-content-mill voice:

**Generic transitions:**
- "Synergize"
- "Move the needle"
- "Game-changer"
- "Game-changing"
- "Disrupt the industry"
- "Paradigm shift"
- "Think outside the box"
- "Low-hanging fruit"
- "Boil the ocean"
- "Drink the Kool-Aid"
- "Touch base"
- "Circle back"
- "Take it offline"
- "At the end of the day" (overused — strip)
- "It is what it is"

**Hyperbolic claims:**
- "Mind-blowing"
- "Mind-blown"
- "Crushing it"
- "Killing it"
- "Absolutely crushing"
- "Next-level"
- "10X your..."
- "Skyrocketed"
- "Through the roof"
- "Massive"
- "Insane" (as adjective for results)
- "Unreal"

**Generic appeals to community/expertise:**
- "Friends, ..."
- "Happy to share..."
- "Honored to..."
- "Humbled to..."
- "Excited to announce..."
- "Thrilled to share..."
- "Delighted to..."
- "It's been a journey..."
- "I learned so much..."
- "Without further ado..."

**Engagement-bait closers:**
- "Thoughts?"
- "Agree or disagree?"
- "What do YOU think?"
- "Drop a comment below 👇"
- "Tag someone who needs this!"
- "Repost if you agree!"
- "Comment AGREE if..."
- "Like and share!"
- Any closer that leads with an emoji directional arrow

## Structural patterns (banned)

- **Bullet-walls.** Posts that are 90%+ bulleted lists with no narrative. Strip and rewrite as paragraph + 1-2 selective bullets max.
- **Emoji walls.** More than 3 emoji in a single paragraph. More than 5 emoji in a post.
- **Hashtag stacks.** More than 2 hashtags in a single post.
- **All-caps shouting.** ALL-CAPS WORDS for emphasis. Use selective bold or italic structure instead.
- **Faux-humility brag** ("I'm just a small founder but..." followed by a humble-brag).
- **Numbered "secrets"** ("3 secrets nobody is telling you about LinkedIn"). The "secrets" framing is dead.

## Banned framings (concept-level)

- **"How I went from $0 to $1M"** stories without specific operational mechanism. The arc is fine; the genericness is not.
- **"Here are the 5 lessons I learned"** without each lesson being mechanism-specific. Lesson posts that read as "be consistent / be authentic / believe in yourself" are LinkedIn-content-mill output.
- **"The future of [X]"** posts without a specific testable thesis. Speculative-future framing reads as filler.
- **"Just had a 3-hour conversation with a 30-year veteran of [industry] and here's what I learned"** — has become a content-mill template. Use the specifics, drop the framing.

## Workspace-specific bans (anti-positioning)

These would directly contradict the operator's anti-establishment positioning per `business_context.positioning`:

- "LinkedIn guru" (even mocking it — gives oxygen)
- "LinkedIn algorithm hack"
- "Influencer"
- "Personal brand" (use "operator presence" or specific role-language instead)
- "Content creator" when referring to the operator (use "operator" or specific role)
- "Mindset" as standalone noun ("right mindset," "winning mindset")
- "Manifesting"
- Generic "value bomb" / "value-packed" framing

## Allowed-with-care list

Some phrases work in specific contexts but pattern-match to slop when used carelessly:

| Phrase | Allowed when | Banned when |
|---|---|---|
| "Quick story:" | Used to set up a specific anecdote >100 words | Used as opener to a 50-word lesson |
| "Here's the thing:" | Used as a body transition | Used as the post opener |
| "Long story short:" | Used to compress an actually-long backstory | Used to skip the substance |
| "I learned X from Y" | Y is a specific person/event | Y is "all my mentors" or "this experience" |

## How the linter runs

The artifact-output linter runs the banned list as a regex pattern match against:
- Post body
- Post hook
- DM body
- Email body
- Application question text
- Sales-page copy

A match returns `BANNED_VOCABULARY_DETECTED` with the specific phrase + line number. The artifact halts. The agent rewrites and re-submits.

Operator override is possible via `company.yaml.invariants_overrides.banned_vocab_exceptions` for legitimate edge cases — but every override is logged and reviewed quarterly to prevent drift.

## Quarterly review

Q1 of each calendar year, this list is reviewed:
- Add new banned patterns that have surfaced as LinkedIn-slop
- Retire patterns that have either gone genuinely mainstream OR ceased to be common
- Cross-reference with `reference/platforms/linkedin/algorithm-notes.md` for LinkedIn-suppression patterns

## Cross-references

- `INVARIANTS.md` N-4 — never use banned vocabulary
- `skills/build-content-engine/SKILL.md` — references this list for hook library construction
- `skills/write-linkedin-post/SKILL.md` — runs lint on output
- `skills/ghostwrite-client-post/SKILL.md` — runs lint on output
- `reference/platforms/linkedin/algorithm-notes.md` — quarterly LinkedIn behavior updates

---

*Spec version 1.0.0 — 2026-05-03. Reviewed quarterly.*
