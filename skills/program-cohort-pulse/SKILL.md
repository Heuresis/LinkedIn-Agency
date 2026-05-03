---
name: program-cohort-pulse
slug: /program-cohort-pulse
agent: program-head
department: program
output_format: cohort-pulse-report
required_profiles:
  program.active_cohort: exists
allowed_scopes:
  - active-cohort
output_path:
  active-cohort: outputs/{date}/cohort-pulses/{cohort_slug}-{week}.md
---

# /program-cohort-pulse — Weekly Cohort Health Check

> **Purpose.** Weekly cohort pulse: per-student engagement, first-win progress, retention risk, mentor capacity, community velocity. Mentor + program-head read it Monday morning to set the week's intervention priorities.
>
> **Bar.** A pulse that catches retention risk in week 3 saves the cohort. A pulse that surfaces it in week 7 doesn't — refunds already requested. Weekly cadence is mandatory.

## When to invoke

- **Cron — Monday 08:00 (weekly, per active cohort).** Per `paperclip.manifest.yaml`.
- After any student churns (cohort recalibration may be needed)
- Before mentor's weekly group call (mentor reads pulse at 08:30 to know where to focus the call)

## Required inputs

1. Active cohort exists (`program.cohorts.{cohort_slug}.status: active`)
2. Cohort week number (week 1-12 for typical 12-week cohort)
3. Per-student data: group-call attendance, async-channel posts, peer-feedback delivered, content shipped per program framework, first-win progress
4. Mentor 1:1 logs for the period (which students used their slot, what was discussed)
5. Community velocity data (cohort posts per day, peer-feedback exchanges, ritual participation)

## The pulse sequence

### Step 1 — Cohort scope load

```yaml
cohort:
  cohort_slug: {slug}
  week_number: {1-12}
  total_students: N
  active_students: N (excluding paused/refunded)
  start_date: {date}
  end_date: {date}
  current_week_focus: {curriculum module being covered}
```

### Step 2 — Engagement metrics (per student)

| Student | Group calls attended (of N) | Async posts (last 7d) | Peer feedback delivered | Content shipped (per program) | Last 1:1 used |
|---|---|---|---|---|---|
| {slug-1} | 4/5 | 3 | 2 | 3 | week 2 |
| {slug-2} | 2/5 | 0 | 0 | 1 | never | ⚠
| ... | | | | | |

Engagement composite score per student (0-100 weighted average across the 5 dimensions). Below 50 = engagement risk flag.

### Step 3 — First-win progress (per student)

Per `/build-first-win-trigger` definitions:

| Student | Archetype | First-win target window | Status |
|---|---|---|---|
| {slug-1} | Agency-stage | week 4-6 | on-track (week 3, 1 retainer in pitch stage) |
| {slug-2} | Pre-revenue | week 2-4 | at-risk (week 3, 0 calls booked yet) |
| ... | | | |

Status definitions:
- **achieved:** first-win confirmed (mentor verified)
- **on-track:** activity consistent with hitting target window
- **at-risk:** activity below pace — intervention needed
- **off-track:** target window passed, no first-win — escalation needed

### Step 4 — Retention risk surface

Any student with:
- Engagement composite <50 for 2+ weeks
- First-win status `at-risk` OR `off-track`
- No async-channel activity for 7+ days
- No 1:1 used by week 4 (high-ticket cohort)
- Refund / pause inquiry surfaced informally

→ Retention risk flag with recommended intervention.

### Step 5 — Mentor capacity check

```yaml
mentor_load:
  group_calls_held: N (of N scheduled)
  1:1s_completed: N (of N available)
  1:1_wait_list: N students waiting
  async_response_avg_hours: N (target <24h)
  office_hours_attendance: N students (avg)
```

If 1:1 wait list >3 students for >1 week: capacity issue. Surface to program-head for either mentor reinforcement OR cohort-size cap.

### Step 6 — Community velocity

```yaml
community_metrics:
  cohort_posts_last_7d: N
  peer_feedback_exchanges_last_7d: N
  weekly_ritual_participation: % (of students)
  case_study_drafts_in_progress: N
  case_study_drafts_completed_this_month: N
```

Cohort posts <2/day = velocity issue. Healthy cohort produces 5-10 posts/day in async channel.

### Step 7 — Cohort-2 readiness check (Phase 3)

Per `reference/playbooks/hybrid-full-stack.md` Phase 3 — cohort-2 launch readiness depends on cohort-1 metrics:

