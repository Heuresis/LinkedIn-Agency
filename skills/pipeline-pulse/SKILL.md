---
name: pipeline-pulse
slug: /pipeline-pulse
agent: agency-director
department: cross-cutting
output_format: pipeline-pulse-report
required_profiles: {}                    # reads all profiles; no minimum threshold
allowed_scopes:
  - agency-wide
output_path:
  agency-wide: outputs/{date}/pipeline-pulse.md
---

# /pipeline-pulse — Whole-Pipeline Diagnostic

> **Purpose.** Run the Three-Lens Audit (Pipeline → Channel → Layer) across the entire agency in one pass. Surface where the pipeline is leaking, plateauing, or disconnecting. Output is a decision-ready report the operator reads in under 10 minutes and walks away with 1-3 ranked fixes plus the named skill that addresses each.
>
> **Bar.** If the operator finishes the report and still doesn't know where this week's attention belongs, the pulse is too thin. The artifact's job is to collapse a 2-hour "what should I work on" question into a 10-minute decision.

## When to invoke

- Weekly cadence — every Monday morning before the operator commits the week's attention
- Whenever the operator asks open-ended questions like "what should I focus on?", "where's the leak?", "what's working?"
- After any 30+ day stretch where revenue, calls-booked, or DM reply rate moves more than 25% in either direction
- Before launching any new offer, vertical, or channel (baseline measurement)
- Phase transition checkpoints per `reference/playbooks/hybrid-full-stack.md` (end of month 4, end of month 12)

## Required inputs

1. Last 30 days of pipeline metrics (lead-list → DM-sent → reply → qualified → application → triage-pass → call-booked → call-held → closed → onboarded)
2. Per-channel attribution data (cold DM / warm DM / inbound-content / referral) for the same window
3. Current `company.yaml` snapshot (all 6 profiles, all per-client subprofiles, completeness scores)
4. Current phase per `reference/playbooks/hybrid-full-stack.md` (Phase 1 / 2 / 3)
5. Last 4 weeks of `outputs/{date}/` artifacts (what shipped, what got rejected, what got rewritten)

If pipeline metrics are missing, the skill DOES NOT block — it produces a report flagged `EVIDENCE_THIN` with structural diagnostics only and a recommendation to instrument the missing stages within 7 days.

## The build sequence

### Step 1 — Phase identification (3 min)

Different metrics matter per phase per `reference/playbooks/hybrid-full-stack.md`. Identify the operator's phase before scoring anything:

| Phase | Months | North star | What "healthy" looks like |
|---|---|---|---|
| **Phase 1** | 1-4 | Outbound-led survival + content seeding | 200-300 cold sends/week, 8-15% reply rate, 3-5 retainers signed by month 4 |
| **Phase 2** | 5-12 | Hybrid balance + audience compounding | DM-from-content ≥10/month by m7, ≥25/month by m9, 8-12 retainers, $40-120K MRR |
| **Phase 3** | 13-24+ | Inbound-led + program tier launch | 70%+ acquisition from inbound, program tier producing $200K+ annual |

Phase mis-identification is the most common pulse failure. If revenue says Phase 2 but content engagement is at Phase 1 levels, flag the dissonance — the operator may be running Phase 2 expectations on a Phase 1 content asset.

Output: `phase: 1 | 2 | 3` plus `phase_dissonance_flag: <none | revenue-ahead-of-asset | asset-ahead-of-revenue | other>`.

### Step 2 — Lens 1: Pipeline (10 min)

Count + conversion rate at every stage. Stages map to the FSM in `workflows/divisions/acquisition.md`:

```yaml
pipeline_table:                              # one row per stage
  - stage: lead-list                         # then dm-sent, reply, qualified, application-submitted,
    count_30d: <int>                         # triage-pass, call-booked, call-held, closed, onboarded
    conversion_from_prior: <pct>             # send rate / reply rate / etc — see workflows/divisions/acquisition.md
    delta_vs_prior_30d: <pct>                # closed-to-onboarded catches signing-then-ghosting
```

For each stage, score the conversion against `workflows/divisions/acquisition.md` health metrics. Mark each stage `HEALTHY` (in range), `SOFT` (within 25% below floor), `LEAK` (more than 25% below floor), or `STARVED` (count below the volume needed to support downstream).

