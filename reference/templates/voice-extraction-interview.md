---
template_name: voice-extraction-interview
used_by_skill: /extract-founder-voice
audience: operator | account-manager
runtime_target: 90 minutes (single session)
output_artifact: brand_voice.{scope} draft, ≥75% completeness
---

# Voice Extraction Interview — 90-Minute Script

## Purpose

Drive the 9-question voice-extraction protocol from `reference/frameworks/voice/voice-extraction-9q.md` to produce a usable voice profile draft in a single 90-minute session. Output target: profile completeness ≥75% with the story bank seeded to ≥7 entries and `phrases_to_avoid` populated to ≥10 verbatim entries. Validation gate (the Blind Output Test) runs separately after this session, against fresh-generated posts.

This template covers two modes:
- **Agency-mode** — operator interviewing themselves (or being interviewed by an internal account manager) for `agency-voice`.
- **Client-mode** — account manager interviewing a newly-onboarded client for that client's `client-voice` subprofile under `client_slug`.

The 9 questions are identical across modes per `voice-extraction-9q.md`. The framing, the source corpus, and the file destination differ.

## When the operator/AM uses it

- New operator onboarding (agency-mode, run by operator on themselves with a recorder; or operator interviewed by founding AM)
- New client signed → during onboarding week (client-mode, run by AM with the client; recorded)
- Voice-drift detector flags a client subprofile (re-extraction with focus on the drifted dimensions — partial re-run)
- Quarterly voice-recalibration cadence (full re-run if the operator/client has notably evolved)

## The template

### Section 0 — Pre-interview prep (do BEFORE the session)

Block 30-45 minutes the day before. The interview only works if the interviewer has already loaded the corpus.

**Read before the session:**

1. **{N} most-engaged posts (target 20)** — pull from the subject's LinkedIn profile, the past 90 days, sorted by engagement. Read each twice. Highlight any sentence that sounds like the subject talking, not LinkedIn talking.
2. **{N} most-recent posts (target 20)** — the last 20 posts regardless of engagement. Catches current voice, post-evolution.
3. **{N} lowest-engaged or rejected posts (target 10)** — the posts that didn't land. Often surfaces what the subject was reaching for and missed.
4. **Intake form / onboarding doc** — `{path/to/intake-form-{client_slug or operator}.md}`. Note any voice-related answers ("how would you describe your tone").
5. **Prior call transcripts** — `{path/to/discovery-call-or-onboarding-call}`. The subject's actual speech patterns, unguarded.
6. **Any voice notes, podcast clips, or Loom recordings the subject has produced** — `{paths}`. Highest-signal source per `voice-extraction-9q.md`.

**Build a one-page interviewer brief before the session, capturing:**

- 5-10 phrases that appear 3+ times in the corpus (candidate `phrases_to_use`)
- 3-5 sentence-shapes that don't appear in generic LinkedIn writing (candidate `language_patterns`)
- 5-10 things the corpus *never* does (candidate `phrases_to_avoid`)
- 2-3 stories the subject has clearly told before in their corpus (story-bank seeds to validate in Q3)
- 1-2 contrarian beliefs visible in the posts (candidate Q2/Q8 frames)

This brief is the interviewer's reference during the session. It is not shared with the subject — sharing it primes the subject toward the curated voice the brief was built from.

---

### Section 1 — Recording setup (first 5 minutes of the session)

1. **Consent for recording.** State explicitly: "I'm going to record this session for transcription. The recording is internal — used to build your voice profile and then archived. Are you OK with that?" Wait for verbal yes.
2. **Primary transcript tool:** `{tool — e.g. Otter, Fireflies, Riverside}`. Confirm it's running and capturing both sides. Verify the meeting room or call is on a stable connection.
3. **Backup audio:** local recording on the interviewer's machine (phone voice memo on the desk, or QuickTime audio capture). Recording failures during a 90-minute interview are catastrophic — single-source recording is a known failure mode.
4. **Frame the session for the subject:** "Nine questions. About 90 minutes. Some of the questions will feel weird because they're not the questions you expect. The weirdness is the point — I'm trying to bypass the version of you that gives polished answers and reach the version that talks like you actually talk. Answer in whatever messy form your first thought arrives. Don't pre-edit. We'll clean up later."
5. **Set the rule.** "If a question feels off-target, tell me and I'll re-frame. If you want to come back to one, we will. The only thing I'll push you on is when an answer is too short or too tidy — I'll ask one or two follow-ups, and that's normal."

---

### Section 2 — The 9-question protocol

Each question below carries (a) the prompt to ask verbatim, (b) probe-deeper follow-ups for if the first answer is too short/abstract/sanitized, (c) what the interviewer is listening-for under the surface of the answer.

#### Q1 — The explanation moment (8 min)

