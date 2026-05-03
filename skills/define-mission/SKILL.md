---
name: define-mission
slug: /define-mission
agent: foundations-head
department: foundations
output_format: mission-statement
required_profiles:
  business_context.basic_info: 50
allowed_scopes:
  - agency-mission
output_path:
  agency-mission: company.yaml::business_context.mission
---

# /define-mission — Agency Mission Statement

> **Purpose.** Build the agency's mission statement — the **durable why** that survives pivots, sets the program tier's pedagogy, filters which clients to take vs. decline, and gives the team a north-star that doesn't move when next quarter's revenue target moves. Different from positioning: positioning is *market-facing* (for whom, against whom, with what mechanism); mission is *operator-facing + team-facing* (why this work exists for the people doing it).
>
> **Bar.** A mission statement passes when the operator can read it 12 months after writing it and still say "yes, that's why I'm doing this" without flinching, and when the agency's first hire can read it on day 1 and use it to make a judgment call without consulting the operator. If it's a wall poster nobody references, it failed.

## When to invoke

- Cycle 1 onboarding — the first foundations skill an operator runs (lower threshold than `/build-positioning` because mission is a generative input to ICP/voice/offer, not a derivative of them)
- After a period of "yes-to-everything" intake — operator has taken on 3+ misfit clients, can't articulate why each was a mistake; mission gap is the diagnostic
- Pre-program launch — the program tier (`product_strategy.program_tier`) needs a pedagogy backbone, not just a curriculum; mission supplies it
- First key hire — operator about to bring on first ghostwriter, account manager, or operator-of-the-operator; the hire needs a why-filter to make judgment calls inside the agency's identity
- Operator-fatigue signal — operator says some variant of "I don't remember why I started this" or "I'm thinking about shutting it down"; mission re-articulation is the intervention before pivot
- 12-month integrity check — every annual review per `agents/foundations-head.md` decision authority

## Required inputs

1. **`business_context.basic_info` ≥ 50%** — agency name, operator name (kept private per `ENCODING.md` Profile 1), business model, years active, current revenue tier. The basic context anchors the interview to the actual operator, not a generic founder
2. **Operator's origin narrative.** Either an existing journal/About-page draft, or operator available for the 5-why interview live (Step 1)
3. **List of the operator's last 3 client decisions** — 2 they took, 1 they declined. Per-client: who, why-took or why-declined, how-it-played-out. Powers Step 2 (negative-mission test)
4. **Optional:** prior mission statement attempts (drafts, About-page copy, founder-letter drafts). Useful to detect what the operator has been *trying* to say but couldn't compress
5. **60-90 minutes operator time** for the full interview chain (Steps 1-5 require their voice; this is not a delegate-able skill)

Mission is an unusually *low-threshold* skill on context (only basic_info ≥50%) because it generates downstream context — running it first sharpens every subsequent ICP, voice, and positioning interview. Operators who skip mission and start at ICP build a competent agency that drifts into incoherence by month 18.

## The build sequence

### Step 1 — The 5-why interview (20 min)

Mission lives 5 layers below the surface answer. The operator's first answer is always too clean. Drill.

Run this question ladder, never skip a level. Each answer becomes the next question's anchor.

| Layer | Question | Expected surface answer | What you actually mine |
|---|---|---|---|
| **1** | "Why did you start this agency?" | "I saw an opportunity in B2B LinkedIn marketing." | The market story — discard, drill |
| **2** | "Why that opportunity, not one of the 50 other opportunities you could've gone after?" | "I was already doing it for myself / I had a result I could replicate." | The capability story — record, drill |
| **3** | "Why does it matter to you that B2B founders get this specifically? You could've sold any service to any audience." | "Because I watched {a person / a moment / a pattern} that I can't unsee." | The conviction story — record, this is the seed |
| **4** | "If you ran this agency for 10 years and never made another dollar above what you make today, would you still run it? Why?" | "Because {the work itself / the people I get to work with / the world it builds}." | The identity-vs-economics signal — record |
| **5** | "If a competitor offered you $5M tomorrow to shut down the agency and never operate in this space again, what's the one thing you'd lose that the $5M doesn't replace?" | "{The thing only this work gives them.}" | The non-negotiable core — this is the mission seed |

