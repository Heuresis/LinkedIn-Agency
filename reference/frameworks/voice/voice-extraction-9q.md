# Voice Extraction — The 9-Question Protocol

> The structural rules for the 9-question interview that surfaces a usable voice profile from a person who has spent years performing a sanitized version of themselves on professional platforms.

## Why this framework exists

Voice profiles built from "describe your tone" prompts produce LinkedIn cliché back. The operator who answers in the language of their last brand-strategy session does not give us the voice that converts on a feed full of people performing the recorded version of themselves.

This 9-question protocol is engineered around one conviction: **conversational voice is the asset, not publishable voice.** Every question is shaped to bypass the curated layer (the LinkedIn-ready answers, the elevator pitch, the about-page paragraph) and surface the unguarded layer underneath — the contractions, the idioms, the verbal tics, the way a person talks when they are not being recorded.

The protocol drives `/extract-founder-voice` for both scopes:
- **agency-voice** (operator's own voice, used for the agency's content)
- **client-voice** (per-client subprofile, used for ghostwritten client content)

The same 9 questions apply to both scopes. The framing shifts (an operator interview versus a client interview), the source materials shift (operator's own corpus versus client's corpus), but the question structure holds. This is by design — the 9 questions are calibrated to what voice extraction *needs*, not who the subject is.

The output of this protocol feeds:
- `brand_voice.agency_voice` or `brand_voice.client_voices[{client_slug}]` (per `ENCODING.md` Profile 2)
- `story-bank-seeding.md` (the story bank built from Q1, Q3, Q6 answers)
- `voice-of-customer-extraction.md` (when the subject is a client, ICP-language patterns surface in Q2 and Q9)

## The 9-question protocol

Run in this order. The order matters — questions early in the sequence (Q1, Q2) warm the subject into specific-and-conversational mode; questions later in the sequence (Q7, Q8, Q9) require that warmth to surface unguarded answers. Reordering or skipping degrades the output.

The interview runs 60-90 minutes for a clean extraction. Allocate roughly: 8 minutes per question through Q1-Q6, then ramp time on Q7-Q9 (where the unguarded layer surfaces and silence does work). Leave 10 minutes at the end for the subject to add anything they almost said but didn't — that "oh, one more thing" answer is often the highest-value entry of the session.

The interviewer's job: ask the question, then shut up. Resist the reflex to paraphrase, summarize, or "translate" the subject's answer into cleaner sentences. The messy original phrasing is the asset; the cleaned-up version is contamination. Record verbatim. Transcribe verbatim. File verbatim.

A note on probes: the probes listed under each question are not mandatory follow-ups. They are escape hatches for when the answer arrives in a form the protocol can't use (too abstract, too sanitized, too short). If the first answer arrives unguarded and specific, accept it and move on — over-probing is its own failure mode (it signals the subject that their first answer was inadequate, and they reach for a more "polished" answer on the next question).

### Q1 — "Tell me about a time you had to explain your work to someone who knew nothing about it."

- **Purpose.** Surface the subject's default-explanatory voice — the analogy or example they reach for when there is no jargon to hide behind. This is the cleanest signal of how they actually think about what they do.
- **Expected answer length.** 2-4 minutes of speech, 200-400 words transcribed.
- **What the answer surfaces.** Signature analogies (their go-to "it's like X" framings), the simplification they default to, the audience-empathy reflex (do they translate or do they double down on jargon?), and one or two stories they tell often enough to have polished.
- **What to probe for.** If the answer is abstract ("I help companies grow"), push: "Tell me about the actual conversation. Who was the person? What did you say first?" If the answer is jargon-heavy, push: "Now do it again, but for your dad / your barber / a cab driver."

### Q2 — "What's a belief about [your industry / your client's industry] that you'd defend even if it became unpopular?"

- **Purpose.** Surface conviction — the contrarian belief that the subject holds against market consensus. Conviction is what makes content distinctive; without it, every post is a polite restatement of what everyone in the category already says.
- **Expected answer length.** 2-3 minutes, 150-300 words.
- **What the answer surfaces.** The contrarian thesis, the conviction-words ("I genuinely believe," "this is non-negotiable," "everyone in this space is wrong about"), the rhetorical posture they take when defending themselves, and the specific examples they cite as proof.
- **What to probe for.** If the answer is hedge-laden ("well, it depends" / "I think most people would agree that..."), push: "Take the hedge off. What do you actually think?" If the answer is a generic industry-pet-peeve, push: "What's a belief that would lose you clients if you said it out loud?"

### Q3 — "Walk me through your most recent client win — start to finish."

