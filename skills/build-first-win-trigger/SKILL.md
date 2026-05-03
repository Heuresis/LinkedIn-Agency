---
name: build-first-win-trigger
slug: /build-first-win-trigger
agent: program-mentor
department: program
output_format: first-win-trigger-spec
required_profiles:
  program.curriculum_spec: exists
allowed_scopes:
  - program-curriculum
output_path:
  program-curriculum: company.yaml::product_strategy.program_tier.first_win_triggers
---

# /build-first-win-trigger — Define First-Win Triggers per Cohort

> **Purpose.** Define the first-win trigger for a cohort — the measurable, time-bound outcome each student archetype achieves in week 2-4 that proves the program works. Without first-win triggers, students lose conviction by week 4 and cohort retention crashes. Per Phase 3 plan, first-win rate target is ≥70% by week 8.
>
> **Bar.** First-win is the moment the student stops questioning the program and starts evangelizing it. Designed correctly, it's also the proof event that becomes the case study material for cohort-2 sales.

## When to invoke

- During program-tier design (called by `/design-program-offer`)
- Before each cohort launch (recalibrate first-win definitions to current curriculum + case study density)
- After each cohort (review first-win rate; refine triggers if rate <70%)
- When a new student archetype emerges (e.g., "consultant pivoting to agency" wasn't an archetype in cohort 1, becomes one in cohort 3 → new trigger needed)

## Required inputs

1. Curriculum spec from `/build-curriculum` (the modules first-wins derive from)
2. Pre-program intake data for the cohort (segment students into archetypes)
3. Prior cohort first-win achievement data (if applicable — informs trigger refinement)
4. Mentor capacity for verification (each first-win requires mentor sign-off)

## The build sequence

### Step 1 — Cohort segmentation by archetype

Per cohort, identify 3-5 student archetypes. Common archetypes in the LinkedIn agency program:

| Archetype | Definition | Typical cohort % |
|---|---|---|
| Pre-revenue | Solo, no clients yet, exploring agency build | 10-25% |
| Agency-stage | 1-3 retainers, building toward 5-10 | 30-45% |
| Established | 4+ retainers, stable, optimizing for margin or scale | 20-30% |
| Pivoting | Existing agency in different vertical, repositioning to LinkedIn-first | 10-20% |
| Solo expert | Senior practitioner monetizing personal expertise as 1:1 retainers | 5-15% |

Each archetype has different first-win definitions because they're at different stages.

### Step 2 — First-win definition per archetype

Each definition must be:
- **Specific:** measurable artifact OR specific revenue / engagement event
- **Achievable:** in week 2-4 with the student's current resources + program support
- **Meaningful:** hitting it shifts the student's conviction (not a vanity metric)
- **Verifiable:** mentor can confirm it happened (screenshot, recording, peer-witness)

Default first-win definitions per archetype:

| Archetype | First-win definition | Target window | Verification |
|---|---|---|---|
| Pre-revenue | First qualified discovery call booked from program-template DM sequence | Week 2-4 | Calendar booking confirmed + DM thread reviewed |
| Agency-stage | First $5K+ retainer signed using program offer architecture | Week 4-6 | Contract signed + offer doc reviewed |
| Established | First case study documented in program format + first program-tier student inquiry from operator's content | Week 6-8 | Case study + inbound DM/comment reviewed |
| Pivoting | First 1,000 impressions on a single LinkedIn post in new vertical | Week 3-5 | Screenshot + post reviewed |
| Solo expert | First $2K+ advisory engagement closed using program qualification thread | Week 3-5 | Contract signed + DM thread reviewed |

These are defaults — operator + program-mentor can customize per cohort.

### Step 3 — Pre-requisite chain

For each first-win, which curriculum modules must be completed first? The student can't hit "first $5K retainer" first-win without completing the offer-design module + the qualification-thread module + the application-gate module.

```yaml
first_win:
  archetype: agency-stage
  definition: "First $5K+ retainer signed using program offer architecture"
  required_modules:
    hard: [build-icp, design-retainer-offer, build-application]
    soft: [build-content-engine, build-outbound-engine]
  unlocks_when: hard_modules_complete AND week >= 3
```

### Step 4 — Trigger conditions

What specific evidence counts as first-win achievement:
- For revenue events: signed contract + first invoice paid
- For booking events: calendar booking confirmed (not just "scheduled")
- For content events: screenshot + post URL
- For outbound events: DM thread reviewed by mentor

Trigger conditions persist to `program.cohorts.{cohort_slug}.first_win_triggers.{archetype}` so cohort-pulse can verify automatically.

### Step 5 — Failure-recovery design

If a student doesn't hit first-win by week 4-6:

```yaml
recovery_protocol:
  week_4_check: mentor 1:1 to diagnose blocker
  week_6_check: cohort-pulse retention-risk flag if no progress
  week_8_intervention: program-head 1:1; consider archetype reassignment OR cohort-pause
  week_12_outcome: if no first-win by cohort end, refund consideration per guarantee tier
```

Most students who slip past week 6 can recover with intervention. Past week 10 = harder. Past week 12 = refund/guarantee territory.

### Step 6 — Celebration ritual

How the cohort recognizes the win:
- Mentor announces in cohort channel within 24h of confirmation
- Student writes a 2-paragraph "what I did" post for cohort + (with permission) program-tier marketing
- Pod celebrates async; cohort spotlight at next group call
- Win added to cohort case study queue (feeds cohort-2 sales material)

The celebration is community-driving AND case-study-generating. Both matter.

### Step 7 — First-win-rate tracking

Cohort-level metric: % of students hitting first-win by week 4 / week 6 / week 8.

```yaml
cohort_first_win_metrics:
  cohort_slug: {slug}
  by_week_4: % | target ≥40%
  by_week_6: % | target ≥60%
  by_week_8: % | target ≥70%
  by_week_12: % | target ≥85%
  by_archetype:
    pre_revenue: % achievement
    agency_stage: % achievement
    established: % achievement
    pivoting: % achievement
```

Below 70% by week 8 = trigger definition revision OR curriculum gap. Per `/program-cohort-pulse`.

### Step 8 — Output emission

```markdown
# First-Win Triggers — Cohort {Cohort Slug}

## Archetypes (this cohort)

[N students per archetype]

## First-win definitions

| Archetype | Definition | Window | Verification |
|---|---|---|---|
| ... | | | |

## Pre-requisite modules per archetype

[chain]

## Recovery protocol

[from Step 5]

## Celebration ritual

[from Step 6]

## Tracking dashboard

{populated as cohort progresses — surfaced in /program-cohort-pulse}
```

## Verification checklist

1. 3-5 archetypes defined per cohort (covers 95%+ of students)
2. Each archetype has a first-win definition with all 4 properties (specific, achievable, meaningful, verifiable)
3. Pre-requisite modules mapped per archetype
4. Trigger conditions allow automatic detection where possible
5. Recovery protocol staged across week 4 / 6 / 8 / 12
6. Celebration ritual designed (community-driving + case-study-generating)
7. Tracking metrics defined with targets per week milestone
8. **Blind Output Test:** would a $7K-$15K student feel the first-win definition is genuinely meaningful (not gameable)? Pass = ship.

## Common failure modes

| Failure | Likely cause | Fix |
|---|---|---|
| First-win rate by week 8 <50% | Definitions too aggressive OR pre-requisites too long | Calibrate definitions down; shorten pre-requisite chain |
| First-win rate by week 8 >95% | Definitions too soft (gameable) | Tighten definitions; require more substantive evidence |
| Same archetype always lags | Archetype definition mismatch (students placed wrong) | Refine archetype criteria; add intake question to discriminate |
| Students hit first-win but conviction doesn't shift | First-win meaningful in workspace's eyes, not student's | Add "what shifted for you" check; revise definition based on student feedback |
| Celebration rituals not landing | Format mismatch with cohort culture | A/B different formats (audio shoutout vs. written post vs. video) |
| Pre-requisite modules cause week-2 burnout | Module scope too heavy | Audit modules; cut to essentials |

## Cross-skill routing

- **Upstream:** `/build-curriculum` (modules that contribute to first-wins); `/design-program-offer` (program tier definition)
- **Sibling:** `/onboard-program-student` (assigns archetype + first-win definition per student); `/program-cohort-pulse` (tracks first-win progress weekly)
- **Downstream:** `/build-retention-pulse` (first-win rate is core retention metric); cohort-2 launch readiness (≥70% first-win required)

## Cross-references

- `agents/program-mentor.md` (Cycle 2 — TBD)
- `agents/program-head.md`
- `INVARIANTS.md` A-12 (program-bound = client-bound bar)
- `reference/playbooks/hybrid-full-stack.md` — Phase 3 first-win-rate target ≥70%
- `skills/build-curriculum/SKILL.md`, `skills/design-program-offer/SKILL.md`
- `skills/onboard-program-student/SKILL.md`, `skills/program-cohort-pulse/SKILL.md`
- `skills/build-retention-pulse/SKILL.md`

---

*Skill version 1.0.0 — 2026-05-03*
