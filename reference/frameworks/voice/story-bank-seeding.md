# Story Bank Seeding

> The structural rules for the story bank — the 8-15 stories the ghostwriter draws from when authoring posts on the operator's (or client's) behalf. Without a story bank, posts default to abstract claims. With a story bank, every post can be anchored in a specific moment.

## Why this framework exists

Posts that win on LinkedIn are anchored in specifics — a conversation, a moment, a number, a proper-noun-as-detail. Posts that lose default to abstract claims about how the market works, what founders should think, or what the operator believes. The line between the two is a story, told in detail, with stakes the reader recognizes.

Ghostwriters cannot invent stories. (Per `INVARIANTS.md` N-3: never invent client results, transformation numbers, case-study metrics. The same logic extends to story content — invention pollutes the corpus and triggers the "this didn't actually happen" rejection that kills voice authenticity faster than any other failure mode.)

A populated story bank solves this. With 8-15 stories tagged and indexed, every post-pillar (Authority, Proof, Personality, Offer-bridge) has multiple stories to draw from. The ghostwriter selects, frames, and shapes — but never fabricates.

The story bank is built once during voice extraction (per `voice-extraction-9q.md` Q1, Q3, Q6) and refreshed quarterly. It lives inside `brand_voice.{scope}.story_bank` per the `ENCODING.md` schema.

This framework applies to both scopes:
- **agency-voice story bank** — the operator's own stories, used for the agency's content
- **client-voice story bank** — per-client subprofile, one story bank per active client, used for ghostwritten client content

## The 7 rules

### Rule 1 — Minimum 8 stories, target 12-15

Below 8, the ghostwriter exhausts the bank by week 3 and starts inventing or repeating. Above 15 diminishes returns — the bank becomes harder to retrieve from, and the marginal story rarely earns its index slot.

The ranges:
- 0-7 stories: profile capped at 60% completeness; ghostwriter cannot ship client-bound posts
- 8-11 stories: profile crosses 75%; ghostwriter ships with operator-review-required
- 12-15 stories: profile crosses 85%; ghostwriter ships with optional operator review
- 16+: diminishing return; consider quarterly cull rather than continued accumulation

### Rule 2 — Each story carries the full tag set

A story is not in the bank until it is tagged on every dimension below. Untagged stories are scratchpad notes, not bank entries.

```yaml
- story_slug: short-descriptor              # e.g. "first-five-figure-month-from-cold-dms"
  pillar_fit: [Authority | Proof | Personality | Offer-bridge]   # 1-3 pillars
  characters: [string]                       # who's in the story (descriptors, never names — see Rule 4)
  stakes: string                             # what was at risk (financial, reputational, identity, time)
  decision_moment: string                    # the single sentence describing the choice that turned it
  outcome: string                            # what actually happened (factual, sourced if metric)
  lessons_extractable: [string]              # 1-3 reusable takeaways the story can support
  source_question: string                    # which voice-extraction question surfaced this (Q1/Q3/Q6/...)
  use_eligibility: [public | client-only | operator-only | NDA-protected]
  story_shape: build-up | payoff-first | anecdote-then-frame
```

The tag set is what makes the bank queryable. A ghostwriter writing a Proof-pillar post for a Helpless-belief ICP queries `pillar_fit:Proof` AND `lessons_extractable:contains("capability")` and gets a shortlist in seconds. Untagged stories are invisible to that query.

### Rule 3 — Story sources are interview answers + real artifacts, never invention

Stories enter the bank from these sources only:
- **Voice-extraction interview answers** — Q1 (the explanation moment), Q3 (the recent client win), Q6 (the worst-fit client) are the highest-yield seeds. Q9 (the frustration moment) often surfaces stories the operator didn't know they had.
- **Client-call recordings** — discovery calls, retention calls, exit interviews. The operator narrates stories on these calls that never made it to the interview.
- **Slack and email threads** — internal Slack rants, client-facing emails describing wins or losses. The first-take phrasing is the asset.
- **Screenshots of past wins** — DM threads that closed, application responses, post-engagement screenshots. Each screenshot is a story-anchor; the story-construction layer wraps around it.
- **Operator-volunteered stories during workshop sessions** — when the operator says "oh, that reminds me of the time when..." — capture verbatim, transcribe, file under the appropriate slug.