Layer 5 answer is almost always the mission. Layers 1-4 are the scaffolding that gets the operator to Layer 5 without flinching into platitudes.

If Layer 5 produces a clean answer in <30 seconds, the operator is either elite-clear (rare in Cycle 1) or platitude-fluent (common). Probe: "Say that as if you're explaining it to someone who's about to quit your team and is asking why they should stay one more month." Real answers come out under that pressure.

Capture verbatim. Do not paraphrase. Voice-of-operator is to mission what voice-of-customer is to ICP.

### Step 2 — Negative-mission test (15 min)

Mission is defined as much by what the agency *won't* do as by what it will. The negative test surfaces the mission's spine by asking: what kind of work would you decline even if it were profitable, easy, and on-brand?

Pull the operator's last 3 client decisions (1 declined + 2 taken from `Required inputs` 3). For each, run the **decline-vector analysis**:

| Decision | Declined / Taken | Reason given at the time | Mission-aligned reason (drilled) | Pattern |
|---|---|---|---|---|
| Client A — declined | declined | "Not a great fit." | "They wanted volume DM, no content. We don't operate without a content layer because content is what makes the DM convert. Volume-only would damage their brand and ours." | Won't take volume-only DM contracts |
| Client B — taken | taken | "Looked promising." | "They had no LinkedIn presence but the founder was deeply opinionated and willing to be on camera. We bet on the conviction." | Will take low-baseline-but-high-conviction founders |
| Client C — taken | taken | "Revenue was good." | (operator pauses) "Honestly? The revenue was good and we needed it. But they're 4 months in and I keep thinking about firing them." | (Misalignment surfaced) Took for revenue, not for mission. This is the gap. |

Patterns from the decline-vector analysis become the **decline list** — the explicit kinds of work the agency declines on principle. Three categories minimum:

1. **Avatar-misfit declines** — the kind of operator/founder the agency won't serve regardless of money
2. **Scope-misfit declines** — the kind of engagement (volume-only, project-based, equity-only, white-label, etc.) the agency won't structure
3. **Method-misfit declines** — the kind of method/tactic the agency refuses to run even if the client demands it (e.g., automated DM volume that violates platform terms, AI-only content with no operator review, fake engagement)

If the operator can't name 3 declines, mission is too soft to do its filter job. Re-run Step 1 to surface what they actually believe.

### Step 3 — Identity statement (10 min)

Who does the operator *become* through this work? Mission is identity-formation, not just service-provision.

Question ladder:

1. "If your kid (or your closest friend) described what you do for a living to a stranger in 2 sentences, what would they say — and what would you wish they said instead?"
2. "What's the version of you that this work is building toward? Not the version that has more revenue — the version that is *more of who you already are*."
3. "Name 1-2 people (alive or dead, public or private) whose work or way of operating you'd want yours compared to in 10 years."

Output the **identity statement** — a 1-2 sentence articulation of who the operator becomes through this work.

Template (one form among many):
> "I am a {operator-archetype, in their own words} who {does the specific work} for {the avatar} so that {the world the work creates}."

Examples (anonymized identity statements):
- *"I am the operator who refuses to ship a post that doesn't sound like the founder, even when it costs us margin, because every voice we lose is a category we lost."*
- *"I am the agency that teaches its students how to fire us, because the goal was never recurring revenue — it was operator independence."*

The identity statement seeds the **core values** field. Do not fill core values from a generic list. Derive from the identity statement.

### Step 4 — Client-impact framing (10 min)

The mission must also point outward — what world does this agency build for its clients? Pure inward identity is operator-narcissism; pure outward impact is corporate platitude. The mission holds both.