The pipeline diagnosis is the **first stage from the top that is not HEALTHY**. Fixing a downstream stage when an upstream stage is starved is waste — per `INVARIANTS.md` A-1 and `agents/agency-director.md` failure mode "optimizing the local maximum."

Output: `pipeline_diagnosis: { first_break: <stage>, severity: SOFT | LEAK | STARVED, downstream_implication: <one-line> }`.

### Step 3 — Lens 2: Channel (8 min)

Per-channel attribution. The four channels:

- **Cold DM** — outbound to never-engaged lists
- **Warm DM** — outbound to engaged-but-not-conversed (likers, commenters, profile-viewers)
- **Inbound-content** — DMs and applications driven by the operator's posts
- **Referral** — existing client/student/peer introductions

For each channel, compute:

```yaml
channel_table:                               # one row per channel (cold-dm, warm-dm, inbound-content, referral)
  - channel: <name>
    pipeline_weight_pct: <pct of total qualified pipeline>
    conversion_to_call: <pct>
    conversion_to_close: <pct>
    cost_per_close: <usd>                    # operator hours + tool cost
    trend_30d: <growing | flat | declining>
```

Phase-aware expected mix:

| Phase | Cold | Warm | Inbound | Referral |
|---|---|---|---|---|
| Phase 1 | 70-85% | 5-15% | 0-10% | 5-10% |
| Phase 2 | 30-50% | 15-25% | 25-40% | 10-15% |
| Phase 3 | 10-20% | 10-20% | 40-60% | 15-25% |

