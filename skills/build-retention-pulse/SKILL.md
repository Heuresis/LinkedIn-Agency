---
name: build-retention-pulse
slug: /build-retention-pulse
agent: program-head
department: program
output_format: retention-pulse-report
required_profiles:
  product_strategy.program_tier: 70
allowed_scopes:
  - active-cohort
  - prior-cohort
  - all-time
output_path:
  active-cohort: outputs/{date}/retention-pulses/active-{cohort_slug}-{period}.md
  prior-cohort: outputs/{date}/retention-pulses/prior-{cohort_slug}-{period}.md
  all-time: outputs/{date}/retention-pulses/all-time-{period}.md
---

# /build-retention-pulse — Cross-Cohort Program Retention Pulse

> **Purpose.** Retention check across cohorts (program-side). Different from `/program-cohort-pulse` (which is weekly health for a single active cohort). This is monthly/quarterly retention analysis: completion rates, refund rates, graduate referrals, alumni-to-retainer conversion, NPS. Outputs the cohort-2 launch-readiness gate.
>
> **Bar.** Cohort 2 should not launch until cohort 1 hits the readiness threshold. Sales pressure trying to launch early without the metrics = cohort 2 refunds + brand damage. The pulse is the gate.

## When to invoke

- **Cron — 1st of each month at 11:00 (program-tier active).** Per `paperclip.manifest.yaml`.
- Quarterly cross-cohort review (program-head + operator)
- Before any cohort-2/3/N launch decision
- Before annual program-tier strategy review

## Required inputs

1. `program.cohorts` data — every cohort's enrollment, completion, refund, and outcome data
2. `program.students.{slug}` data per cohort — first-win achievements, completion status, post-cohort outcomes
3. Refund records + reasons
4. Alumni-to-retainer conversion data (program graduates who became DFY clients)
5. Graduate-referral data (graduates who referred next-cohort enrollees)
6. NPS survey responses (week 12 + 6-month follow-up)

## The pulse sequence

### Step 1 — Scope selection

| Scope | Use case |
|---|---|
| `active-cohort` | Current cohort mid-flight (typically week 4-12) |
| `prior-cohort` | Specific completed cohort (post-cohort retrospective) |
| `all-time` | Cross-cohort patterns across program lifetime |

Different scopes surface different signals.

### Step 2 — Completion rate per cohort

```yaml
cohort_completion:
  cohort_slug: {slug}
  cohort_size: N (enrolled)
  completed: N (% of enrolled)
  paused: N
  refunded: N (% of enrolled)
  decline_pattern: {analysis — when did students drop off}
```

Healthy completion rate: ≥80% for first 3 cohorts; ≥85% from cohort 4 onward (the program tightens with each iteration).

### Step 3 — First-win rate per cohort

Per `/build-first-win-trigger`:

```yaml
first_win_rate:
  cohort_slug: {slug}
  by_week_8: % (target ≥70%)
  by_week_12: % (target ≥85%)
  by_archetype:
    pre_revenue: %
    agency_stage: %
    established: %
    pivoting: %
```

Below 70% by week 8 = curriculum or trigger-definition issue. Surface to `/build-curriculum` refresh.

### Step 4 — Refund rate per cohort

```yaml
refund_rate:
  cohort_slug: {slug}
  refund_count: N
  refund_percent: %
  refund_reasons: [list with counts]
  guarantee_tier_invoked: N (per /design-guarantee tier triggers)
```

Healthy refund rate: <5% with the workspace's guarantee tier; >10% = program design issue.

### Step 5 — Graduate-to-referral rate

How many graduates referred enrollees to subsequent cohorts:

```yaml
graduate_referral:
  cohort_slug: {slug}
  total_graduates: N
  graduates_who_referred: N (%)
  total_referrals_sourced: N
  referrals_who_enrolled: N (%)
```

This is the highest-quality lead source — graduate referrals close at much higher rates because the referrer has lived the experience.

### Step 6 — Alumni-to-retainer conversion

Some program graduates become DFY retainer clients later (high-LTV funnel — they trust the operator + know the methodology):

```yaml
alumni_to_retainer:
  cohort_slug: {slug}
  total_graduates: N
  alumni_who_signed_retainer: N (%)
  total_alumni_retainer_revenue: $
  avg_alumni_retainer_value: $
  alumni_retainer_LTV_vs_avg: ratio
```

Track this across all cohorts — alumni often have higher retention than directly-acquired retainer clients.

### Step 7 — NPS / qualitative pulse

Two surveys per cohort:
- **Week 12 (end of cohort):** "How likely are you to recommend?" + "What's the one thing we should change?"
- **6-month follow-up:** "Did the program deliver what you needed?" + "What do you wish we'd taught more deeply?"