Forbidden sources:
- AI-generated story-shapes filled in with operator details (the cadence reads inverted — generic frame around specific filling, instead of specific frame around honest filling)
- Composite stories ("a typical client" / "this happens all the time") — composites read as fiction even when based on real patterns
- Hypothetical stories ("imagine if...") — even with a "for example" frame, hypotheticals lose authority

Per `INVARIANTS.md` N-3: every story must have happened. Verifiability matters even when the story is too granular to source-cite publicly.

### Rule 4 — Character anonymization is mandatory

Per `INVARIANTS.md` A-11 and N-13: methodology descriptors only. No real names of clients, prospects, competitors, or partners in any story bank entry.

The descriptor convention:
- Clients: "the SaaS founder I onboarded last quarter" / "the agency operator in the procurement vertical"
- Prospects: "the prospect who replied to the third DM" / "the buyer who ghosted after the demo"
- Competitors: "the volume-first agency I lost the deal to" / "the inbound-content authority the prospect was comparing me against"
- Internal team: role only — "my account manager" / "the ghostwriter on that account"

Real names belong in operator-private notes, never in the story bank that hydrates downstream artifacts. The descriptor is the public-safe unit; the bank stores only descriptors.

### Rule 5 — Quarterly refresh, with cull and expansion

Story banks decay. Stories the operator told for 18 months become stale; new client wins, new losses, new conviction-forming moments arrive every quarter. The bank must refresh:

- **Quarterly cadence:** at the start of each quarter, run a 30-minute story-bank audit
- **Cull criteria:** remove stories that (a) the operator has not pulled in 90 days, (b) the operator has rejected as "I don't tell that story anymore," (c) carry NDA or eligibility issues that have shifted
- **Expansion criteria:** add stories from the past quarter's client work, the past quarter's losses, the past quarter's conviction-forming moments
- **Net target:** keep total count in 12-15 range — refresh-out matches refresh-in

If the operator has had a major event (offer pivot, vertical pivot, public conviction-shift), trigger an off-cadence refresh — those events invalidate prior stories and seed new ones.

### Rule 6 — Use-eligibility is tagged per story

Not every story is publishable on every channel. The bank tags eligibility explicitly:

- **public** — story can ship in any agency-account post, client-account post, sales material, or program content
- **client-only** — story can ship in the *specific client's* posts only (e.g., a story the client told that belongs to their voice, not the operator's)
- **operator-only** — story is the operator's first-person and cannot transfer to a client (e.g., "when I started this agency...")
- **NDA-protected** — story carries an NDA or contractual confidentiality; not eligible for any external use until cleared

Eligibility prevents the most common error: a story from a client interview surfaces in an agency post and the client recognizes it ("that's my story, why is the agency telling it?"). Eligibility tagging makes that error impossible.

### Rule 7 — Story-shape is tagged per story

The same story can ship in three different shapes. The shape choice is not a narrative preference — it determines the post-type the story can support and the hook the post can use.

- **build-up** — chronological. Setup, tension, resolution, lesson. Best for personality-pillar posts and longer-form authority posts. Hook: usually a specific scene-setter ("Three weeks before our launch, the lead developer quit.").
- **payoff-first** — outcome at the top, story as proof. Best for proof-pillar posts and short-form authority posts. Hook: the outcome itself ("We closed $87K in retainers from one DM thread last month. Here's what was in it.").
- **anecdote-then-frame** — story as a single beat, frame as the lesson the post argues. Best for offer-bridge posts and contrarian-take posts. Hook: usually the frame, with the anecdote as the proof underneath ("Most agencies sell hours. The ones that compound sell decisions. Here's what changed when I made the switch.").

Tag each story with the shape(s) it can support. Some stories support all three; some only support one. The tag prevents the ghostwriter from forcing a story into a shape it can't carry.

## Output template

The story bank is a list inside the voice profile per `ENCODING.md` Profile 2. The full bank-entry schema:

