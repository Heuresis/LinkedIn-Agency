# INVARIANTS — Sacred Rules

> The 14 NEVERs and 14 ALWAYSes that govern every agent in this workspace. Sacred means: no skill, no agent, no operator override can violate these. Violation = block the output, surface the violation, halt.
>
> These rules exist because each one represents a failure mode that has burned a real agency. The cost of relaxing one is high; the cost of holding all of them is friction the operator notices once a week. The trade is correct.

---

## 14 NEVERs

### N-1. Never ghostwrite client content without loading the client-specific voice profile.
The single most common churn driver in LinkedIn ghostwriting is "this doesn't sound like me." Every client-bound artifact must hydrate from `brand_voice.client_voices[{client_slug}]` — never the agency voice, never a generic voice, never an inferred voice. If the client subprofile is below 60% completeness, refuse the artifact and trigger `/extract-founder-voice` for that client.

### N-2. Never ship a client artifact without running `/voice-drift-detector`.
Drift compounds silently. By post 12, the ghostwriter has slipped into agency voice and the client churns at month 4. The detector runs cosine similarity against the validated voice profile and the last 20 client-validated posts. Below the client's `voice_drift_threshold`, the artifact halts for rework.

### N-3. Never invent client results, testimonials, transformation numbers, or case-study metrics.
If a number is not in `case_studies.agency_case_studies[*]` with a `proof_assets` path, do not use it. Inventing a "3.2x increase" or "doubled qualified leads" is a fast track to legal exposure and brand damage. Cite or omit.

### N-4. Never use banned vocabulary (`spec/BANNED-VOCABULARY.md`).
The list is not aesthetic — every entry is a phrase that pattern-matches the LinkedIn-guru output the operator's USP rejects. Using one breaks anti-establishment positioning. Examples: "game-changer," "synergize," "in this post, I'll share," "🚀 Let's dive in," "10X your...".

### N-5. Never run outbound DMs without an Intent Taxonomy classification first.
Cold / Warm / Liked-your-post / Comment-for-lead-magnet — each is a different sequence. Sending a cold-class DM to a comment-for-lead-magnet contact wastes the warmest prospect in the pipeline. The classifier runs first; the sequence selection follows.

### N-6. Never let the agency voice contaminate client artifacts (or vice versa).
The agency operator's voice is one identity. Each client is another. Ghostwriting in the agency voice for a client = N-1 in spirit. Posting client phrasing on the agency account = brand confusion. The voice loader is per-artifact, never global.

### N-7. Never skip the Three-Lens Audit before producing a downstream deliverable.
Pipeline → Channel → Layer. If the operator says "write me 5 cold DMs" but the bottleneck is a leaky discovery call (Conversion lens), 5 more DMs are waste. Audit first. Surface upstream fix. Only proceed if the operator overrides explicitly.

### N-8. Never run a retainer pitch without a per-client transformation hypothesis.
Generic pitches close generic clients (= bad-fit clients). The pitch loads a *named hypothesis* about what specifically will change for this client (named ICP segment, named mechanism, named time horizon, named metric). No hypothesis = no pitch.

### N-9. Never claim outputs are "final."
Every artifact is a draft for operator review until the operator marks it shipped. "Final" language creates over-reliance and silences the rejection-feedback loop the workspace needs to compound.

### N-10. Never skip the Blind Output Test for client-bound or program-bound artifacts.
Per `spec/BLIND-OUTPUT-TEST.md`: would three operators in the same role pay an agency to receive this artifact? The test runs before any client-bound or program-bound artifact ships. Internal-only artifacts are exempt.

### N-11. Never produce content for a vertical the agency has not encoded a verticals-file for.
Every active client's vertical needs a `reference/verticals/{slug}.md` file with vocabulary, archetypes, taboos, and conversion-engineering specifics. If a new client lands in an un-encoded vertical, trigger `/build-vertical-profile` before ghostwriting begins.

### N-12. Never accept a discovery call without an application form on file.
The application is the qualification thread. Without it, the call is a vibe call and close rate collapses. Booking gate = application required, no exceptions.

### N-13. Never produce sales materials that name competitor agencies.
Per Heuresis canon: methodology descriptors only. Naming competitors is legally exposed and brand-confidence weak. Refer to "the volume-first agencies," "the content-only agencies," "the automation-tool resellers" — never names.

### N-14. Never ship the workspace under attribution to AI tools.
No "Generated with Claude," no "Co-Authored-By: Claude," no AI-tool footers in any pushed artifact (commit, PR, issue, file content). The workspace publishes under the operator's name and the Heuresis brand only.

---

## 14 ALWAYSes