Flag channels that are **off-mix-for-phase** (operator in Phase 2 with 80% cold = inbound is broken; Phase 1 with 60% inbound = case-study-density is misreporting OR the operator is in Phase 2 and didn't notice). Flag channels that are **plateauing** (flat 60+ days while volume in upstream stages is growing). Flag channels that are **producing pipeline weight but not closing** (good top-of-funnel, broken offer-to-channel match).

Output: `channel_diagnosis: { primary_load_bearing: <channel>, plateau_channels: [<channel>], off-mix-flags: [<flag>] }`.

### Step 4 — Lens 3: Layer (10 min)

Which encoding layer is the bottleneck? Run completeness check + symptom map:

Six layers get audited. Per layer, capture completeness + a layer-specific evidence field + a diagnosis verdict:

| Layer | Key fields | Diagnosis values |
|---|---|---|
| **voice** | agency + client-avg completeness, drift_evidence (clients flagged in last 4 weekly audits) | HEALTHY / THIN / DRIFTING |
| **icp** | agency + client-avg completeness, icp_evidence_strength (buying triggers + objection verbatim?) | HEALTHY / THIN / IMPRECISE |
| **offer** | service + program tier completeness, unique_mechanism_clarity, guarantee_specificity | HEALTHY / THIN / UNCLEAR |
| **application-gate** | application_completion_rate, triage_pass_rate, hard_dq_trips_30d | HEALTHY / TOO-LOOSE / TOO-TIGHT |
| **ghostwriter-output** | voice_drift_audits_run_30d, violations_30d, client_rewrites_pct | HEALTHY / DRIFTING / CHURN-RISK |
| **content-engine** | cadence_actual_vs_target, avg_impressions_30d + delta, pillar_balance_vs_spec | HEALTHY / PLATEAU / DECAY |

The layer diagnosis is the layer with the highest expected pipeline-load-impact-per-fix. Voice drift in a 12-client roster = highest impact (multi-client churn risk). Application gate too loose = booked-but-unqualified call burden. ICP imprecise in Phase 1 = whole outbound engine is firing into noise.

Output: `layer_diagnosis: { primary_bottleneck: <layer>, secondary_bottlenecks: [<layer>], evidence: <one-line per> }`.

### Step 5 — Bottleneck synthesis (8 min)

Combine the three lens diagnoses into 1-3 most-load-bearing fixes, ranked by **revenue-impact-per-operator-hour**:

```yaml
ranked_fixes:
  - rank: 1
    fix: <one-line, named>
    rationale: <why this is the load-bearing fix — cite which lens findings converge here>
    skill_to_run: </skill-name>              # the specific workspace skill that addresses it
    profile_to_update: <profile.path>        # the profile that gets sharper after the fix
    estimated_operator_hours: <int>
    estimated_revenue_impact_30d: <usd-band>
    confidence: HIGH | MEDIUM | LOW
  - rank: 2
    ...
  - rank: 3
    ...
```

Synthesis rules:
- **Always include the upstream-most fix** if pipeline + channel + layer all point to the same upstream gap. One fix beats three downstream patches.
- **Never recommend more than 3 fixes per pulse.** Operator attention is the bottleneck; ranking past 3 dilutes execution.
- **Every fix must name the skill that addresses it** — no "improve content quality" hand-waves. If the fix has no owning skill, the fix is "encode a new skill" and the rank-1 owner becomes the operator + foundations-head.
- **Rank-1 fix MUST be do-able this week** (≤8 operator hours). If the load-bearing fix is structural (hire a setter, sunset a vertical), escalate per `agents/agency-director.md` escalation path and rank it `STRUCTURAL_ESCALATION`.

### Step 6 — Health metric assignment (3 min)

For every stage in the pipeline table (Step 2), assign the per-stage health metric vs. healthy range from `workflows/divisions/acquisition.md`. This is the operator's weekly dashboard — same fields every week, so the eye trains on the deltas.

```yaml
health_dashboard:                            # one row per metric, in this order
  - metric: <name>                           # cold-dm-reply-rate, warm-dm-reply-rate, liked-post-dm-reply-rate,
    current: <value>                         # qualifying-to-application-rate, application-completion-rate,
    healthy_range: <from acquisition.md>     # booked-to-held-rate, call-to-close-rate, dm-from-content-rate
    status: HEALTHY | SOFT | LEAK            # (phase-dependent), avg-impressions-per-post (follower-tier-dependent
    trend_4w: <sparkline-descriptor>         # per /build-content-engine Step 5), voice-drift-violations-30d (target 0)
```

Persist to `outputs/{date}/pipeline-pulse.md` AND append the row to `outputs/_dashboards/pipeline-pulse-history.csv` so trend lines compound.

### Step 7 — Operator action list (5 min)

Split the fixes into what the operator does this week vs. what the workspace agents handle. The split protects operator attention.

```yaml
operator_this_week:
  - action: <one-line>
    estimated_hours: <int>
    skill_or_session: </skill-name | session-with-foundations-head | external-action>
    deadline: <day-of-week>
agents_handle:
  - agent: <agent-slug>
    skill: </skill-name>
    scope: <agency-wide | client-slug>
    expected_artifact: <one-line>
    expected_completion: <day-of-week>
escalations_to_operator:
  - issue: <one-line>
    why_escalated: <triggers per `agents/agency-director.md` escalation path>
    decision_needed_by: <day-of-week>
```

Split rules:
- Operator owns: voice calibration, qualification call run, pricing, hiring, firing, brand-voice rewrites of rejected ghostwritten posts
- Workspace handles: drafts, classification, ICP scoring, lead-list filtering, hook ideation, post-mortem analysis, call summaries, report drafts, sequence A/B variants
- Anything ambiguous defaults to operator review (per `SYSTEM.md` §2 — "AI is leverage on operator judgment, not a replacement")

## Validation gate (mandatory before declaring done)

Two checks:

1. **The 10-minute test.** Hand the report to a fresh reader (the operator who didn't run the audit). They should be able to articulate: (a) which phase the agency is in, (b) the rank-1 fix and the skill that addresses it, (c) what they personally do this week vs. what the workspace handles. If they can't do all three from the report alone, the report is too long or the synthesis is too thin.

2. **The "next pulse will move" test.** For each of the rank-1 to rank-3 fixes, predict which dashboard metric will move and by how much in the next 30 days. Log the prediction. Next pulse, score the prediction. Predictions that consistently miss = the layer audit is mis-weighting bottleneck impact, and the recommendation engine needs recalibration.

If either gate fails, do not ship the report. Re-run Step 5 (synthesis) with tighter ranking.

## Output format

```yaml
---
artifact_type: pipeline-pulse-report
scope: agency-wide
pulse_date: YYYY-MM-DD
phase: 1 | 2 | 3
phase_dissonance_flag: <none | ...>
profile_completeness_summary:
  business_context: <pct>
  brand_voice_agency: <pct>
  brand_voice_clients_avg: <pct>
  ideal_customer_profile_agency: <pct>
  ideal_customer_profile_clients_avg: <pct>
  content_strategy_agency: <pct>
  content_strategy_clients_avg: <pct>
  case_studies: <pct>
  product_strategy: <pct>
context_quality_tier: Skeleton | Foundation | Solid | Optimized | Elite
profiles_used: [all]
frameworks_used: [three-lens-audit, hybrid-full-stack-phase-model, acquisition-fsm-health-metrics]
confidence: HIGH | MEDIUM | LOW
validation_gate: passed | failed
---

## TL;DR (the three-line summary)
Phase: {phase}. Rank-1 fix: {fix}. This week, operator: {one action}.

## Lens 1 — Pipeline
{the populated pipeline_table from Step 2}
**Diagnosis:** {pipeline_diagnosis}

## Lens 2 — Channel
{the populated channel_table from Step 3}
**Diagnosis:** {channel_diagnosis}

## Lens 3 — Layer
{the populated layer_audit from Step 4}
**Diagnosis:** {layer_diagnosis}

## Synthesis — Ranked fixes
{the populated ranked_fixes from Step 5}

## Health dashboard (the weekly numbers)
{the populated health_dashboard from Step 6}

## This week — operator action list
{the populated operator_this_week, agents_handle, escalations_to_operator from Step 7}

## Predictions for next pulse
{which dashboard metric moves, by how much, by next pulse date — for scoring}
```

## Failure modes and guards

- **The report becomes a wall of metrics.** Symptom: 8 pages, no decision. Guard: TL;DR is the contract — three lines, phase + rank-1 + this-week-action. If TL;DR is empty or vague, re-run Step 5.
- **All three lenses point downstream while ICP is at 35%.** The layer audit is wrong. Re-weight: ICP gap is always the load-bearing constraint when ICP is below 60%.
- **Recommending more than 3 fixes.** Operator attention dilutes; nothing ships. Hard-cap at 3.
- **Every pulse recommends the same fix.** Either the operator isn't acting on the fix, or the rank-1 is mis-prioritized. After 2 consecutive same-rank-1 pulses with no progress, escalate to operator: "Why isn't this getting done?"
- **Phase mis-identification.** Symptom: dashboard metrics match Phase 1 but operator is told Phase 2. Guard: revenue-tier + months-active are the floor; if asset signals contradict, flag dissonance and recommend `/strategic-advisory` for the phase-positioning question.
- **Pulse runs but no prior pulse exists to trend against.** First pulse is baseline-only — flag as `BASELINE_PULSE` and skip the trend columns. Second pulse onward, full trends.
- **No metrics instrumented.** Skill produces a structural-only report flagged `EVIDENCE_THIN` and rank-1 becomes "instrument the missing pipeline stages within 7 days." Don't pretend to diagnose what's not measured.

## Cross-references

- `SYSTEM.md` Layer 2 §6 — Three-Lens Audit framework
- `INVARIANTS.md` A-1 — pipeline → channel → layer audit precedes deliverable
- `INVARIANTS.md` A-7 — declare context quality tier at session start
- `agents/agency-director.md` — owning agent; runs this skill weekly
- `reference/playbooks/hybrid-full-stack.md` — phase model + per-phase resource allocation + per-phase failure modes
- `workflows/divisions/acquisition.md` — health metric ranges per pipeline stage
- `spec/CONTEXT-THRESHOLDS.md` — profile completeness requirements per skill (drives Layer 3 audit)
- `skills/diagnose-bottleneck/SKILL.md` — targeted follow-up when a single metric needs deeper root-cause work
- `skills/strategic-advisory/SKILL.md` — when the rank-1 fix is structural (hire / sunset / pivot)
- `skills/_INDEX.md` — full skill catalog (used to name the skill in each ranked fix)

## Examples

See `examples/`:
- `example-phase-1-pulse-month-3.md` — outbound-led survival, content seeding, rank-1 = ICP refinement
- `example-phase-2-pulse-month-9.md` — hybrid balance, content plateau, rank-1 = hook library refresh
- `example-phase-3-pulse-month-18.md` — inbound-led, program tier active, rank-1 = voice drift across client roster
- `example-baseline-pulse-no-prior-data.md` — first pulse, evidence-thin, instrumentation rank-1

---

*Skill version 1.0.0 — 2026-05-03*