```yaml
brand_voice.{scope}.story_bank:
  - story_slug: ""                       # short kebab-case descriptor
    story_type: ""                       # Origin | Transformation | Conflict | Revelation | Anti-Hero
    pillar_fit: []                       # [Authority, Proof, Personality, Offer-bridge] — 1-3 entries
    characters: []                       # descriptors only, per Rule 4
    stakes: ""                           # one-sentence, what was at risk
    decision_moment: ""                  # one-sentence, the choice that turned it
    outcome: ""                          # what happened — factual, sourced if numeric
    emotional_arc: ""                    # setup → tension → resolution → lesson
    key_lessons: []                      # 1-3 reusable takeaways
    proof_anchors: []                    # specific numbers, dates, names-of-things
    source_question: ""                  # Q1/Q3/Q6/Q9/etc, or "client-call:{slug}", or "slack:{thread-id}"
    source_path: ""                      # path to the transcript or artifact, when applicable
    use_eligibility: ""                  # public | client-only | operator-only | NDA-protected
    story_shape: []                      # [build-up, payoff-first, anecdote-then-frame] — 1-3 entries
    last_used_date: ""                   # YYYY-MM-DD; updated when the ghostwriter pulls it
    use_count_lifetime: 0                # incremented on each pull; signals over-use
    operator_rejection_count: 0          # incremented when operator rejects a draft using this story; signals stale
```

The `last_used_date` and `use_count_lifetime` fields drive the quarterly cull (Rule 5). Stories with high use-count and no recent rejections are evergreen; stories with rising rejection-count are stale and exit the bank at the next refresh.

## Failure modes

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| Bank too thin | Ghostwriter ships generic posts week 3+; operator rejects "this isn't anchored in anything specific" | Bank populated with <8 stories or stories tagged superficially | Re-run `voice-extraction-9q.md` Q3 and Q6 with explicit slowdown prompts; pull stories from client-call recordings if interview yield is low |
| Stories invented or composite | Operator says "this didn't happen — I never had a client like that" | Ghostwriter pattern-matched a generic story-shape and filled in operator details | Per `INVARIANTS.md` N-3, halt and remove the entry; re-source from real interview/transcript |
| Real names in the bank | Operator or client recognizes a real name in a draft post | Rule 4 was skipped during intake | Audit the entire bank for real-name leaks; replace with descriptors; add a pre-publish lint that flags any capitalized proper noun in story-bank entries |
| Eligibility crossed | A client recognizes their story in an agency-account post | `use_eligibility` was unset or set incorrectly | Per Rule 6, tag every story on intake; run an audit before any client-bound or agency-bound batch ships |
| Bank becomes stale | Ghostwriter relies on the same 4 stories for 6 months; engagement drops | Quarterly refresh skipped | Run Rule 5 audit immediately; cull stories not pulled in 90 days; expand from past-quarter sources |
| Story forced into wrong shape | Post reads disjointed — the story doesn't support the hook the post uses | `story_shape` tag was missing or the ghostwriter overrode it | Tag every story on intake (Rule 7); when a draft fails for shape-mismatch, log it as a rejection-signal and adjust the shape tag |
| Operator-only story shipped on client account (or vice versa) | "Why is my client talking about my origin story?" | `use_eligibility` defaults applied without per-story review | Default new entries to `NDA-protected` until the operator explicitly upgrades — fail closed, not open |

## Cross-references

- `reference/frameworks/voice/voice-extraction-9q.md` — Q1, Q3, Q6, Q9 are the highest-yield story seeds
- `reference/frameworks/voice/voice-of-customer-extraction.md` — sibling protocol; VOC quotes and stories live in adjacent fields, not the same field
- `skills/extract-founder-voice/SKILL.md` — Step 4 (story bank seeding) runs this framework
- `agents/voice-extractor.md` — owns the bank; tracks decay; triggers quarterly refresh
- `ENCODING.md` Profile 2 — `brand_voice.{scope}.story_bank` schema
- `INVARIANTS.md` N-3, A-11, N-13 — never invent stories or numbers; descriptors only, no real names

---

*Framework version 1.0.0 — 2026-05-03*