- **Prompt:** "Tell me about a time you had to explain your work to someone who knew nothing about it. Walk me through what you said."
- **Probes if first answer is abstract:** "Tell me about the actual conversation. Who was the person? What did you say first?" / "Now do it again, but for your dad / your barber / a cab driver."
- **Listening for:** signature analogies (their go-to "it's like X"), the simplification reflex, jargon-vs-translation tendency, one or two stories told often enough to be polished. Capture any "it's basically..." or "the way I always describe it..." constructions verbatim.

#### Q2 — The defensible belief (8 min)

- **Prompt:** "What's a belief about {their industry / their client's industry} that you'd defend even if it became unpopular?"
- **Probes if hedge-laden:** "Take the hedge off. What do you actually think?" / "What's a belief that would lose you clients if you said it out loud?"
- **Listening for:** the contrarian thesis, conviction-words ("I genuinely believe," "this is non-negotiable," "everyone in this space is wrong about"), the rhetorical posture they take when defending. Note any named opponents they reach for (descriptors only — never let names enter the transcript per `INVARIANTS.md` A-11/N-13).

#### Q3 — The recent client win (10 min)

- **Prompt:** "Walk me through your most recent client win — start to finish. The whole thing, not the highlight reel."
- **Probes if too tight:** "Slow down. What was the first DM? What did the discovery call feel like? What did you almost get wrong?" / "What was the moment on the call where you knew this was going to close?"
- **Listening for:** story-construction defaults (do they start with the problem, the prospect, the moment of insight?), proof-language (specific numbers, dates, names-of-things), the emotional arc they default to. This is the highest-yield story-bank seed — capture every sub-story that surfaces, even ones the subject brushes past.

#### Q4 — The signature phrase (6 min)

- **Prompt:** "What's a phrase or expression you find yourself using a lot? In your work, in your writing, in conversation."
- **Probes if sparse:** Don't push. Sparse here is normal — most subjects don't know their own signature phrases. Lean on the corpus brief from Section 0 instead. If they offer 1-2 phrases, follow up: "Anything your team or partner teases you for saying?"
- **Listening for:** self-aware signature phrases (file under `phrases_to_use` directly). Cross-reference with the corpus-derived list from Section 0 — phrases that appear in both are the highest-confidence entries.

#### Q5 — The actively-avoided phrase (10 min)

- **Prompt:** "What's a phrase or framing you actively avoid using? Things you'd never write, even if they'd get engagement."
- **Probes if abstract:** "Give me five specific phrases. Five." / "Walk me through the phrases that other people in your space use that make you cringe."
- **Listening for:** explicit pattern-rejection (LinkedIn-guru phrases), aesthetic-rejection (rocket emojis, "🚀 Let's dive in," "happy to share"), category-rejection (phrases that pattern-match to competitors the subject is positioning against). This populates `phrases_to_avoid` directly. The list cannot be too long — push for at least 10 specific phrases.

#### Q6 — The worst-fit client (10 min)

- **Prompt:** "Describe the worst-fit client you ever took on. What signals did you miss?"
- **Probes if sanitized:** "What did they say on the discovery call that should have been a flag? What did you tell yourself to ignore it?"
- **Listening for:** voice-under-frustration (closer to actual voice than the polished one), specific narrative details usable as a story, named missed-signals that become DQ filters when the subject is the operator. Capture verbatim phrasings like "I should have known when they said..." — those are story-anchors plus DQ-criterion candidates simultaneously.

---

### MID-INTERVIEW CHECK-IN (15-min mark)

After Q2, take 60 seconds: "Are these questions landing? Anything feeling off?" Recalibrate if needed. If the subject is hedging or performing, reset by saying: "Remember — first thought, even if it's messy. I'm catching you mid-thought, not mid-pitch."

---

#### Q7 — The single DM (6 min)

