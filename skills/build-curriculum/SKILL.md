---
name: build-curriculum
slug: /build-curriculum
agent: program-mentor
department: program
output_format: curriculum-module
required_profiles:
  product_strategy.program_tier: 70
  case_studies.agency_case_studies: 70
allowed_scopes:
  - program-curriculum
output_path:
  program-curriculum: company.yaml::product_strategy.program_tier.curriculum
---

# /build-curriculum — Build a Single Curriculum Module

> **Purpose.** Build one curriculum module for the program tier. Modules derive from REAL case-study journeys (not theoretical frameworks). Each module has a learning objective, prerequisite modules, the case-study spine that grounds it, the framework it formalizes, the student exercise, the first-win contribution.
>
> **Bar.** A student completing this module can apply it to their own agency in week 1 and produce a measurable artifact. If the module is "interesting but not actionable", the program reverts to information-product economics — and information-product margins don't justify $7K-$15K pricing.

## When to invoke

- During program design phase (curriculum architecture from `/design-program-offer`)
- When a new case study validates a pattern worth teaching (curriculum refresh)
- Before each cohort launch (curriculum review against latest case studies)
- Manually when a cohort surfaces "this module isn't landing" feedback

## Required inputs

1. Module scope declared (which transformation does this module teach)
2. `product_strategy.program_tier` ≥70% complete (the program offer this curriculum implements)
3. `case_studies.agency_case_studies` ≥70% (≥3 validated case studies — modules derive from these, not theory)
4. Pre-requisite module list (the modules that must be completed before this one)
5. Mentor capacity (modules that require heavy 1:1 support need mentor bandwidth check)

## The build sequence

### Step 1 — Module scope

What specific transformation does this module teach? Examples:
- "Voice extraction that produces ghostwriter-ready profile in 90 minutes"
- "Build a 4-class DM intent taxonomy for your agency's outbound"
- "Design a retainer offer that defends $15K+/month price"
- "Run weekly voice-drift audits that prevent month-4 churn"

The scope statement is the module's title + 1-paragraph promise.

### Step 2 — Case-study spine selection

Which 1-2 client journeys from `case_studies.agency_case_studies` most clearly demonstrate this module's outcome? Selection criteria:
- **Validated:** operator + client both signed off (per `INVARIANTS.md` N-3 — no invented case studies)
- **Vertically diverse from cohort's avatar majority** (so the case study isn't dismissable as "that's only for X vertical")
- **Time-tested:** outcome held for ≥6 months (not a one-week spike)
- **Sequence-traceable:** the journey can be reconstructed step-by-step (not "they hired us and good things happened")

The case study spine becomes the module's narrative backbone. Every framework taught is anchored to "this is what {anonymized case study} did at {step}."

### Step 3 — Framework formalization