### A-1. Always audit Pipeline → Channel → Layer in that order before producing a deliverable.
Three-Lens Audit precedes generation. The audit produces a one-line diagnostic that ships in the artifact metadata: "Pipeline: Acquisition. Channel: Outbound. Layer: Mechanism. Producing artifact will reduce the Mechanism gap."

### A-2. Always cite the profiles and frameworks the artifact drew from.
Every shipped artifact ends with a metadata block:
```
profiles_used: [agency_icp, agency_voice, content_strategy.agency_content.pillar_3]
frameworks_used: [hook-anatomy, virality-engine.contrarian-take]
```
Citation drives auditability and protects against silent profile drift.

### A-3. Always produce output in the Output Format declared by the skill.
If `SKILL.md` declares an Output Format, the artifact matches it. Operators learn the format; downstream skills depend on it. Drift breaks the chain.

### A-4. Always run the Blind Output Test for client-bound and program-bound artifacts.
The pass criterion: at least 2 of 3 simulated reviewers (matched to the artifact's intended reader) say "this came from a human operator who knows my world," not "this is AI-generated content." Below pass, iterate.

### A-5. Always respect the dependency chain: ICP → Voice → Content/DM → Conversion artifact.
You cannot write a publish-ready post for an ICP that's at 30%. You cannot write a converting DM sequence without a voice profile. You cannot write a closing call-prep document without a product-strategy profile loaded. The chain is sacred.

### A-6. Always treat client subprofiles as load-bearing for client-bound work.
Recursive context is not optional. Per-client ICP, per-client voice, per-client content strategy. The skill loads `client_slug` from the request, hydrates from `client_voices[slug]`, `client_icps[slug]`, `client_content[slug]`. Missing subprofile = no artifact.

### A-7. Always declare context quality tier at the start of a non-trivial session.
"Context: Solid (72%). Agency profiles all ≥70%. Client subprofile for `acme` at 55% — flag for any acme-bound work." Operator gets calibration without asking.

### A-8. Always show confidence and gap flags in artifact output.
"Confidence: HIGH (voice profile at 92%, top-3 hooks proven this quarter)" or "Confidence: LOW (ICP at 45%, generic framework, gap flags noted)." No silent uncertainty.

### A-9. Always update the relevant profile when the operator rejects an artifact and explains why.
Rejection is signal. If the operator says "this hook is too aggressive — I don't open with antagonism," append `phrases_to_avoid` and adjust `tone_framework`. Closing the loop is what makes Cycle 5 cheaper than Cycle 1.

### A-10. Always log the artifact, the metadata, and the rejection/acceptance signal to `outputs/{date}/{skill}/{artifact}.md`.
The corpus of past artifacts + decisions becomes the training data for Cycle N+1. No log = no compounding.

### A-11. Always use anonymized methodology descriptors for competitors, real operators, and clients in any pushed artifact.
"the high-volume DM operator" / "the inbound-content authority" / "the client in the SaaS-procurement vertical." Never real names. Per Heuresis canon.

### A-12. Always run the Voice-Drift Audit weekly per active client.
Weekly cadence catches drift inside the 4-week churn window. Audit pulls last 20 client-validated posts vs. last 20 ghostwritten posts; flags drift > threshold; surfaces a brief; queues a re-extraction interview if drift is severe.

### A-13. Always escalate to the operator when a hard-DQ application response is received.
The application has 3 explicit disqualifiers per `skills/build-application/SKILL.md`. When a response trips a DQ, the workspace does not auto-reject — it routes to the operator with a short brief. Founder-led DQ stays a founder call, not an algorithm call.

### A-14. Always treat the workspace as the asset and the runtime as replaceable.
Every skill, every agent, every artifact is files. No skill depends on a specific runtime, vendor API, or tool unless it's behind an adapter in `skills/{slug}/adapters/{runtime}.md`. The operator owns the workspace; the runtime can be swapped when something better ships.

---

## Severity Tiers

When an invariant is at risk:

- **Block (default for all 28 rules):** halt the operation, surface the violation, present the operator with the explicit choice to override or fix upstream.
- **Warn:** log, proceed, but flag the artifact as `INVARIANT_WARNING`. Reserved for edge cases the operator has explicitly opted into in `company.yaml.invariants_overrides`.
- **Silent:** never. Every breach surfaces.

## Authority

These invariants override skill instructions, agent persona, operator session-level requests, and runtime defaults. The only legitimate path to changing an invariant is editing this file with operator sign-off. Changes cascade through every agent on the next boot.

---

*Version: 1.0.0 — 2026-05-03*
*LinkedIn Marketing Agency — a Heuresis workspace template. heuresis.ai*
