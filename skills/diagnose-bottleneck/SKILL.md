---
name: diagnose-bottleneck
slug: /diagnose-bottleneck
agent: agency-director
department: cross-cutting
output_format: bottleneck-diagnosis
required_profiles:
  symptom_or_metric: provided            # operator describes the symptom or names the metric
allowed_scopes:
  - agency-wide
  - client-specific
output_path:
  agency-wide: outputs/{date}/bottleneck-{symptom-slug}.md
  client-specific: outputs/{date}/bottleneck-{client-slug}-{symptom-slug}.md
---

# /diagnose-bottleneck — Symptom → Root-Cause Map

> **Purpose.** The operator describes a symptom — "DM reply rate dropped from 15% to 6% over 4 weeks" or "client X is asking why their post engagement halved" — and the skill works back through the pipeline + encoding layers to identify root cause + the specific skill that fixes it. Different from `/pipeline-pulse`: this is targeted, single-symptom, fast. Different from `/strategic-advisory`: there's an objective right answer (a confirmed root cause), not options-with-tradeoffs.
>
> **Bar.** Operator brings a symptom → 30 minutes later, the operator knows the root cause, the fix skill, the recovery time estimate, and the gate that should have caught it. If the diagnosis ends in "could be a few things, hard to say without more data," the diagnostic ladder was too shallow — re-run.

## When to invoke

- Operator names a metric that moved in the wrong direction with magnitude (≥25% delta in 30 days, OR sustained drift over 60 days)
- Client surfaces a symptom on their account ("my engagement dropped", "calls dried up")
- A pipeline stage in the latest `/pipeline-pulse` flagged `LEAK` or `STARVED` and operator wants targeted root-cause vs. waiting for next pulse
- A specific output keeps getting rejected (ghostwritten posts that the client rewrites at >30% rate; cold DMs that get banned by LinkedIn)
- Anything where the question is "why is THIS specific number broken" rather than "what should I focus on" (the latter → `/pipeline-pulse`) or "what should I decide" (the latter → `/strategic-advisory`)

## Required inputs

1. The symptom in operator-words (verbatim)
2. The metric (named) + the delta (current vs prior or vs target) + the timeframe + the scope (agency-wide or client-slug)
3. Last 60 days of relevant data for the symptom's pipeline stage
4. If client-scoped: the client_slug + the per-client subprofiles (voice, ICP, content_strategy, recent ghostwritten artifacts)
5. Recent operator/client communications around the symptom (DMs, Slack, calls — if available)

If symptom is vague ("things feel off this week"), refuse and route to `/pipeline-pulse` instead. Diagnose-bottleneck operates on **named, quantified, time-bounded symptoms** only.

## The build sequence

### Step 1 — Symptom precision (4 min)

Convert the operator's raw words into a quantified, scoped symptom statement. The statement is the contract for the rest of the diagnosis.

```yaml
symptom_statement:
  raw_operator_words: "<verbatim>"
  metric_name: <one-of-the-named-stage-metrics from `workflows/divisions/acquisition.md`>
  scope: agency-wide | client-{slug}
  current_value: <number with unit>
  baseline_value: <number with unit>
  baseline_window: <e.g., 30d trailing prior-to-current-window>
  delta_pct: <signed pct>
  delta_direction: down | up                  # up can be a problem too (e.g., reply rate up but close rate down = unqualified replies)
  timeframe_of_change: <e.g., "first observed week of 4/14, sharp from 4/21">
  affected_population: <one-line — which leads / which clients / which posts>
  symptom_slug: <kebab-case derived from the above>
```

Precision rules:
- **No "feels like" — only numbers.** If the operator can't name a number, get one. If no instrumentation exists, the diagnosis becomes "instrument and revisit in 14 days."
- **Disambiguate the direction.** "Reply rate is up" can be a symptom (more replies but lower-quality). State *what's bad about the change*, not just the change.
- **Timeframe must be specific.** "Lately" is not a timeframe. Convert to a specific calendar window.