The specific framework the case study spine implies. Must be:
- **Teachable in 60-90 minutes** (the module's main session length)
- **Reducible to a YAML or markdown template** the student can fill in
- **Composable with prior modules** (each module's framework slots into a larger architecture)

Framework formalization output:
```yaml
module_framework:
  name: {framework_name}
  inputs: [list of required inputs]
  steps: [4-7 steps]
  outputs: [the artifact the student produces]
  decision_points: [where the student makes a judgment call vs. follows a template]
```

### Step 4 — Pre-requisite mapping

Which prior modules MUST be complete before this one? Modules build on each other — students can't run "design-retainer-offer" before "build-icp."

```yaml
prerequisites:
  hard: [module_slugs that MUST be complete]
  soft: [module_slugs recommended but not required]
unlocks: [module_slugs that this one enables]
```

### Step 5 — Student exercise design

The action the student takes in their own agency to apply this module:
- Completable in 1 week (not a 3-week project)
- Produces a measurable artifact (a YAML, a doc, a launched campaign — something inspectable)
- Mentor-verifiable (mentor can review the artifact in <15 min)

Exercise spec:
```yaml
student_exercise:
  brief: {1-paragraph what the student does}
  artifact: {what they produce}
  evidence: {how mentor verifies}
  estimated_time: {hours}
  common_blockers: [list]
  success_criteria: {threshold for "complete"}
```

### Step 6 — First-win contribution

Does this module produce a first-win-eligible artifact? If yes:
- Cross-reference `/build-first-win-trigger` for the relevant student archetype
- Note that completing this module can trigger first-win confirmation

```yaml
first_win_contribution:
  triggers_first_win_for_archetypes: [pre-revenue | agency-stage | established | pivoting]
  evidence_required: {specific artifact + threshold}
  mentor_verification_protocol: {how mentor confirms}
```

### Step 7 — Mentor support spec

What mentor support to expect:
- Common 1:1 questions for this module (top 5)
- Async-support patterns (most-asked questions in pod channel)
- Common student mistakes (and how mentor catches them in week-1 review)
- Escalation triggers (when mentor brings to program-head)

### Step 8 — Output emission

```markdown
# Module: {Module Title}

**Scope:** {transformation taught}
**Position:** Module {N} of {N total} | Prerequisites: {modules}
**Estimated student time:** {hours/week × weeks}
**First-win contribution:** {if applicable}

## Promise

{1-paragraph what the student walks away with}

## Case study spine

This module is taught through the journey of {anonymized case study}:
- Stage 1: {what they did}
- Stage 2: {what they did}
- Outcome: {the specific transformation, with metrics}

## The framework

[from Step 3]

## Your exercise this week

[from Step 5]

## What you'll produce

{the artifact spec}

## How mentor will verify

{the verification protocol}

## Common blockers and mentor support

[from Step 7]

## Composition with prior modules

This module's framework slots into the larger architecture as: [explanation].
```

Modules persist to `product_strategy.program_tier.curriculum.{module_slug}`.

## Verification checklist

1. Module scope is a specific transformation, not a topic
2. Case-study spine pulls from validated case studies (operator + client sign-off per N-3)
3. Framework is teachable in 60-90 min and reducible to a template
4. Prerequisites mapped (hard + soft)
5. Student exercise completable in 1 week with measurable artifact
6. First-win contribution explicit (or noted as not-applicable)
7. Mentor support spec includes top 5 expected questions + common mistakes
8. **Blind Output Test:** would a $7K-$15K student feel the module justifies cohort price relative to their week's spend? (Specific framework + real case study + actionable exercise = yes; theoretical lecture = no.)

## Common failure modes

| Failure | Likely cause | Fix |
|---|---|---|
| Module is "interesting" but not "actionable" | Framework too abstract; no exercise | Force exercise design before module ships |
| Case-study spine is invented or composite | Tempted to construct an idealized journey | Use only validated case studies per N-3; if no validated case study exists for this module's outcome, the module isn't ready to ship |
| Student exercise takes 3+ weeks | Scope too broad | Cut exercise to a single week's artifact |
| First-win contribution unclear | Module disconnected from first-win architecture | Cross-reference `/build-first-win-trigger` during design |
| Mentor flooded with same question repeatedly | Module's framework has a gap students all hit | Add mini-section addressing the gap; surface to next curriculum refresh |
| Cohort 2 case study spine still references cohort 0 case study | Curriculum not refreshed with newer case studies | Quarterly case study refresh per `reference/playbooks/hybrid-full-stack.md` Phase 3 |

## Cross-skill routing

- **Upstream:** `/design-program-offer` (curriculum architecture); `case_studies.agency_case_studies` (the spine source)
- **Sibling:** `/build-first-win-trigger` (first-win contribution coordination); `/onboard-program-student` (consumes curriculum spec for week-1 plan)
- **Downstream:** `/program-cohort-pulse` (per-module landing data — which modules confuse students)
- **On case study refresh:** Quarterly review of all modules — replace stale spines with newer case studies

## Cross-references

- `agents/program-mentor.md` (Cycle 2 — TBD) — owning persona
- `agents/program-head.md` — curriculum quality owner
- `INVARIANTS.md` N-3 (no invented case studies), A-12 (program-bound = client-bound bar)
- `ENCODING.md` — `case_studies.agency_case_studies` schema
- `spec/BLIND-OUTPUT-TEST.md` — module passes BOT before cohort use
- `skills/design-program-offer/SKILL.md`, `skills/onboard-program-student/SKILL.md`
- `skills/build-first-win-trigger/SKILL.md`, `skills/program-cohort-pulse/SKILL.md`
- `reference/playbooks/hybrid-full-stack.md` — Phase 3 curriculum refresh cadence

---

*Skill version 1.0.0 — 2026-05-03*
