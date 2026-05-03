# The Bar — The Blind Output Test

> **The publish-bar gate.** Every client-bound or program-bound artifact this workspace produces runs through this test before it ships. The test is the source of the workspace's "outperforms a paid agency" claim — and the source's testable, one artifact at a time.

---

## Why this gate exists

The workspace makes a strong claim: every artifact it produces should outperform what a B2B founder would receive from a $5K-$25K/month LinkedIn agency. That claim is testable. This is the test. Without the gate, the workspace would still produce content that is cleaner than DIY — but indistinguishable from the commodity-agency middle of the market. The whole point of the workspace is to not land there. The gate is what keeps the work above the commodity line, every time, on every artifact, on every client.

---

## The bar in one sentence

**Would a B2B founder pay an agency for this?**

If yes, the artifact ships. If no, the artifact gets iterated — and so does whatever upstream encoding gap caused the failure.

---

## When the test runs

Hard-required for these eight artifact types:

1. LinkedIn post (founder OR client ghostwritten)
2. DM sequence (any class — cold / warm / liked-post / comment-for-magnet)
3. Discovery call prep doc
4. Retainer proposal
5. Monthly client report
6. Lead magnet
7. Sales-page copy (program offer or service offer)
8. Curriculum module

These are the artifacts a real client or real student receives. They carry the brand. They drive revenue or retention. The bar applies.

Not required for:

- Internal artifacts (operator notes, working drafts, profile interview transcripts)
- Routing decisions (agency-director diagnostics, scope audits)
- Lead-list research files (intermediate work product, not yet a deliverable)
- Voice-drift audit reports (a measurement, not a creative artifact)

The split is: anything a client or student reads → through the gate. Anything only the operator reads → exempt.

---

## The 3 simulated reviewer profiles

Each artifact is read by three simulated reviewers, calibrated to the artifact's intended reader. The three reviewers are not identical — they vary on bias profile to expose different failure modes:

**Reviewer 1 — Skeptical-experienced.** "I have seen this pitch before. I have hired three LinkedIn agencies in the last five years and two of them disappointed me. Show me why this one is different." This reviewer is the hardest to convince. They probe specificity. They notice when a mechanism is named but not actually explained. They bounce on generic frameworks dressed up as insight.

**Reviewer 2 — Curious-novice.** "First time evaluating this category. I do not yet know what 'good' looks like in LinkedIn agency work. I will trust the artifact if it explains itself clearly and feels grounded." This reviewer rewards clarity and explanation. They penalize jargon and assumed knowledge. They are the hardest to convince of voice fit because they have no prior reference.

**Reviewer 3 — Time-pressed-decisive.** "I will skim, then decide in 60 seconds whether to read the rest. If I am still unconvinced at 60 seconds, I bounce." This reviewer is the hook test. The first three lines have to land. The decision-readiness has to be obvious. If the artifact buries the lede, this reviewer never reaches it.

The three together cover the realistic spectrum of who actually reads the work. A pitch that lands a Reviewer-1 close but loses Reviewer-3 in the first paragraph is a pitch that wins on paper and loses in the inbox. The gate catches both failure modes.

---

## The 5-axis rubric

Each reviewer scores the artifact on five axes:

| # | Axis | The question | Pass |
|---|---|---|---|
| 1 | **Source attribution** | "Was this produced by a human operator who knows my world, or by a content tool?" | "Human operator" |
| 2 | **Specificity** | "Are the specifics — numbers, names, mechanisms — credible and concrete?" | "Yes" |
| 3 | **Voice fit** | For ghostwritten: "Does this sound like the role/person it claims to be from?" For direct: "Does this sound like a peer, not a vendor?" | "Yes" |
| 4 | **Decision-readiness** | "Could I decide my next action from this?" | "Yes" |
| 5 | **Conviction induction** | "Did this make me want to take the action it implies?" | "Yes" |

Source-attribution is the headline axis — if every reviewer says "content tool / AI-generated," nothing else matters; the artifact is dead on arrival. Specificity and voice-fit are the credibility axes. Decision-readiness and conviction-induction are the action axes. An artifact that passes credibility but fails action is read and forgotten; an artifact that passes action but fails credibility is read and disbelieved. The bar is all five.

---

## Pass criteria

**Hard pass.** All 3 reviewers say "Human operator" on axis 1, AND ≥4 of 5 axes pass for ≥2 of 3 reviewers. The artifact ships clean — no metadata flag, no follow-up.