```yaml
nps:
  cohort_slug: {slug}
  week_12_score: 0-100
  week_12_response_rate: %
  six_month_score: 0-100
  six_month_response_rate: %
  top_positive_themes: [list]
  top_improvement_themes: [list]
```

Week-12 vs. 6-month delta is informative. If week-12 NPS = 70 but 6-month NPS = 40, the program felt great in moment but didn't deliver durable change.

### Step 8 — Cohort-N launch-readiness gate

Per `reference/playbooks/hybrid-full-stack.md` Phase 3, cohort-N should not launch until cohort-(N-1) hits all of:

```yaml
cohort_n_readiness:
  cohort_n_minus_1_metrics:
    first_win_rate_by_week_8: % (must be ≥70%)
    completion_rate: % (must be ≥80%)
    refund_rate: % (must be <10%)
    graduate_case_studies_documented: N (must be ≥5)
    nps_week_12: (must be ≥50)
  go_no_go: green / yellow / red
  rationale: [if not green, what to fix before launching cohort N]
```

Hard rule: don't open cohort-N enrollment without green. This is a quality gate the operator enforces against sales pressure.

### Step 9 — Output emission

```markdown
# Retention Pulse — {Scope} — Period {date_range}

## Summary

[overall retention health one-paragraph]

## Per-cohort metrics

| Cohort | Size | Completion | First-win (wk8) | Refund | NPS (wk12) | Alumni→Retainer |
|---|---|---|---|---|---|---|
| {slug-1} | N | % | % | % | score | % |

## Cohort-N launch readiness

[from Step 8]

## Trends + recommendations

[3-5 patterns surfacing across cohorts + actions]

## Risks

[things to watch — declining NPS, refund pattern, alumni conversion drop, etc.]
```

## Verification checklist

1. All cohorts in scope included (no silent drop)
2. Completion + first-win + refund rates computed per cohort
3. Graduate referral + alumni-to-retainer data current
4. NPS scores include both week-12 + 6-month where data exists
5. Cohort-N launch readiness gate applied (green/yellow/red with rationale)
6. Trends include cross-cohort pattern detection (not just per-cohort numbers)
7. Risks surfaced explicitly (declining NPS, refund clustering, alumni dropoff)
8. **Blind Output Test:** would the operator make a clear go/no-go decision on cohort-N from this pulse? Pass = ship.

## Common failure modes

| Failure | Likely cause | Fix |
|---|---|---|
| Cohort-N launched despite yellow/red readiness | Sales pressure overrode quality gate | Hard rule: program-head has veto on cohort launch; operator + program-head sign off jointly |
| First-win rate trends down across cohorts | Curriculum stale OR archetype definitions outdated | Curriculum refresh per `/build-curriculum` quarterly review |
| NPS week-12 high but 6-month low | Program felt great but didn't deliver durable change | Audit which modules students forget by 6-month; reinforce via alumni community |
| Refund rate climbing | Guarantee tier too generous OR program quality slipping | Per `/design-guarantee` tier appropriate check; audit recent cohort delivery |
| Graduate referral rate <10% | Graduates didn't have the experience to refer with conviction | Audit cohort experience for referrability — what made cohort 1 students refer vs. cohort 2 not? |
| Alumni-to-retainer conversion dropping | Alumni community weak OR retainer offer not being surfaced to alumni | Strengthen alumni community ritual; build alumni-specific retainer-tier offer |

## Cross-skill routing

- **Upstream:** `/program-cohort-pulse` (weekly per cohort feeds this monthly/quarterly view); `/build-first-win-trigger` (first-win rate definitions)
- **Sibling:** `/build-curriculum` (curriculum refresh based on retention patterns); `/design-program-offer` (program tier refinements based on cohort-N+1 readiness)
- **Downstream:** Operator's go/no-go on cohort-N launch; annual program strategy review

## Cross-references

- `agents/program-head.md` — owns the readiness gate
- `agents/agency-director.md` — strategic owner
- `INVARIANTS.md` A-12, A-13 (operator on hard gates)
- `reference/playbooks/hybrid-full-stack.md` — Phase 3 readiness criteria
- `skills/program-cohort-pulse/SKILL.md`, `skills/build-first-win-trigger/SKILL.md`
- `skills/build-curriculum/SKILL.md`, `skills/design-program-offer/SKILL.md`, `skills/design-guarantee/SKILL.md`
- `paperclip.manifest.yaml` — 1st-of-month cron

---

*Skill version 1.0.0 — 2026-05-03*
