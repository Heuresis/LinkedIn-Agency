# Blind Output Test

> The publish-bar gate. Every client-bound or program-bound artifact runs through this test before ship. The test answers one question:
>
> **"Would a B2B founder pay an agency for this?"**
>
> Pass = ship. Fail = iterate the artifact OR iterate the encoding (often both).

## Why this exists

The workspace's value proposition is that it produces work that outperforms what a paid LinkedIn agency would deliver. That claim is testable. This is the test.

Without the gate, the workspace ships generic-good content — content that's cleaner than DIY but indistinguishable from $3K/month commodity-agency output. That's a workspace that breaks even, not the kind of workspace that makes the operator's competitors irrelevant.

The gate prevents drift toward the commodity-agency mean.

## When the test runs

Hard-required for these artifact types:
- LinkedIn post (founder OR client ghostwritten)
- DM sequence (any class)
- Discovery call prep doc
- Retainer proposal
- Monthly client report
- Lead magnet
- Sales-page copy (program or service offer)
- Curriculum module

Not required for:
- Internal artifacts (operator notes, working drafts, profile interview transcripts)
- Routing decisions (agency-director diagnostics)
- Lead-list research files (intermediate work product)

## The test protocol

### Step 1 — Identify the intended reader

For each artifact, identify exactly who would receive it. Examples:
- LinkedIn post → ICP target reader (specific role + situation)
- DM sequence → ICP-matched cold prospect
- Retainer proposal → B2B founder who completed the application
- Monthly client report → the specific client (named in metadata)
- Curriculum module → enrolled program student at the relevant stage

### Step 2 — Generate 3 simulated reviewer profiles

Each simulated reviewer is a persona matched to the intended reader. The persona has:
- Specific role + company stage + revenue tier
- Specific prior experience with the artifact category (e.g., has hired a LinkedIn agency before for the proposal test)
- Specific bias profile (skeptical / curious / time-pressed)

The three reviewers are NOT identical — they vary on bias profile to expose different failure modes:
- **Reviewer 1: Skeptical-experienced** ("I've seen this pitch before")
- **Reviewer 2: Curious-novice** ("First time evaluating this category")
- **Reviewer 3: Time-pressed-decisive** ("I'll skim, then decide in 60 seconds")

### Step 3 — The blind read

Each simulated reviewer reads the artifact cold (no context about workspace authorship). For each reviewer, score:

| Axis | Question | Pass |
|---|---|---|
| **Source attribution** | "Was this produced by a human operator who knows my world, or by a content tool?" | "Human operator" |
| **Specificity** | "Are the specifics (numbers, names, mechanisms) credible and concrete?" | "Yes" |
| **Voice fit** | "Does this sound like the role/person it claims to be from?" (for ghostwritten) OR "Does this sound like a peer, not a vendor?" (for direct) | "Yes" |
| **Decision-readiness** | "Could I decide my next action from this?" | "Yes" |
| **Conviction induction** | "Did this make me want to take the action it implies?" | "Yes" |

### Step 4 — Pass criteria

**Hard pass:** All 3 reviewers say "Human operator" on the source-attribution question AND ≥4 of 5 axes pass for ≥2 of 3 reviewers.

**Conditional pass:** 2 of 3 reviewers say "Human operator" AND no axis fails for >1 reviewer. Ship with metadata flag `BOT_CONDITIONAL_PASS — monitor next 3 artifacts in this category for trend`.

**Fail:** Any of the following:
- ≥2 reviewers say "content tool" / "AI-generated"
- Any axis fails for ≥2 reviewers
- Reviewer 3 (time-pressed) bounces in <30 seconds

### Step 5 — Iteration on fail

Map the failure to the encoding layer that caused it:

| Failure | Likely encoding gap |
|---|---|
| "Sounds AI-generated" | Voice profile thin OR ghostwriter using template variables in personalization slots |
| "Specifics not credible" | ICP voice-of-customer thin OR case-study profile lacking proof anchors |
| "Voice doesn't fit" | Voice profile drift (run `/voice-drift-detector`) OR wrong voice scope loaded (recursive context error) |
| "Couldn't decide next action" | Offer/CTA structure unclear; re-run relevant offer-tier skill |
| "Didn't induce conviction" | Mechanism specificity weak; named mechanism missing or generic |

Iterate the artifact AND the upstream encoding gap. Re-run the test. Maximum 3 iterations before escalating to operator.

## Why simulated reviewers (not real ones)

For high-volume artifacts (5-25 LinkedIn posts/week per client, 200+ DMs/week, etc.), real-reviewer panels don't scale. Simulated reviewers — generated as personas matched to the intended reader — produce calibration signal at workspace velocity.

For low-volume high-stakes artifacts (retainer proposals, curriculum modules, sales pages), real reviewers should also be used. Simulated test runs first; real-reviewer test runs second.

## The pass-rate watch

Workspace logs every test result. Monthly review:
- **Pass rate <85%:** the encoding has degraded. Audit the profiles + the artifact-to-profile fit.
- **Conditional-pass rate >15%:** the bar is creeping down. Tighten the simulated reviewer bias profiles.
- **One artifact category disproportionately failing:** likely a profile gap specific to that artifact's input requirements. Map and fix.

## Cross-references

- `INVARIANTS.md` N-10 — never skip the Blind Output Test for client/program-bound artifacts
- `ENCODING.md` — the 6 profiles that supply the encoding test
- `agents/agency-director.md` — owns workspace-level pass-rate health
- `agents/fulfillment-head.md` — owns client-bound test enforcement
- `agents/program-head.md` — owns program-bound test enforcement
- `spec/CONTEXT-THRESHOLDS.md` — the upstream gate that prevents most failures

---

*Spec version 1.0.0 — 2026-05-03*