- **Purpose.** Surface story-shape — how the subject naturally constructs a narrative when they are recounting something that actually happened. Most subjects perform a different story-shape on LinkedIn (payoff-first, framework-led) than they use in conversation (chronological, messy, with asides). The conversational shape is the asset.
- **Expected answer length.** 5-8 minutes, 500-1000 words.
- **What the answer surfaces.** Story-construction defaults (do they start with the problem, the prospect, the moment of insight?), proof-language (the specific numbers, dates, names-of-things they reach for), the emotional arc they default to, and at least one usable story for the story bank.
- **What to probe for.** If the answer is too tight ("we got them from X to Y in 90 days, here's how"), push: "Slow down. What was the first DM? What did the discovery call feel like? What did you almost get wrong?" The texture is in the slowdowns.

### Q4 — "What's a phrase or expression you find yourself using a lot?"

- **Purpose.** Surface signature phrases the subject uses without noticing — the verbal tics, the catch-phrases, the framings that have entered their muscle memory. These are the highest-leverage entries in `phrases_to_use`.
- **Expected answer length.** 1-2 minutes, 50-150 words. (Plus follow-up listening.)
- **What the answer surfaces.** Self-aware signature phrases (the ones the subject knows they use), and — more valuably — the *absence* of awareness, which signals that the corpus and the people-around-them quote are the better source.
- **What to probe for.** If the answer is sparse, do not push the subject — instead, lean on the corpus (read 20+ posts, find phrases that appear 3+ times) and the people-quote ("ask your co-founder / your partner what phrase they tease you for using"). The unconscious phrases are the most valuable; the subject often cannot name them.

### Q5 — "What's a phrase or framing you actively avoid using?"