**Conditional pass.** 2 of 3 reviewers say "Human operator" AND no axis fails for more than 1 reviewer. The artifact ships with a metadata flag: `BOT_CONDITIONAL_PASS — monitor next 3 artifacts in this category for trend`. If the trend continues across the next three, the encoding is leaking and gets audited.

**Fail.** Any one of:
- ≥2 reviewers say "content tool" / "AI-generated" on axis 1
- Any axis fails for ≥2 reviewers
- Reviewer 3 (time-pressed) bounces in <30 seconds

A failed artifact does not ship. It iterates.

---

## What happens on fail

The failure is mapped to its most likely encoding gap, and both the artifact AND the encoding are addressed in the same loop:

| Failure signal | Likely encoding gap | Where to fix |
|---|---|---|
| "Sounds AI-generated" | Voice profile thin OR ghostwriter using template variables in personalization slots | Re-run `/extract-founder-voice` for the relevant scope; fix template defaults in the post type |
| "Specifics not credible" | ICP voice-of-customer thin OR case-study profile lacking proof anchors | Add verbatim language from real sales calls to ICP; populate case-study `proof_assets` with real receipts |
| "Voice doesn't fit" | Voice profile drift OR wrong voice scope loaded (recursive context error) | Run `/voice-drift-detector` for the client; verify the skill loaded the correct subprofile per `docs/RECURSIVE_CONTEXT.md` |
| "Couldn't decide next action" | Offer/CTA structure unclear; mechanism for the next step missing | Re-run the relevant offer-tier skill (`/design-retainer-offer`, `/design-program-offer`); tighten the conversion bridge in the content engine |
| "Didn't induce conviction" | Mechanism specificity weak; named mechanism missing or generic | Encode the mechanism into the voice profile and content engine; require named mechanism on every pillar |

Iterate the artifact AND the upstream encoding gap. Re-run the test. **Maximum 3 iterations** before escalating to the operator. If three iterations cannot produce a pass, the encoding is wrong at a deeper level than this artifact can fix — the operator owns the next move.

The tight loop here is the heart of the workspace. Every failure surfaces a gap. Every gap fix sharpens the encoding. Every cycle the encoding sharpens, the next 100 artifacts of this type pass on first attempt. The compounding starts at the gate.

---

## Why simulated reviewers vs. real ones

A real-reviewer panel — three actual founders, paid for their time, reading every artifact — would be the gold standard. It does not scale. The workspace produces:

- 5-25 LinkedIn posts per week per client
- 200+ DMs per week per client
- 1 monthly report per client
- 1 retainer proposal per qualified prospect
- 1 curriculum module per program build cycle

A 10-client agency with one program cohort produces 50-250 posts per week, 2,000+ DMs per week, 10 monthly reports per month, plus proposals on every applied prospect. Real reviewers cannot keep up. The bottleneck shifts from production to review, and the workspace's velocity advantage collapses.

Simulated reviewers — generated as personas matched to the intended reader, scored against the 5-axis rubric — produce calibration signal at workspace velocity. They are not as accurate as real reviewers on any single artifact. They are accurate enough that, applied across thousands of artifacts, the pass-rate trend reliably tracks the actual quality trend.

For low-volume high-stakes artifacts (retainer proposals, curriculum modules, sales-page copy), real reviewers should also be used. The simulated test runs first as a screen. The real-reviewer test runs second as a confirmation. Most of the failure modes get caught at the simulated stage; the real reviewers spend their time on the close calls.

---

## The pass-rate watch

Every test result is logged. The workspace runs a monthly review:

- **Pass rate <85%.** The encoding has degraded. Audit the profiles + the artifact-to-profile fit. Likely culprit: a client subprofile or a content engine that has not been refreshed against the most recent quarter's market signal.
- **Conditional-pass rate >15%.** The bar is creeping down. The simulated reviewer bias profiles need tightening — they are passing artifacts they should be flagging. Review reviewer prompts and pull samples for operator review.
- **One artifact category disproportionately failing.** Likely a profile gap specific to that artifact's input requirements. Map the category back to its required profiles, find the weak one, fix it.

Three months of pass rate ≥85% with ≤10% conditional means the encoding is healthy and the gate is calibrated. Anything below that triggers an encoding audit.

---

## Worked example: a ghostwritten client post enters the gate

The skill `/ghostwrite-client-post` produces a post for client `acme`. The voice-drift cosine check passes (similarity 0.87, threshold 0.82). The post enters the Blind Output Test gate.

**Reviewers generated.** Three personas matched to Acme's audience — VP-of-Sales at a mid-market B2B SaaS firm. Reviewer 1: skeptical, has bought from three SaaS-services vendors before. Reviewer 2: curious, first time evaluating SaaS-content services. Reviewer 3: time-pressed, will skim and decide in 60 seconds.

