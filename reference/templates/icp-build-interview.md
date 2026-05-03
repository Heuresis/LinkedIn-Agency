---
template_name: icp-build-interview
used_by_skill: /build-icp
audience: operator | account-manager
runtime_target: 60 minutes (single session)
output_artifact: ideal_customer_profile.{scope} draft, ≥75% completeness with verbatim VOC
---

# ICP Build Interview — 60-Minute Script

## Purpose

Drive the 12-question ICP protocol from `skills/build-icp/SKILL.md` to produce an ICP profile draft in a single 60-minute session. Output target: profile completeness ≥75% with the `voice_of_customer` block populated to ≥30 verbatim quotes (per `voice-of-customer-extraction.md` Rule 4) and the disqualifier set defined explicitly enough to gate the application form.

This template covers two modes:
- **Agency-mode** — operator interviewing themselves (or being interviewed by an internal AM) for `agency-icp` (who the agency sells to).
- **Client-mode** — AM interviewing a newly-onboarded client for that client's `client-icp` subprofile under `client_slug` (who the client sells to).

The 12 questions are identical across modes. The framing, the source deals, and the file destination differ. Per `INVARIANTS.md` A-6: agency-ICP and client-of-client-ICP are separate profiles, never collapsed.

## When the operator/AM uses it

- New operator onboarding (agency-mode, run during foundations week)
- New client signed → during onboarding (client-mode, run alongside or just after the voice-extraction interview)
- Performance signal: discovery call close-rate <25%, or post engagement is high but inbound calls don't convert → ICP refinement (partial re-run)
- Vertical pivot or new segment expansion (full new build for the new segment)

## The template

### Section 0 — Pre-interview prep (do BEFORE the session)

Block 30-60 minutes the day before. The 12-question interview is engineered to consume the operator's pattern-match across 10+ closed-won deals. If the prep isn't done, the answers default to abstraction and the profile fails the specificity gate.

**Read or pull before the session:**

1. **Past closed-won deals (target 10-20)** — pull from `outputs/{date}/closed-won-log.md` or the operator's CRM. Per deal, capture: role + company stage + revenue stage + industry + trigger event + decision-making + sales motion + time-to-close + what they actually said on the call. Build the closed-won pattern grid from `build-icp/SKILL.md` Step 1 *before* the interview, not during.
2. **Past closed-lost deals (target 5-10) with reasons** — `outputs/{date}/closed-lost-log.md`. The contrast set. Per `build-icp/SKILL.md` Step 2: which dimension did each fail on? This becomes the disqualifier set the interview confirms.
3. **Churn cases from past clients (target 3-5)** — clients who signed but left within 6 months. Why? What signals were missed at qualification? These often reveal hard-DQs that closed-lost analysis alone misses.
4. **Sales-call recordings or DM thread transcripts (target 3-5)** — for verbatim VOC capture per Rule 1 of `voice-of-customer-extraction.md`. Highest-quality VOC source. Pull both winning calls and losing calls.
5. **Competitor evidence** — descriptors only per `INVARIANTS.md` A-11/N-13. Note which competitor categories the prospects have tried before (the "volume-first agencies," "the content-only agencies," "the automation-tool resellers") and what they said about those experiences.
6. **The intake form / onboarding doc** — `{path}`. Note any ICP-related answers the operator (or client) already provided.

**Build a one-page interviewer brief before the session:**

- The closed-won pattern grid (Step 1 of `/build-icp` — pre-filled, not derived during the interview)
- The candidate disqualifier list (Step 2 — pre-filled, to validate during the interview)
- 5-10 verbatim quotes already extracted from sales-call transcripts (to seed the `voice_of_customer` bank during Q9)
- The hypothesized limiting-belief mix (Helpless / Hopeless / Worthless — to validate during Q4, Q5, Q6, Q10)

This brief is for the interviewer only. Don't share with the subject — sharing primes them toward what they think the interviewer wants to hear.

---

### Section 1 — Recording setup (first 5 minutes of the session)