- **Prompt:** "If you had to send one DM to your ideal client tomorrow, what would it say?"
- **Probes if it's a sales pitch:** "Now write the DM as you'd actually send it on a Tuesday morning when you're warm. Not the demo version." / "What's the question that would make the right ICP reply within the hour?"
- **Listening for:** persuasion default (story-first, mechanism-first, contrarian-provocation, authority-by-proof), the language they think their ICP wants to hear (flag if it doesn't match VOC), the call-to-action shape they default to. This signals `persuasion_style`.

#### Q8 — The disagreed-with opinion (8 min)

- **Prompt:** "What's an opinion you hold that most people in your space disagree with?"
- **Probes if it overlaps Q2:** "If you said this on a podcast tomorrow, who would unfollow you? Who would DM you to thank you?" / "What's the consequence of holding this opinion publicly?"
- **Listening for:** shipped-conviction language (different from private-conviction of Q2), rhetorical-posture-under-attack (calm-corrective, sharp-corrective, dismissive, evangelistic), the named-reaction (descriptors only). This seeds the contrarian-take post-pillar.

---

### MID-INTERVIEW CHECK-IN (60-min mark)

Before Q9, take 60 seconds. "We're at the last question. This is the one that usually surfaces the most useful stuff. I want you to actually let it rip — no LinkedIn-filter on this one." Then move into Q9 without a pause.

---

#### Q9 — The frustration moment (12 min)

- **Prompt:** "Tell me about something that made you angry or frustrated this month — work or otherwise."
- **Probes if short or polished:** "Take the LinkedIn off. What's the actual rant version? Pretend you're talking to your co-founder."
- **Listening for:** cadence patterns (sentence length, rhythm, where they trail off), contractions and idioms ("I'm just over it," "the thing that kills me," "and don't even get me started on"), energy-level under stress (sharp, heavy, dry-amused, weary), topical orbit they default to when frustration unlocks honesty. This is the question that catches the voice-tail no other question reaches. Let silence work — wait for the second-thought, the after-thought, the "and another thing." Those are the voice asset.

---

### Section 3 — Wrap-up (final 10 minutes)

1. **Story-bank confirmation.** "I caught {N} stories during this — {brief one-line callout for each}. Are any of those off-limits, NDA-protected, or stories you don't tell anymore?" Tag use-eligibility per story per `story-bank-seeding.md` Rule 6.
2. **Signature-phrase callouts.** "I noticed you said {phrase} a few times — is that a phrase you're conscious of using, or did I just catch you?" Self-aware signatures get high-confidence tags; unconscious ones get cross-referenced with the corpus.
3. **Calibration of what we'll send back for review.** "I'll process the transcript and send you a draft voice profile within {N business days}. The thing I most need from you on review: flag anything that doesn't sound like you. Even one phrase that feels off matters more than ten that feel right."
4. **One-more-thing prompt.** "Anything you almost said in the last 90 minutes but didn't?" Wait. The "oh, one more thing" answer is often the highest-value entry of the session.
5. **Stop the recording. Confirm the file saved. Confirm the backup audio captured.**

---

### Section 4 — Post-interview processing (within 48 hours)

1. **Transcript processing.** Run the recording through the transcript tool. Manually correct any speaker-attribution errors and any words the auto-transcriber mangled (verbatim accuracy is sacred per `voice-of-customer-extraction.md` — do not "tidy up" the language).
2. **Profile draft hydration.** Per `extract-founder-voice/SKILL.md` Step 5, populate the `brand_voice.{scope}` YAML schema. Cite the source question (or corpus path) for every populated field per `voice-extraction-9q.md` output rule.
3. **Profile completeness scoring.** Score honestly per `extract-founder-voice/SKILL.md`:
   - ≥80% if all fields populated, story bank ≥7, `phrases_to_use` ≥15
   - 60-79% if structural fields populated but story bank or phrases thin
   - <60% if interview cut short or corpus thin → flag for re-extraction
4. **Story-bank tagging.** Per `story-bank-seeding.md` Rule 2, tag every story on every dimension (`pillar_fit`, `characters` as descriptors only per Rule 4, `stakes`, `decision_moment`, `outcome`, `lessons_extractable`, `source_question`, `use_eligibility`, `story_shape`). Untagged stories don't enter the bank.
5. **Send draft to subject for review.** Operator (agency-mode) or client (client-mode). Reviewer's only job: flag anything that doesn't sound like them. Track rejections per `INVARIANTS.md` A-9 — every rejection updates the profile.
6. **Schedule the validation-gate run** per `extract-founder-voice/SKILL.md` validation-gate section. The session is not complete until the Blind Output Test runs.

## Quality bar

- Recording captured both sides cleanly, transcript verbatim, no "for clarity" edits applied.
- All 9 questions asked in order. Q9 not skipped or rushed.
- Story bank seeded with ≥7 stories, each fully tagged per `story-bank-seeding.md` Rule 2.
- `phrases_to_avoid` populated to ≥10 verbatim entries (the field that protects against ghostwriter rejection most).
- Every populated profile field cites its source question or corpus path per `voice-extraction-9q.md`.
- Subject signed off on use-eligibility for every story-bank entry.
- Validation gate (Blind Output Test) scheduled within 7 days of the interview.

## Cross-references

- `skills/extract-founder-voice/SKILL.md` — the skill this template runs the interview phase of
- `reference/frameworks/voice/voice-extraction-9q.md` — the 9-question protocol with deeper rationale per question
- `reference/frameworks/voice/story-bank-seeding.md` — the tagging schema for stories surfaced during Q1, Q3, Q6
- `reference/frameworks/voice/voice-of-customer-extraction.md` — sibling protocol for client-mode (VOC surfaces alongside voice during client interviews)
- `reference/templates/icp-build-interview.md` — sibling template; ICP and voice interviews often run back-to-back during onboarding
- `INVARIANTS.md` N-1, N-6, A-9, A-11, A-12 — voice rules, no contamination, descriptors only, rejection closes the loop