**Round 1 reads.**

| Axis | R1 Skeptical | R2 Novice | R3 Time-pressed |
|---|---|---|---|
| Source attribution | Human operator | Human operator | Human operator |
| Specificity | Pass | Pass | Pass |
| Voice fit | **Fail** | Pass | Pass |
| Decision-readiness | Pass | Pass | Pass |
| Conviction induction | Pass | Pass | Pass |

R1 says: "The mechanism described in this post does not sound like Acme's founder. Acme always frames decisions through unit-economics; this post frames the decision through brand-marketing. Drift."

**Result: Fail.** Voice-fit axis flagged by R1 — and the failure pattern matches the "decision-frame consistency" axis from `/voice-drift-detector`. The artifact halts.

**Iteration loop.**

1. The artifact gets routed back through `/voice-drift-detector` with focus on decision-frame axis. The detector finds: across the last 5 ghostwritten posts, decision-frame violations have crept in twice. Pattern matches "ghostwriter using template defaults" root cause from the drift-detector remediation table.
2. The ghostwriter is recalibrated against Acme's last 10 reference posts (which all frame through unit-economics). The post is rewritten with a unit-economics frame.
3. The rewritten post re-enters the BOT gate. Round 2: all three reviewers pass on all five axes. Hard pass.
4. The post ships to client review queue. The encoding gap (decision-frame slipping under template defaults) is logged for the next quarterly profile-refresh review.

The artifact passed. The encoding got sharper. Both happened in the same loop. That is the gate working as designed.

---

## Common attempts to bypass the gate + why they fail

| Attempt | Why it gets tried | Why it fails |
|---|---|---|
| "Skip the BOT for this one — the client needs the post today" | Time pressure; operator wants to ship | The artifact ships generic. Client engagement on that post drops. Three weeks of skipped BOTs and the client says "this doesn't sound like me" → churn. The time saved costs months of revenue. |
| "Lower the simulated reviewer bias profiles so more passes" | Pass rate looks better on the dashboard | The bar is the bar because of what it screens out. Lowering the reviewers lets through exactly the artifacts that make the workspace indistinguishable from a commodity agency. The dashboard improves; the moat collapses. |
| "Run BOT but ship even on conditional fail" | Operator has confidence in this specific artifact | Conditional fail is fine to ship with the metadata flag and trend monitoring. Hard fail is not. If the operator overrides on hard fail, the override is logged and the artifact joins the quarterly review queue — and three repeated overrides on the same axis triggers an encoding audit anyway. |
| "Use only one reviewer instead of three" | Faster, cheaper | Single-reviewer signal is high-variance. The three-reviewer panel is calibrated against the false-positive vs. false-negative cost asymmetry. Dropping to one introduces a steady stream of escapes — the kind that shows up six months later as client churn the operator can't trace. |
| "Generate the simulated reviewers from a generic LLM persona prompt instead of one matched to the intended reader" | Easier to write | The reviewers are valuable because they match the actual audience. A generic reviewer passes generic content. The whole gate exists to screen against generic content. Bypass = inversion of the gate. |
| "Treat the BOT as a post-publish audit instead of a pre-publish gate" | Feels less like friction | A failed artifact in the wild has already done its damage. The gate exists because the cost of catching the failure pre-publish is minutes of rework; the cost of catching it post-publish is client trust + brand drift + the lost cycle of the post slot. |

---

## Cross-references

- `spec/BLIND-OUTPUT-TEST.md` — the canonical spec for the test protocol
- `INVARIANTS.md` N-10 — never skip the BOT for client-bound or program-bound artifacts
- `INVARIANTS.md` A-4 — always run the BOT for client-bound and program-bound artifacts
- `ENCODING.md` — the 6 profiles whose quality drives the BOT pass rate
- `spec/CONTEXT-THRESHOLDS.md` — the upstream gate that prevents most BOT failures by ensuring profiles are above the floor before generation
- `docs/RECURSIVE_CONTEXT.md` — the scope discipline that prevents the most common voice-fit failures
- `docs/SKILL_AUTHORING.md` — every new skill ships with the BOT integrated into its validation gate (section 6)
- `skills/voice-drift-detector/SKILL.md` — the sibling check that catches drift before it becomes a BOT failure
- `agents/agency-director.md` — owns workspace-level pass-rate health
- `agents/fulfillment-head.md` — owns client-bound BOT enforcement
- `agents/program-head.md` — owns program-bound BOT enforcement

---

*Doc version 1.0.0 — 2026-05-03*