1. **Consent for recording.** "I'm recording this for transcription. Internal use only — builds your ICP profile. OK?" Wait for verbal yes.
2. **Primary transcript tool:** `{tool}`. Confirm both sides captured. Local backup audio running.
3. **Frame the session.** "Twelve questions. About 60 minutes. The first four cover *who* — the buyer. The next four cover *what* — what they want and what's blocked them. Three cover *why* — what makes them decide. The last one captures the disqualifiers. Answer with specifics: actual deals, actual conversations, actual phrases. Generic answers ('B2B founders who want growth') mean we have to redo this — we won't ship a profile that vague."
4. **Set the rule.** "If a question feels off, push back. If you can't think of an answer, that's data — it tells us where the closed-won pattern thins out. We'll mark those gaps and decide whether to draft or re-run later."

---

### Section 2 — The 12-question protocol

Each question carries (a) the prompt to ask verbatim, (b) probe-deeper follow-ups, (c) what the interviewer is listening-for under the surface.

#### Q1 (WHO — role) — The 1-sentence profiles (5 min)

- **Prompt:** "Describe your last 3 closed-won clients in 1 sentence each — by what they were *trying to do* when they hired you, not what they bought."
- **Probes if abstract:** "Stop describing the company. Tell me about the human. What was their job title? What had they been trying to fix for the last 90 days?"
- **Listening for:** the psychographic-desire pattern across 3 deals. Look for repeat trigger phrases ("I just need...", "I'm tired of...", "I want to stop..."). These feed `psychographics.desires` and `psychographics.aspirations`.

#### Q2 (WHO — stage) — The trigger moment (5 min)

- **Prompt:** "Of those 3, what was the moment in the prior 90 days that made them think 'I need to fix this now'?"
- **Probes if vague:** "What was the specific event? Did someone leave? Did a number drop? Did a competitor ship something?"
- **Listening for:** named buying triggers (events, not states). "Their VP of Sales quit." "They missed pipeline two quarters in a row." These feed `behavioral_patterns.buying_triggers`. Triggers in plural across the 3 deals = ICP triggers; triggers in singular = noise.

#### Q3 (WHO — vertical) — The success metric (5 min)

- **Prompt:** "What's the one number on their dashboard that, if it doesn't move in 60 days, makes them fire you?"
- **Probes if generic ('revenue'):** "More specific. Which line? Pipeline value? Closed-won? Reply rate? Application-submission rate?"
- **Listening for:** the metric the ICP actually watches. This is the metric the offer's transformation hypothesis must move per `INVARIANTS.md` N-8. Different ICP segments watch different metrics — capture per-segment if the operator names them.

#### Q4 (WHO — geography & company shape) — The prior failure (5 min)

- **Prompt:** "What did each of them try BEFORE you that didn't work, and why didn't it?"
- **Probes if surface-level:** "Did they hire an agency? Build in-house? Use a tool? What did they spend on each, and what made them stop?"
- **Listening for:** solution awareness level (Naive / Aware / Skeptical / Exhausted per `build-icp/SKILL.md` Step 6) and prior-agency baggage. "Exhausted" ICPs need different content + DM language than "Aware" ICPs. Capture the named alternatives (descriptors only) — these become positioning anchors.

---

#### Q5 (WHAT — desire) — The smart-thing-said signal (5 min)

- **Prompt:** "What's the smartest thing they each said on the discovery call that told you they were the right fit?"
- **Probes if the operator can't recall:** "Pull up the call recording. What was the moment you knew this was going to close?"
- **Listening for:** signals of qualified leads — the language pattern that distinguishes high-fit from low-fit on the call. Capture verbatim. These feed `behavioral_patterns.trust_signals_required` and seed the qualification-thread checkpoints per `/build-qualification-thread`.

#### Q6 (WHAT — pain) — The dumb-objection signal (5 min)

- **Prompt:** "What's the dumbest objection you keep hearing from people who DON'T close?"
- **Probes if too charitable:** "What's the objection that makes you internally roll your eyes? The one you've heard 30 times that always means the deal is dead?"
- **Listening for:** disqualifier patterns. The dumb objection is a soft-DQ candidate; the rationalization-pattern behind it often points to a hard-DQ root. These feed `psychographics.objections` and the disqualifier set the application form gates on.

#### Q7 (WHAT — current solutions tried) — The profile signal (5 min)

- **Prompt:** "What does an ideal-fit lead's LinkedIn profile look like? Headline, company size, recent posts, who they engage with?"
- **Probes if generic:** "If I gave you a stack of 50 profiles and said 'pick the 5 that would close,' what would you look at first? Walk me through the scan."
- **Listening for:** firmographic + research-behavior signals usable by `/source-lead-list`. The headline patterns, the company-size brackets, the post-cadence signals, the engagement-pattern signals. These determine ICP-fit scoring downstream.