```yaml
cohort_2_readiness:
  cohort_1_first_win_rate_to_date: % (target ≥70% by week 8)
  cohort_1_completion_pace: on-track / at-risk
  graduate_case_studies_pipeline: N (target ≥5 by cohort end)
  refund_count_so_far: N (target ≤5%)
  go_no_go: green / yellow / red
```

Used by program-head to gate cohort-2 enrollment open / close decisions.

### Step 8 — Mentor action queue (the output that matters most)

3-5 prioritized interventions for the week:

```markdown
## Mentor action queue — week of {date}

1. **{student-slug}: 1:1 booking (retention risk)** — engagement composite 35, first-win at-risk. Mentor 1:1 by Wednesday.
2. **{student-slug-2}: pod-recheck** — pod async dynamics broken (no exchanges in 10 days). Re-pod by Friday OR mentor facilitate intro re-do.
3. **Cohort-wide: week 4 group call rebook** — current Tuesday 09:00 PT slot has 50% no-show; survey cohort for better time.
4. **{student-slug-3}: first-win celebration** — they hit it (case study confirmed). Spotlight in Friday cohort post + add to graduate case study queue.
5. **Curriculum module 4 confusion** — 3 students raised same question in async; add 30-min mini-session before Thursday.
```

### Step 9 — Output emission

```markdown
# Cohort Pulse — {Cohort Slug} — Week {N} of {N}

**Active students:** {N}
**Engagement composite (cohort avg):** {0-100}
**First-win achievements to date:** {N} of {total} ({%})
**Retention risk students:** {N}
**Mentor 1:1 wait list:** {N}
**Cohort-2 readiness:** green / yellow / red

## Per-student snapshot

[table from Step 2 + Step 3]

## Retention risk

[from Step 4 with recommended intervention per student]

## Community velocity

[from Step 6]

## Mentor action queue this week

[from Step 8]
```

## Verification checklist

1. All active students included (no silent drop)
2. Engagement composite computed across all 5 dimensions
3. First-win status assigned per student
4. Retention risk surfaced for any student matching criteria
5. Mentor capacity checked + 1:1 wait list size flagged
6. Community velocity computed (posts/day, ritual participation, case study pipeline)
7. Cohort-2 readiness assessed
8. Mentor action queue limited to 3-5 items (more dilutes execution)
9. Pulse delivered Monday 09:00 local — before mentor's group-call prep

## Common failure modes

| Failure | Likely cause | Fix |
|---|---|---|
| Engagement metrics show high but first-win achievements low | Activity-without-application — students consuming, not doing | Reframe weekly check-in to require evidence of action, not consumption |
| Same retention risk students surface week after week with no improvement | Intervention not landing | Escalate to program-head; consider mid-cohort 1:1 with operator |
| 1:1 wait list grows uncontrolled | Mentor capacity too thin OR cohort too large | Add second mentor OR cap next cohort size |
| Cohort-2 readiness yellow/red but program-head opens enrollment anyway | Sales pressure overriding quality gate | Hard rule: don't open cohort-2 enrollment without green readiness; loss now < refunds in cohort 2 |
| Community velocity high but no graduate case studies | Activity isn't producing the artifact this cohort needs to feed cohort 2 | Tighten ritual: weekly check-in requires evidence, monthly case study draft mandatory |
| First-win rate by week 8 <70% | Curriculum gap OR archetype assignments wrong | Audit `/build-first-win-trigger` definitions; consider mid-cohort recalibration |

## Cross-skill routing

- **Upstream:** `/onboard-program-student` (defines per-student baselines), `/build-first-win-trigger` (defines first-win), `/build-curriculum` (curriculum spec)
- **Sibling:** `/build-retention-pulse` (monthly/quarterly version of this — looks across cohorts)
- **Downstream:** Mentor's weekly group call prep; cohort-2 launch decisions (program-head); operator's strategic-advisory if cohort issues persist

## Cross-references

- `agents/program-head.md` — owning persona
- `agents/program-mentor.md` (Cycle 2 — TBD)
- `INVARIANTS.md` A-9, A-12
- `reference/playbooks/hybrid-full-stack.md` — Phase 3 first-win + cohort-2 readiness
- `skills/onboard-program-student/SKILL.md`, `skills/build-first-win-trigger/SKILL.md`
- `skills/build-curriculum/SKILL.md`, `skills/build-retention-pulse/SKILL.md`
- `paperclip.manifest.yaml` — Monday 08:00 cron

---

*Skill version 1.0.0 — 2026-05-03*