If precision can't be achieved in 4 minutes, that itself is the diagnosis — instrumentation gap. Skill exits with `OUTCOME: instrumentation_required` and a recommendation to wire up the missing metric.

### Step 2 — Bottleneck hypotheses generation (5 min)

Generate 3-5 candidate root causes ranked by **prior likelihood** for this archetype + this symptom. Hypothesis priors come from the failure-modes-by-phase table in `reference/playbooks/hybrid-full-stack.md` and the per-stage health metrics in `workflows/divisions/acquisition.md`.

Hypothesis schema:

```yaml
- hypothesis_id: H1
  layer: pipeline | channel | layer-encoding | external
  one_line: <the candidate root cause>
  prior_likelihood: HIGH | MEDIUM | LOW       # how often this cause produces this symptom in this archetype
  evidence_required_to_confirm: <one-line; must be checkable in <30 minutes>
  evidence_required_to_deny: <one-line>
- hypothesis_id: H2
  ...
```

Common-symptom → common-hypothesis priors (this is the diagnostic prior table, refreshed quarterly):

| Symptom | H1 (highest prior) | H2 | H3 | H4 | H5 (lowest prior) |
|---|---|---|---|---|---|
| Cold DM reply rate dropped | M1 personalization decay (operator-side) | Lead list quality decay (sourcing drift) | LinkedIn algorithmic suppression (sender flagged) | ICP shift (target moved, message didn't) | Seasonal noise |
| Inbound DMs from content dropped | Content engagement plateau (hook decay) | Banned vocabulary creeping in | Pillar drift (less Authority + Proof, more Personality) | Algorithm pocket (account suppressed) | Vertical news cycle dominating attention |
| Application completion rate dropped | App got too long / friction added | Calendar gate moved (less front-of-mind) | ICP shift (apps from wrong segment) | Mobile-vs-desktop UX regression | None |
| Booked-to-held rate dropped | Time-to-call too long (>5 days) | Calendar tool friction | Qualification thread weak (calendar leak — `INVARIANTS.md` N-12 implication) | Operator timezone mismatch | Seasonal |
| Call-to-close rate dropped | Discovery questions don't surface buying trigger | Offer pricing changed without value-stack update | Case-study mismatch to the target avatar | Operator energy on calls | Competitor undercut |
| Client post engagement dropped | Voice drift (per `INVARIANTS.md` N-1, N-2) | Banned vocabulary in hook library | Pillar mix lost the Proof or Authority pillar | Posting cadence disruption | Account suppression on client side |
| Client churned at month 4-6 | Voice drift (cumulative compound) | Scope creep eroding margin | Reporting quality (client doesn't see results) | First-90-days onboarding rushed | Client business-side change |

If symptom is novel, build the hypothesis tree from first principles using the Three-Lens Audit: which pipeline stage is upstream of the broken metric, which channel feeds the stage, which layer encodes the channel.

### Step 3 — Diagnostic ladder (10 min)

For each hypothesis, walk the evidence ladder. Each rung is a check the workspace can run in <30 minutes. The ladder runs in **prior-likelihood order** — H1 first, stop as soon as a rung confirms.

Ladder format per hypothesis:

```yaml
hypothesis_id: H1
ladder:
  - rung: 1
    check: <specific evidence to look at>
    where: <file path, profile path, dashboard, or data source>
    confirms_if: <observable signal>
    denies_if: <observable signal>
    minutes_to_run: <int>
    result: confirmed | denied | inconclusive | not-yet-run
  - rung: 2
    ...
```

Example ladder for "Cold DM reply rate dropped, H1 = M1 personalization decay":

```yaml
hypothesis_id: H1
hypothesis_one_line: M1 personalization decay (operator skipped manual personalization, sequence M1 looks copy-paste)
ladder:
  - rung: 1
    check: Pull last 30 cold DM M1 sends; score personalization specificity (named role, named recent post, named pain) on 0-3 scale
    where: outputs/_dm-log/m1-sends-{date-range}.csv
    confirms_if: avg score <1.5 AND prior 30d avg ≥2.0
    denies_if: avg score ≥2.0
    minutes_to_run: 15
    result: not-yet-run
  - rung: 2
    check: Diff M1 templates in current outbound engine spec vs prior version; check for personalization-token regressions
    where: company.yaml::product_strategy + adapters/{runtime}/m1-template.md history
    confirms_if: tokens removed OR template moved to non-personalized variant
    denies_if: templates unchanged
    minutes_to_run: 5
    result: not-yet-run
  - rung: 3
    check: Operator self-report — has the operator stopped writing M1 manually?
    where: operator interview, 1 question
    confirms_if: yes
    denies_if: no
    minutes_to_run: 2
    result: not-yet-run
```

Rules:
- **Every rung is checkable in under 30 minutes.** If a check requires more, split it or flag as `RUNG_TOO_DEEP`.
- **Each rung names where to look.** No "review the data" — the ladder names the file path or interview question.
- **Stop ladder traversal as soon as a hypothesis is confirmed.** Don't run all hypotheses if H1 confirms; conserve operator attention.
- **If H1, H2, H3 all deny, run H4 and H5.** If all 5 deny, the prior table is wrong — escalate to `/pipeline-pulse` for whole-pipeline view, log the novel symptom + diagnosis path for prior table refinement.

### Step 4 — Triage outcome (3 min)

After the ladder, one of three outcomes:

```yaml
outcome:
  status: root-cause-confirmed | escalate-to-pipeline-pulse | instrumentation-required
  confirmed_hypothesis_id: H1 | H2 | ... | null
  one_line_root_cause: <named cause>
  confidence: HIGH | MEDIUM | LOW
  evidence_anchors: [<list of rung results that confirmed>]
```

- **root-cause-confirmed** — proceed to Step 5 (Fix prescription)
- **escalate-to-pipeline-pulse** — symptom is downstream of a multi-layer issue, single-symptom diagnosis can't resolve it, route to `/pipeline-pulse` and exit
- **instrumentation-required** — the data needed to confirm is not captured; recommend instrumentation + revisit window

If two hypotheses both partially confirm (e.g., H1 confirms at rung 1 but H3 also confirms at rung 2), name the **upstream-most** as primary. Per `INVARIANTS.md` A-1 and `agents/agency-director.md`: fix upstream first.

### Step 5 — Fix prescription (4 min)

Once root cause is named, prescribe the fix as: **specific skill to run + profile to update + recovery time estimate**.

```yaml
fix_prescription:
  primary_skill: </skill-name>            # the workspace skill that addresses the root cause
  primary_skill_scope: agency-wide | client-{slug}
  profile_to_update: <profile.path>       # which profile gets sharper after the fix
  estimated_operator_hours: <int>
  estimated_recovery_time:
    metric_returns_to_baseline_by: <weeks>
    confidence: HIGH | MEDIUM | LOW
    leading_indicator: <named — the canary that says it's working before the lagging metric does>
  prerequisite_skills: [<list — anything that must run first per `spec/CONTEXT-THRESHOLDS.md`>]
  fallback_if_fix_fails: <one-line — the next move if the prescribed fix doesn't move the metric in the recovery window>
```

Recovery time estimates per common fix:

| Fix | Recovery window | Leading indicator |
|---|---|---|
| Re-run `/extract-founder-voice` for client | 2-4 weeks (next 8 ghostwritten posts measured against profile) | Voice-drift score ≥0.85 in next `/voice-drift-detector` run |
| Re-run `/build-icp` (sharpen) | 4-6 weeks (next 200 cold DMs sent against refined ICP) | Cold reply rate +2-4 pts within 14 days |
| Refresh hook library (`/build-hook-library`) | 2-3 weeks (10-15 posts published with new hooks) | Avg first-12-impression dwell-time +15% |
| Tighten application gate (`/build-application` with stricter DQ) | 1-2 weeks (next 20 applications) | Triage pass rate +10-20 pts; show rate +5-10 pts |
| Rebuild qualification thread (`/build-qualification-thread`) | 2-3 weeks (next 30-50 qualifying conversations) | Qualifying-to-application rate +10-15 pts |
| Rebuild outbound engine M1 (`/build-outbound-engine` step 4-5) | 1-2 weeks (next 200 M1 sends) | Cold reply rate restored to prior baseline within 14 days |

If the prescribed fix's recovery window is shorter than the symptom's observed timeframe, that's a flag — the symptom may have multiple compounding causes. Re-run Step 2 with broader hypotheses.

### Step 6 — Prevention encoding (3 min)

What gate or check would have caught this earlier? This step proposes a workspace amendment if appropriate. The diagnosis ends with the system getting smarter, not just the metric getting fixed.

```yaml
prevention:
  what_would_have_caught_this:
    - check_name: <one-line>
      where_it_lives: <skill | invariant | dashboard | weekly-cadence>
      detection_window: <how-soon-after-the-cause-it-would-fire>
      false_positive_risk: <low | medium | high>
  proposed_amendment:
    type: new-invariant | new-skill | new-dashboard-metric | new-cadence-check | new-spec-row | none
    description: <one-line>
    owner_for_decision: operator | foundations-head | acquisition-head | fulfillment-head | marketing-head | program-head
    blocks_recurrence: yes | partial | no
  log_to: outputs/_prevention-proposals/{symptom-slug}.md
```

Examples of prevention amendments:
- Symptom: M1 personalization decay went undetected for 3 weeks → propose `/audit-m1-personalization` weekly check, add row to `/pipeline-pulse` Step 6 health dashboard
- Symptom: voice drift on client X for 8 posts → enforce existing weekly `/voice-drift-detector` cadence (not new — the cadence existed but wasn't running)
- Symptom: novel symptom not in the diagnostic prior table → propose adding a row to the prior table in this skill's Step 2

Not every diagnosis needs an amendment. Recurring symptoms always need one. Routine symptoms with healthy detection don't.

## Validation gate (mandatory before declaring done)

Two checks:

1. **The "operator can act in 30 minutes" test.** From the moment the operator opens the diagnosis, can they: (a) understand the named root cause, (b) start the prescribed fix-skill, (c) tell their team / their VA / their ghostwriter what to do differently? If yes, gate passes. If the operator has to re-interpret the diagnosis, the prescription is too abstract — re-run Step 5.

2. **The recovery-window prediction test.** The fix prescription named a leading indicator and a recovery window. Within the recovery window, did the leading indicator move? If yes, the diagnosis was correct and the prior table priors hold. If no, the fix was wrong — log to `outputs/_diagnosis-misfires/` for prior-table refinement, run again with the next-most-likely hypothesis. After two consecutive misfires on the same symptom class, escalate the prior table itself for review by foundations-head.

## Output format

```yaml
---
artifact_type: bottleneck-diagnosis
scope: agency-wide | client-{slug}
client_slug: null | "<slug>"
symptom_slug: <kebab-case>
diagnosis_date: YYYY-MM-DD
outcome: root-cause-confirmed | escalate-to-pipeline-pulse | instrumentation-required
confidence: HIGH | MEDIUM | LOW
profiles_used: [<list — typically only the profiles touching the affected stage>]
frameworks_used: [three-lens-audit, hybrid-full-stack-failure-modes, acquisition-fsm-health-metrics, diagnostic-prior-table]
hypotheses_evaluated: <int>
hypotheses_confirmed: <int>
ladder_rungs_run: <int>
total_diagnosis_minutes: <int>
validation_gate: passed | failed
---

## TL;DR
Symptom: {one-line}. Root cause: {one-line}. Fix: {skill} → recovery in {weeks}.

## Symptom statement
{the populated symptom_statement from Step 1}

## Hypotheses evaluated
{the populated hypothesis list from Step 2 with prior_likelihood}

## Diagnostic ladder — what was checked
{the populated ladder from Step 3, hypothesis by hypothesis, rung by rung, with results}

## Outcome
{the populated outcome from Step 4}

## Fix prescription
{the populated fix_prescription from Step 5}

## Prevention proposal
{the populated prevention from Step 6}

## Recovery checkpoint
Re-check the leading indicator on {date}. If unmoved, run fallback: {fallback_if_fix_fails}.
```

## Failure modes and guards

- **Diagnosis ends in "could be a few things."** The ladder didn't go deep enough. Guard: every diagnosis must end in `root-cause-confirmed` OR `escalate-to-pipeline-pulse` OR `instrumentation-required`. No fourth option.
- **Operator brings a vague symptom.** "Things feel off." Guard: refuse and route to `/pipeline-pulse`. Diagnose-bottleneck only operates on named, quantified, time-bounded symptoms.
- **The fix is "improve content quality" or other unspecific advice.** Guard: every fix names a specific skill from `skills/_INDEX.md`. If no skill addresses the root cause, the prevention amendment is "encode a new skill" and it gets escalated.
- **Skipping the ladder, jumping to a fix.** Symptom: "the cause is obviously X" without rung-1 check. Guard: even HIGH-prior hypotheses run rung 1. Confirmation cost is small; misdiagnosis cost (running the wrong fix for 2 weeks) is large.
- **Confirming H1 too eagerly.** Symptom: rung 1 partially supports H1, ladder stops, fix runs, doesn't work. Guard: the rung's `confirms_if` clause must be unambiguous. Partial signal = inconclusive, run rung 2.
- **Ignoring the recovery-window prediction.** Symptom: fix runs, operator forgets to re-check, 4 weeks later same symptom. Guard: the diagnosis output includes a calendar reminder for the recovery checkpoint, logged to `outputs/_recovery-checkpoints/{date}.md`.
- **Overlap with `/pipeline-pulse`.** Symptom: operator runs `/diagnose-bottleneck` 6 times in a week instead of one `/pipeline-pulse`. Guard: if the same operator runs ≥3 bottleneck diagnoses in 7 days, recommend `/pipeline-pulse` next instead.
- **Client-scoped symptom, agency-wide profile loaded.** Symptom: client X's engagement dropped, ladder reads agency_voice instead of client_voices[X]. Guard: scope determines profile load; client-{slug} symptoms read client subprofiles per `INVARIANTS.md` A-6.

## Cross-references

- `SYSTEM.md` Layer 2 §6 — Three-Lens Audit (the analytical spine)
- `INVARIANTS.md` A-1 — pipeline → channel → layer audit precedes deliverable
- `INVARIANTS.md` A-6 — client subprofiles for client-bound work
- `INVARIANTS.md` A-9 — rejection signal updates profiles (informs prevention encoding)
- `agents/agency-director.md` — owning agent; failure mode "optimizing the local maximum" guarded by upstream-first rule
- `reference/playbooks/hybrid-full-stack.md` — failure-modes-by-phase table (drives prior likelihood in Step 2)
- `workflows/divisions/acquisition.md` — health metric ranges per stage (defines what "broken" means per metric)
- `spec/CONTEXT-THRESHOLDS.md` — prerequisite skills for fix prescriptions
- `skills/pipeline-pulse/SKILL.md` — escalation target when single-symptom diagnosis is insufficient
- `skills/strategic-advisory/SKILL.md` — when the root cause is structural and requires options-with-tradeoffs
- `skills/_INDEX.md` — full skill catalog for fix prescriptions

## Examples

See `examples/`:
- `example-cold-dm-reply-rate-drop-15-to-6.md` — H1 (M1 decay) confirmed at rung 1, fix recovers in 14d
- `example-client-post-engagement-halved.md` — client-scoped, voice drift confirmed, `/voice-drift-detector` cadence enforced
- `example-call-close-rate-dropped-35-to-18.md` — discovery question gap, escalated to `/build-icp` for buying-trigger refinement
- `example-instrumentation-required-no-data.md` — operator brought symptom with no data behind it, exits with instrumentation rec

---

*Skill version 1.0.0 — 2026-05-03*