#### Q8 (WHAT — barriers) — The follow-and-engage signal (5 min)

- **Prompt:** "Who do they currently follow on LinkedIn? Whose content do they comment on?"
- **Probes if uncertain:** "Pull up your last 5 closed-won prospects' LinkedIn profiles. What do their 'recently engaged with' lists have in common?"
- **Listening for:** research behavior and where the ICP can be found. These feed `behavioral_patterns.research_behavior` and the source-list strategy. If the ICP follows specific descriptors-of-creators, those creators' comment sections become outbound surfaces.

---

#### Q9 (WHY — verbatim VOC) — The phrase-tag signal (5 min)

- **Prompt:** "What's a phrase they use that nobody outside this segment uses? Verbatim — exactly how you'd hear it on a call."
- **Probes if paraphrased:** "Don't translate it. Quote it. The actual words." Per `voice-of-customer-extraction.md` Rule 2: if the operator can't produce the verbatim, file as `operator_paraphrase_pending_verification` — never as VOC.
- **Listening for:** segment-specific language. These feed `voice_of_customer.actual_customer_language` and become hook-library + DM personalization anchors. Cross-reference against the verbatim quotes pulled from call transcripts in Section 0.

#### Q10 (WHY — fears) — The dominant-fear signal (5 min)

- **Prompt:** "What's their dominant fear about working with another agency?"
- **Probes if abstract:** "What did the last losing prospect say at the end of the call when they were stalling? What's the fear underneath the polite no?"
- **Listening for:** `psychographics.fears` and the limiting-belief axis (Helpless / Hopeless / Worthless per Step 5 of `/build-icp`). "I've been burned before" = Hopeless. "I don't know how to manage an agency" = Helpless. "I'm not the kind of founder whose stuff works" = Worthless.

#### Q11 (WHY — decision-making) — The internal-Slack signal (5 min)

- **Prompt:** "What does their internal Slack look like the week before they sign you?"
- **Probes if speculative:** "On the closed-won deals — did they CC anyone on the email thread? Was there a partner, a co-founder, a head of growth involved? Whose sign-off did they need?"
- **Listening for:** decision-making process. These feed `behavioral_patterns.decision_making_process`. Solo-decision ICPs need a different sales motion than committee-decision ICPs.

---

#### Q12 (HARD-DQ FILTER) — The silent-objection signal (5 min)

- **Prompt:** "What's the one thing they would have changed about how you sold them, and why didn't they say it? AND — what's the one trait that would make you turn down a discovery call even if the deal was big?"
- **Probes if the second half is dodged:** "If you could put one disqualifier on the application form right now that would auto-reject the worst-fit clients, what would it be?"
- **Listening for:** the hard-DQ set per `/build-application`. Three explicit disqualifiers minimum. These become the application-form gate criteria and the operator-review trigger per `INVARIANTS.md` A-13. Capture each as a one-line rule (e.g. "founder is not the decision-maker" / "expects deliverable in <14 days" / "previous agency was {descriptor}").

---

### Section 3 — Voice-of-customer slot capture (mid-interview, parallel)

While the operator answers Q5, Q6, Q9, Q10, the interviewer keeps a verbatim VOC ledger running on a separate doc. Per `voice-of-customer-extraction.md` Rule 4, the bank needs ≥30 verbatim quotes to pass validation. Sources during this session:

- **The operator's own paraphrase of what prospects said** — files into `operator_paraphrase_pending_verification`, NOT into the VOC bank. Verification happens later when call transcripts confirm.
- **Direct quotes the operator pulls up from a call transcript during the session** — files directly into the VOC bank with `source_path` cited.
- **Phrases the operator captures from the verbatim Q9 answer** — files into `pain_language_patterns` or `framing_language_patterns` per category-fit.

If the bank ends the session below 15 verbatim quotes, schedule a separate VOC-capture pass against 5+ call transcripts. The interview alone rarely produces a complete bank.

---

### Section 4 — Limiting-belief triad capture (final 10 minutes)

From the answers to Q4, Q5, Q6, Q10, classify the ICP's dominant belief per `/build-icp` Step 5:

