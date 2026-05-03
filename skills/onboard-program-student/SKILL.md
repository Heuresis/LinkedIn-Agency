---
name: onboard-program-student
slug: /onboard-program-student
agent: program-mentor
department: program
output_format: student-onboarding-package
required_profiles:
  student.enrollment_confirmed: true
  student.slug: declared
  program.curriculum_spec: exists
allowed_scopes:
  - single-student
output_path:
  single-student: outputs/{date}/program-onboarding/{student_slug}/onboarding-package.md
---

# /onboard-program-student — Cohort Onboarding for High-Ticket Program Students

> **Purpose.** Onboard a single program student into a cohort. Capture pre-program baseline, assign cohort-pod, define week-1 first-win plan, set mentor cadence, install community ritual onboarding. By week 2, every student has a measurable first-win path OR is flagged as engagement-risk.
>
> **Bar.** Students who hit a first-win in week 2-4 finish the cohort and refer others. Students who don't tend to disengage by week 5 and request refunds by week 8. Onboarding sets the trajectory.

## When to invoke

- After enrollment confirmation (payment processed + welcome packet sent)
- Cohort-launch day (batch onboarding for 8-15 students at cohort start)
- Re-onboarding scenario: student who paused returns to next cohort

## Required inputs

1. `student_slug` declared (non-PII alias used throughout — never the student's actual company name in shared artifacts)
2. Enrollment confirmed (`student.enrollment_confirmed: true`)
3. Program curriculum spec from `/build-curriculum` (modules + sequence + prerequisites)
4. Pre-enrollment intake form responses (current MRR, current LinkedIn metrics, current outbound metrics, agency stage, what they want to learn, what they fear)
5. Cohort manifest (other students in the same cohort — for pod assignment balance)
6. Mentor capacity (1:1 slots available + group call rhythm)

## The onboarding sequence

### Step 1 — Student subprofile init

```yaml
program:
  students:
    {student_slug}:
      status: onboarding
      cohort_id: {cohort_slug}
      enrolled_at: {date}
      pre_program_baseline:
        agency_stage: { mrr: $X, retainer_clients: N, program_clients: N }
        linkedin_metrics: { followers: N, monthly_impressions: N, monthly_inbound_dms: N }
        outbound_metrics: { weekly_dms_sent: N, monthly_calls_booked: N }
        case_studies: N
      stated_goal: {what they want by end of cohort — verbatim}
      stated_fear: {what they're afraid of — informs mentor approach}
      pod_assignment: pending → {pod_slug}
      first_win_definition: pending → {populated Step 5}
      onboarding_milestones:
        kickoff_call: { due: day_3, status: pending }
        baseline_capture: { due: day_5, status: pending }
        pod_assignment: { due: day_7, status: pending }
        week_1_plan_signoff: { due: day_7, status: pending }
        first_win_target: { due: day_28, status: pending }
```

### Step 2 — Pre-cohort baseline capture (day 0-5)

Capture the student's starting state across:
- Current MRR + retainer/program client count
- Current LinkedIn metrics (followers, monthly impressions, monthly inbound DMs)
- Current outbound metrics (weekly DM volume, monthly calls booked, current close rate)
- Current content cadence (posts/week, current engagement)
- Most recent case study (if any) — used as proof-anchor source for student's own program work

Baseline persists to `program.students.{slug}.pre_program_baseline`. Used at week 4 / 8 / 12 deltas to measure progress objectively.

### Step 3 — Cohort-pod assignment (day 5-7)

Students grouped into pods of 4-6 for accountability. Pod balancing rules:
- Don't put 4 same-stage students together (mix pre-revenue + agency-stage + established for cross-learning)
- Don't put 2 same-vertical students together if the vertical is competitive
- Geographic / timezone clustering only when pod-call timing requires it
- Optional: archetype balance (mix outbound-leaning + content-leaning + hybrid)

Pods get a dedicated async channel. Pod-mate intros happen day 7.

### Step 4 — Week-1 plan (day 5-7)

The student's first-week assignments. Specific, completable, designed to build momentum:

```markdown
## Week 1 Plan — {Student Slug}

By end of week 1, you will have:

1. ☐ Completed voice extraction interview (90 min, scheduled day 7-10)
2. ☐ Shipped 3 LinkedIn posts using the program's pillar architecture (Authority + Personality + Proof)
3. ☐ Sent 25 cold DMs using the program's M1 template (your version, with personalization)
4. ☐ Posted intro to your cohort pod
5. ☐ Attended week-1 live group call (Tuesday 09:00 PT)

If you complete all 5: you're on track for first-win by week 3-4.
```

### Step 5 — First-win definition (day 7)

Per the student's archetype (from /build-first-win-trigger), define what counts as first-win + when it should land:

| Archetype | First-win definition | Target window |
|---|---|---|
| Pre-revenue (no clients yet) | First qualified discovery call booked from program-template DM | Week 2-4 |
| Agency-stage (1-3 retainers) | First $5K+ retainer signed using program offer architecture | Week 4-6 |
| Established (4+ retainers) | First case study documented in program format + first program-tier student inquiry from operator's content | Week 6-8 |
| Pivoting (changing vertical) | First 1,000 impressions on a single LinkedIn post in new vertical | Week 3-5 |

First-win persists to `program.students.{slug}.first_win_definition`. Tracked weekly in `/program-cohort-pulse`.

### Step 6 — Mentor cadence assignment (day 5-7)

Per cohort tier:
- Group calls: weekly 60-min, recorded, attendance tracked
- 1:1 mentor access: 30 min/month per student (high-ticket cohort) or 15 min/month (mid-tier)
- Async support: pod-channel + cohort-wide channel + direct mentor DM (response SLA 24h business)
- Office hours: weekly drop-in 60-min for any student (mentor present, no agenda)

Cadence persists to student's onboarding package + cohort manifest.

### Step 7 — Community ritual onboarding (day 5-10)

Three rituals every student opts into:
1. **Weekly check-in post** in cohort channel (Friday): 1 win, 1 challenge, 1 ask
2. **Peer-feedback exchange** (Tuesday): each student gives 1 piece of feedback to a pod-mate's recent LinkedIn post or DM
3. **End-of-month case study draft** (last day of month): every student drafts a 1-page case study of their progress that month — feeds into program-tier marketing for next cohort

Rituals persist to cohort manifest + student onboarding package.

### Step 8 — Output emission

```markdown
# Program Onboarding Package — {Student Slug}

**Cohort:** {cohort_slug}
**Pod:** {pod_slug}
**Mentor cadence:** Group call Tuesdays 09:00 PT | 1:1 monthly | Pod async
**First-win target:** {definition} by {week}

## Week 1 Plan

[from Step 4]

## Pre-program baseline

[from Step 2 — for delta tracking]

## Cohort rituals

[from Step 7]

## Mentor + escalation contacts

- Primary mentor: {mentor_slug}
- Pod channel: {channel_id}
- Escalation: program-head if 1:1 unanswered >48h
```

## Verification checklist

1. Student subprofile initialized with all baseline + milestone fields
2. Pre-program baseline captured (no missing field)
3. Pod assigned with balance rules respected
4. Week-1 plan delivered to student by day 7
5. First-win definition assigned (matches student's archetype)
6. Mentor cadence agreed and added to student's calendar
7. Community ritual onboarding completed (intro post, ritual cadence understood)
8. Day-7 milestone audit clean

## Common failure modes

| Failure | Likely cause | Fix |
|---|---|---|
| Student doesn't complete week-1 plan | Plan too aggressive OR student over-extended | Mentor 1:1 by day 10 to recalibrate; cut to top-3 priorities |
| First-win target slips past week 6 | Wrong archetype assigned OR first-win definition unrealistic | Re-run /build-first-win-trigger for this student; update definition |
| Pod-mate dynamic not landing | Mismatched personalities OR scheduling conflicts | Re-pod by day 14 if no engagement |
| Student goes silent week 2-3 | Onboarding momentum lost OR personal life intervened | Mentor 1:1 + offer 2-week pause if needed (rare; usually re-engagement works) |
| Group call attendance <60% by week 3 | Time/timezone mismatch OR perceived value weak | Audit timing; surface in /program-cohort-pulse for cohort-wide adjustment |

## Cross-skill routing

- **Upstream:** Program enrollment confirmation (payment + welcome packet); `/build-curriculum` (curriculum spec); `/design-program-offer` (cohort tier definition)
- **Sibling:** `/build-first-win-trigger` (defines first-win archetypes); `/program-cohort-pulse` (weekly tracking starts day 7)
- **Downstream:** All curriculum modules + first-win progression; `/build-retention-pulse` (cohort-level health)

## Cross-references

- `agents/program-mentor.md` (Cycle 2 — TBD) — owning persona
- `agents/program-head.md`
- `INVARIANTS.md` A-9, A-12 (program-bound = client-bound bar)
- `ENCODING.md` — `program.students` schema
- `spec/BLIND-OUTPUT-TEST.md` — onboarding package passes BOT before delivery
- `skills/build-curriculum/SKILL.md`, `skills/design-program-offer/SKILL.md`
- `skills/program-cohort-pulse/SKILL.md`, `skills/build-first-win-trigger/SKILL.md`
- `reference/playbooks/hybrid-full-stack.md` — Phase 3 program-launch context

---

*Skill version 1.0.0 — 2026-05-03*