Run the **impact ladder**:

1. **First-order impact** — what literally changes for the client during the engagement? (e.g., "Their pipeline doubles. Their content sounds like them. Their DMs convert.")
2. **Second-order impact** — what becomes possible for them because of the first-order change? (e.g., "They hire instead of grinding. They take a real vacation. They stop being the bottleneck on their own growth.")
3. **Third-order impact** — what kind of operator/business/world emerges from the second-order change being normal? (e.g., "Founder-led B2B becomes economically viable at scale. The category resists collapse into commodity agencies and AI slop.")

The third-order is the mission's outward face. It's not "we help founders win on LinkedIn" — that's first-order. It's the systemic change the agency contributes to by doing first-order work well.

If third-order rings hollow ("we help create a better B2B world"), mission is still operating at the slogan layer. Re-run Step 1 with sharper Layer 5 probes.

### Step 5 — 10-year integrity test (10 min)

Mission must be **time-stable**. The integrity test asks: in 10 years, will this still be the mission, or is it the artifact of a current market condition?

Run 5 stress questions:

1. **The platform-collapse test:** If LinkedIn shut down tomorrow and the agency had to pivot to a different platform, would this mission still be true? (If mission depends on LinkedIn specifically, it's tactic, not mission. Rewrite at the layer of *what LinkedIn enables for the avatar*, not *LinkedIn itself*.)
2. **The AI-replaces-90%-of-the-work test:** If AI tomorrow replaced 90% of ghostwriting and DM operations, would this mission still hold? (If mission depends on the labor itself, it's job description, not mission. Rewrite at the layer of *what the labor produces*.)
3. **The exit-event test:** If the operator exited at $20M tomorrow, would this mission still pull them back into the work in some form? (If no, mission is current-stage motivation, not durable purpose.)
4. **The successor test:** Could the operator's eventual successor — a CEO they hire in year 7 — read this mission and use it to make decisions the operator would agree with, without ever speaking to the operator? (If no, mission is operator-personal, not agency-portable.)
5. **The decline-test:** Read the decline list from Step 2. In 10 years, would those declines still hold? (If a decline only holds because of current market conditions, it's a tactical preference, not a mission boundary.)

If 2+ tests fail, the mission is not time-stable. Re-run the failed dimension's source step.

### Step 6 — Output the mission artifact

Hydrate `business_context.positioning.mission_statement` plus the supporting structures:

```yaml
mission:
  mission_statement: ""                    # 1 sentence — the durable why (15-35 words)
  identity_statement: ""                   # 1-2 sentences — who the operator becomes (Step 3)
  supporting_beliefs: []                   # 3 — the load-bearing convictions the mission rests on (Step 1 + Step 4)
  core_values: []                          # 4-6 — derived from identity statement (Step 3), not generic
  decline_list:
    avatar_misfit: []                      # who the agency won't serve (Step 2)
    scope_misfit: []                       # what kinds of engagements (Step 2)
    method_misfit: []                      # what kinds of methods/tactics (Step 2)
  client_impact:
    first_order: ""                        # Step 4
    second_order: ""                       # Step 4
    third_order: ""                        # Step 4 — the systemic change
  integrity_test_results:
    platform_collapse: pass | fail
    ai_replaces_labor: pass | fail
    exit_event: pass | fail
    successor: pass | fail
    decline_test: pass | fail
```

Note: `mission_statement` writes to `business_context.positioning.mission_statement` per `ENCODING.md` Profile 1's existing field; the supporting structures (`decline_list`, `client_impact`, `integrity_test_results`) write to `business_context.mission` as an extension.

## Output format

```yaml
---
artifact_type: mission-statement
scope: agency-mission
profile_completeness:
  business_context.basic_info: 0-100
build_date: YYYY-MM-DD
integrity_test_passes: 0-5
profiles_used: [business_context.basic_info]
frameworks_used: [5-why-mission, negative-mission-test, impact-ladder, 10-year-integrity-test]
confidence: HIGH | MEDIUM | LOW
gap_flags: [list]
---

## Mission YAML (writes to business_context.mission + business_context.positioning.mission_statement)
{the populated YAML from Step 6}

## Mission narrative (1-page brief, operator-voice)
{the why-this-work-exists narrative — Layer 5 answer expanded into 250-400 words; reads like an internal founder letter, not a marketing page; written in operator voice using verbatim phrases from Step 1}

## The 3 supporting beliefs
1. {Belief — the load-bearing conviction the mission rests on, with a 1-sentence "this is true because" anchor}
2. {Belief}
3. {Belief}

## The decline list (operator-facing)
**Avatar-misfit declines:** {list}
**Scope-misfit declines:** {list}
**Method-misfit declines:** {list}

For each item: "Why we decline this — even when profitable" sub-rationale.

## Client-impact framing (3-tier)
**First-order:** {what literally changes for the client}
**Second-order:** {what becomes possible because of the first-order change}
**Third-order:** {systemic change the agency contributes to}

## Integrity test results
{table of 5 tests, pass/fail with rationale per test}

## Operator's verbatim Layer-5 answer (preserved for re-anchor)
"{the verbatim answer from Step 1, Layer 5 — preserved as the seed}"

## Next actions
{usually: /build-positioning (mission supplies the operator-facing why; positioning supplies the market-facing what — they must not contradict), /build-icp if avatar is still soft, /design-retainer-offer with decline-list as scope guardrails}
```

## Verification checklist

1. **Layer 5 verbatim captured.** Mission narrative includes the operator's actual words from Step 1 Layer 5, not a paraphrase. If paraphrased, fail — the seed is gone.
2. **Mission statement is 15-35 words.** Shorter = slogan. Longer = paragraph posing as sentence.
3. **Decline list has ≥3 items per category** (avatar-misfit, scope-misfit, method-misfit). Below 3 = mission too soft to filter.
4. **Identity statement names what the operator becomes**, not just what they do. "I help" or "we provide" = service description, not identity. Fail.
5. **Supporting beliefs are 3, distinct, and load-bearing** — each one must be a conviction without which the mission collapses. Generic beliefs ("we believe in quality") = fail.
6. **Client-impact has all 3 orders filled distinctly** — first ≠ second ≠ third. If first and third sound similar, mission is still at slogan layer.
7. **10-year integrity test ≥ 4 of 5 pass.** Below 4 = re-run failed dimension.
8. **Mission does not contradict positioning** (if positioning exists). Run cross-check: positioning's avatar must be a *subset* of who the mission serves, not a divergence. If they diverge, foundations-head decision: which is wrong, fix it. Per `agents/foundations-head.md` decision authority.
9. **Voice-of-operator preserved.** Mission narrative uses operator's actual cadence, signature phrases. Generic founder-letter voice = re-run with verbatim transcripts.
10. **Blind Output Test run** per `spec/BLIND-OUTPUT-TEST.md` — would the operator's first hire read this mission on day 1 and make 1 judgment call from it without asking the operator? Required because mission ships internally to team artifacts (employee handbook, About page, founder letters) and externally on the website.

## Common failure modes

| Failure | Fix |
|---|---|
| Mission statement is a slogan ("empowering B2B founders to win on LinkedIn") | Re-run Step 1 — operator stopped at Layer 2 or 3. Drill to Layer 5. Use the $5M-shutdown probe. |
| Operator says "I don't really have a strong why, I'm just running a business" | Skip Step 1 question 1. Start at Layer 4 (would you do this for 10 years without making more money). If still flat, mission may be premature — operator is in revenue-anxiety mode. Run `/diagnose-bottleneck` first, then return when revenue is stable. |
| Decline list contains items the operator won't actually decline ("we don't take clients with bad attitudes") | Pull last 3 client decisions and stress-test each decline. If a decline is aspirational not actual, remove it — mission must reflect actual filtering, not desired filtering. |
| Identity statement reads like a job description ("I am a strategic LinkedIn consultant who helps founders") | Re-run Step 3 — identity ≠ role. Identity is who you become through this. Use the kid-describing-you probe. |
| Third-order client impact rings hollow ("we contribute to a better B2B ecosystem") | Re-run Step 4 with concrete second-order changes. Third-order must be downstream of *specific* second-order changes, not generic. |
| Mission contradicts positioning (mission serves operators-of-all-sizes, positioning targets $1M-$10M founders) | Foundations-head decision per `agents/foundations-head.md`: positioning constrains mission OR mission is broader than current commercial focus and positioning is the wedge. Document the relationship explicitly. |
| Operator wants to ship mission as the website's About page verbatim | Mission is operator-facing + team-facing. Website About page is market-facing — derived from mission, not equal to it. Run `/build-positioning` to produce the market-facing version; keep mission as internal source of truth. |

## Cross-skill routing

- **Feeds into:** `/build-positioning` (mission's avatar boundary feeds positioning's avatar lock; mission's decline list feeds positioning's anti-position frame), `/design-retainer-offer` + `/design-program-offer` (decline list becomes scope guardrails per `INVARIANTS.md` A-1 chain), `/build-application` (decline list becomes the application's hard-DQ logic), `/onboard-program-student` and `/onboard-client` (mission narrative shows up in welcome sequences and program pedagogy)
- **Consumes:** `business_context.basic_info` only — mission is generative, not derivative. Operators who skip mission and start at `/build-icp` build coherent tactics on top of an absent why
- **Triggers re-run when:** operator-fatigue signal surfaces, 12-month integrity check, vertical pivot, first key hire, mission-positioning contradiction detected by `/strategic-advisory`
- **Owned by:** `agents/foundations-head.md` per skills_owned declaration

## Cross-references

- `agents/foundations-head.md` — owning agent + decision authority on mission-positioning conflicts
- `INVARIANTS.md` A-5 (dependency chain — mission seeds the chain), A-1 (audit before producing), A-11 (anonymized descriptors throughout the artifact)
- `ENCODING.md` Profile 1 (`business_context.basic_info`, `business_context.positioning.mission_statement`, `business_context.positioning.core_values`)
- `spec/CONTEXT-THRESHOLDS.md` — `/define-mission` row (business_context.basic_info ≥50)
- `spec/BLIND-OUTPUT-TEST.md` — required pre-ship test (mission ships to website + team artifacts)
- `spec/BANNED-VOCABULARY.md` — phrase filter for mission narrative (no "empower," "transform," "unlock potential" without specific anchor)
- `skills/build-positioning/SKILL.md` — market-facing complement; mission and positioning must align
- `skills/build-icp/SKILL.md` — mission's avatar boundary feeds ICP narrowing
- `skills/design-retainer-offer/SKILL.md` — decline list becomes scope guardrails
- `skills/build-application/SKILL.md` — decline list becomes hard-DQ logic
- `reference/frameworks/foundations/5-why-mission.md` — (Cycle 2 — TBD) full interview rationale per layer
- `reference/frameworks/foundations/negative-mission-test.md` — (Cycle 2 — TBD) decline-vector analysis protocol
- `reference/frameworks/foundations/10-year-integrity-test.md` — (Cycle 2 — TBD) 5-test rubric

## Examples

See `examples/`:
- `example-mission-hybrid-agency.md` — (Cycle 2 — TBD) mission for a hybrid DFY+program agency where the operator's why is "operator independence for B2B founders"
- `example-mission-program-only.md` — (Cycle 2 — TBD) mission for an agency pivoting toward education where the why is "preserve founder voice in an AI-slop era"
- `example-mission-vertical-saas.md` — (Cycle 2 — TBD) mission for a vertical-locked agency where the why is "make the DevOps category survive marketing's commodity collapse"

---

*Skill version 1.0.0 — 2026-05-03*