- **Helpless** ("I don't know how to do this myself") → ICP needs `capability` transformation. Content + DMs lean toward demonstrating-the-mechanism + showing-how.
- **Hopeless** ("I've tried everything") → ICP needs `safety` transformation. Content + DMs lean toward proof + risk-reversal + named-mechanism.
- **Worthless** ("I'm not the kind of person whose posts go viral") → ICP needs `status` transformation. Content + DMs lean toward identity-shift + positioning + before-after.

Capture **3 limiting beliefs the ICP holds about the category** (descriptors of the category, not the agency). Examples for an agency-ICP of B2B founders considering LinkedIn agencies:
- "LinkedIn agencies are mostly volume-and-spam plays." (skepticism-of-category)
- "Ghostwriting only works if I'm a thought-leader already." (capability-doubt)
- "If I do this myself for 30 days I can replace what the agency would do." (replacement-fantasy)

Each limiting belief becomes a content-pillar argument-target and a DM-objection-rebuttal-target. They feed `psychographics.beliefs` directly.

---

### Section 5 — Wrap-up (final 10 minutes)

1. **The specificity gate dry-run** — per `/build-icp` specificity gate:
   - "Name 5 real companies (descriptors only) that fit this ICP." Operator should produce in <60 seconds. Capture each.
   - "Name 5 real companies (descriptors only) that look like they fit but don't, and tell me why each one fails." Operator should produce in <90 seconds. Capture each with the disqualifier-reason.
   - If the operator can't hit either bar, the profile is too broad — flag for re-run with sharper firmographics before declaring `validated`.
2. **One-more-thing prompt.** "Anything I should have asked but didn't?" Often surfaces the thing the operator has been pattern-matching but hasn't articulated.
3. **Calibration of next steps.** "I'll process the transcript and the closed-won pattern grid into a profile draft within {N business days}. Review will focus on: do the 5 fits and 5 lookalike-non-fits hold? Is the disqualifier set tight enough to gate the application form?"
4. **Stop the recording. Confirm save. Confirm backup audio.**

---

### Section 6 — Post-interview processing (within 48 hours)

1. **Hydrate the YAML profile** per `/build-icp` Step 6 schema. Cite sources for every populated field.
2. **VOC bank reconciliation.** Move verified quotes into `voice_of_customer`; keep paraphrases in `operator_paraphrase_pending_verification` until the next call surfaces them verbatim.
3. **Disqualifier set finalization.** Three explicit hard-DQs minimum. Format for direct consumption by `/build-application`.
4. **Specificity-gate documentation.** Log the 5 fits + 5 lookalike-non-fits + the 3-post engagement-prediction check (run separately within 7 days).
5. **Send draft to operator (or client) for review.** Reviewer's primary job: validate the 5/5 specificity gate and confirm the disqualifier set is tight enough.
6. **Schedule the engagement-prediction check** — pick 3 random posts from the operator's content, predict ICP engagement, compare to actual. 2-of-3 prediction-match required to pass per `/build-icp` specificity gate.

## Quality bar

- All 12 questions asked in order. Q12 (hard-DQ) not skipped.
- Closed-won pattern grid pre-built (Section 0 prep) — not derived during the interview.
- Voice-of-customer bank populated with ≥15 verbatim quotes from the session + transcript pulls; pathway to ≥30 within 14 days.
- 3 limiting beliefs captured with category-descriptor framing (not agency-descriptor).
- Specificity gate dry-run completed in the session (5 fits + 5 lookalike-non-fits named in <2.5 minutes total).
- Disqualifier set ≥3 explicit hard-DQs, each formatted as a one-line application-form rule.
- Subject signed off on the gap flags (anything thin, anything missing, anything pending verification).

## Cross-references

- `skills/build-icp/SKILL.md` — the skill this template runs the interview phase of
- `reference/frameworks/voice/voice-of-customer-extraction.md` — the verbatim-VOC protocol that runs alongside the interview
- `reference/templates/voice-extraction-interview.md` — sibling template; ICP and voice interviews often run back-to-back during onboarding
- `skills/build-application/SKILL.md` — consumes the disqualifier set this interview captures
- `skills/build-outbound-engine/SKILL.md` — consumes the VOC bank for DM construction
- `INVARIANTS.md` A-5 (dependency chain ICP → Voice → Content/DM), A-6 (recursive subprofiles), A-11 (descriptors only), A-13 (operator on hard-DQ), N-8 (per-client transformation hypothesis), N-12 (application before calendar)