- **Purpose.** Surface `phrases_to_avoid` — the explicit anti-list. This is the highest-priority field in the voice profile because banned phrases have asymmetric cost: one banned phrase in a ghostwritten post triggers "this isn't me" rejection, while ten unsigned phrases produce a passable post.
- **Expected answer length.** 2-3 minutes, 150-300 words.
- **What the answer surfaces.** Explicit pattern-rejection (LinkedIn-guru phrases the subject won't write), aesthetic-rejection (rocket emojis, "🚀 Let's dive in," "happy to share"), category-rejection (phrases that pattern-match to competitors the subject is positioning against).
- **What to probe for.** If the answer is short ("I don't like fluff"), push: "Give me five specific phrases. Five." Specificity is the asset; abstract avoidance is unusable downstream.

### Q6 — "Describe the worst-fit client you ever took on. What signals did you miss?"

- **Purpose.** Surface voice-under-frustration and ICP-by-negation. This question doubles as a story bank seed (Q3 + Q6 are the highest-yield story sources) and an ICP signal source (the missed signals become DQ filters when the subject is the agency operator).
- **Expected answer length.** 4-6 minutes, 400-800 words.
- **What the answer surfaces.** The subject's voice when they are frustrated (which is usually closer to their actual voice than the polished one), specific narrative details usable as a story, and the named missed-signals that become disqualifier patterns.
- **What to probe for.** If the answer is sanitized ("they weren't a fit"), push: "What did they say on the discovery call that should have been a flag? What did you tell yourself to ignore it?" The self-blame layer is where the unguarded voice lives.

### Q7 — "If you had to send one DM to your ideal client tomorrow, what would it say?"

- **Purpose.** Surface persuasion-style under constraint. One DM forces compression — and compression reveals the persuasion frame the subject defaults to (story-first, mechanism-first, contrarian-provocation, authority-by-proof).
- **Expected answer length.** 1-2 minutes, 100-200 words.
- **What the answer surfaces.** Persuasion default (the lever they reach for first when they have one shot), the language they think their ICP wants to hear (which may or may not match actual VOC — flag the gap), the call-to-action shape they default to.
- **What to probe for.** If the answer is a sales pitch, push: "Now write the DM as you'd actually send it on a Tuesday morning when you're warm. Not the demo version." If the answer is a question-only DM, push: "What's the question that would make the right ICP reply within the hour?"

### Q8 — "What's an opinion you hold that most people in your space disagree with?"

- **Purpose.** Surface contrarian-positioning specificity. Q2 surfaces the *belief*; Q8 surfaces the *positioning consequence* of the belief — the take that, if shipped on LinkedIn, would draw both a defender camp and an opponent camp. This is the seed for contrarian-take post-types.
- **Expected answer length.** 2-3 minutes, 150-300 words.
- **What the answer surfaces.** The shipped-conviction language (different from the private-conviction language of Q2), the rhetorical-posture-under-attack (calm-corrective, sharp-corrective, dismissive, evangelistic), and the named opponents (descriptors, never names, per `INVARIANTS.md` A-11 / N-13).
- **What to probe for.** If the opinion overlaps Q2, push for a *consequence*: "If you said this on a podcast tomorrow, who would unfollow you? Who would DM you to thank you?" The named-reaction reveals the audience-shape the post would create.

### Q9 — "Tell me about something that made you angry or frustrated this month — work or otherwise."

- **Purpose.** Surface unguarded voice in its most distilled form. Anger and frustration produce the cleanest signal of cadence, contractions, idioms, and the way the subject actually talks when they are not curating. This is the question that catches the voice-tail no other question reaches.
- **Expected answer length.** 3-5 minutes, 300-600 words.
- **What the answer surfaces.** Cadence patterns (sentence length, rhythm, where they trail off), contractions and idioms ("I'm just over it," "the thing that kills me," "and don't even get me started on"), the energy-level under stress (sharp, heavy, dry-amused, weary), and the topical orbit they default to when frustration unlocks honesty.
- **What to probe for.** If the answer is short or polished, push: "Take the LinkedIn off. What's the actual rant version? Pretend you're talking to your co-founder." This is the question where silence works hardest — wait for the second-thought, the after-thought, the "and another thing." Those are the voice asset.

## Output template

The output of the 9-question protocol hydrates the voice profile schema. Cross-reference each populated field with the question (or corpus path) that surfaced it. The structural slots that depend on each question:

```yaml
brand_voice.{scope}:
  communication_style: ""              # cross-question synthesis (Q1, Q3, Q9)
  tone_framework:
    primary: ""                        # cross-question synthesis (Q3, Q9)
    secondary: ""                      # contrast layer (Q2, Q8)
  personality_traits: []               # 3-5 traits, each anchored to a question (cite Q#)
  language_patterns: []                # cadence patterns (Q1, Q9 primary; corpus secondary)
  phrases_to_use: []                   # 10-25 verbatim (Q4 primary; corpus + Q3 + Q9 secondary)
  phrases_to_avoid: []                 # 10-25 verbatim (Q5 primary; corpus-confirmed never-uses)
  persuasion_style: ""                 # one of authority-by-proof / story-first / mechanism-first / contrarian-provocation (Q7)
  authority_positioning: ""            # category-king / challenger / insider / outsider-with-edge (Q2, Q8)
  story_bank: []                       # 8-15 stories (Q1, Q3, Q6 primary — see story-bank-seeding.md)
  voice_drift_threshold: 0.82          # default; tune from rejection signal
```

Every populated field must cite the question (or corpus path) it was sourced from. Citation drives auditability — when downstream rejects an artifact and the trace runs back to the wrong source, the agent knows which question to re-run.

## Failure modes

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| Generic-LinkedIn-voice contamination | Profile reads as "professional, authoritative, direct" — could describe anyone | Subject answered all 9 questions in their LinkedIn-ready register | Re-run Q9 first ("the rant version"), then loop Q1 and Q3 with the unguarded register established |
| Idealized-self profile | Profile describes who the subject wishes they sounded like, not who they are | No unguarded source provided (no voice notes, no Slack threads, no off-the-record podcast) | Refuse the profile per `INVARIANTS.md` N-1 spirit; require at least one unguarded source before resuming |
| Thin story bank | Profile completes structurally but only has 3-4 stories | Q3 and Q6 were rushed; subject defaulted to summary instead of narrative | Re-run Q3 and Q6 with explicit slowdowns ("what was the first DM," "what did you almost get wrong"); see `story-bank-seeding.md` |
| Banned-phrases list too short | `phrases_to_avoid` has 3 entries instead of 10-25 | Q5 was answered abstractly ("I don't like fluff") | Re-run Q5 with the "give me five specific phrases" prompt; cross-reference corpus for never-uses |
| Voice extracted from stale corpus | Profile calibrates to past-subject, not current-subject | Most-recent posts in the corpus are >6 months old | Require last-90-days posts in the corpus; if unavailable, mark profile as `extraction-pending` and schedule re-interview |
| Wrong scope variant run | Agency-mode interview run on a client (or vice versa) | Scope not declared at invocation; defaults applied | Re-run with explicit scope; the 9 questions are the same, the framing and source materials are not |
| Q9 skipped because "we got enough from Q1-Q8" | Profile lacks unguarded-cadence patterns; ghostwriter output reads as too-clean | Operator (or interviewer) treated Q9 as optional | Q9 is sacred — never skip. The unguarded-cadence layer cannot be reconstructed from any other question |

## Cross-references

- `skills/extract-founder-voice/SKILL.md` — the skill that runs this protocol end-to-end
- `agents/voice-extractor.md` — the agent that owns the protocol
- `reference/frameworks/voice/story-bank-seeding.md` — the story bank built from Q1, Q3, Q6
- `reference/frameworks/voice/voice-of-customer-extraction.md` — the verbatim-VOC protocol that runs alongside (when scope is client-voice)
- `reference/templates/voice-extraction-interview.md` — the operator-facing interview template (agency-mode + client-mode variants)
- `ENCODING.md` Profile 2 — `brand_voice` schema (agency + per-client recursive)
- `INVARIANTS.md` N-1, N-6, A-9, A-12 — voice-loading rules, no contamination, rejection signal closes the loop

---

*Framework version 1.0.0 — 2026-05-03*
