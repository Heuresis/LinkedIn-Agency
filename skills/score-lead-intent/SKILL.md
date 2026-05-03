---
name: score-lead-intent
slug: /score-lead-intent
agent: lead-list-builder
department: acquisition
output_format: lead-intent-scorecard
required_profiles:
  lead-list spec: exists                  # consumed from /source-lead-list output
allowed_scopes:
  - single-lead                           # score one contact (runtime trigger when a hot signal fires)
  - batch                                 # score every contact in a batch (default — every new list)
output_path: outputs/{date}/lead-intent-scores/{batch-slug}.csv
---

# /score-lead-intent — Pre-DM Intent Scoring

> **Purpose.** Per-lead pre-DM intent scoring. For every contact in a list, compute a 0-100 intent score (the **WHEN** signal) and assign a tier (cold / warm / hot) that determines DM prioritization, sequence selection, and operator-vs-VA handling.
>
> **Bar.** A VA running a high-tier batch first must out-perform a VA running an equal-size random-tier batch by **≥1.5x on qualified-reply rate** within 14 days. If lift is below 1.5x, scoring axes are mis-weighted and this skill re-runs.
>
> **The 1-of-1 distinction.** ICP-fit (from `/source-lead-list`) tells you **WHO** the prospect is. This skill tells you **WHEN** to touch them. Both are scored independently; both are necessary; neither is sufficient. A perfect-fit prospect with no recent intent signal is a future warm — not a today touch. A high-intent prospect outside ICP is a distraction.

## When to invoke

- Immediately after `/source-lead-list` (default — every new list scored before VA pickup)
- Runtime trigger for a single contact (e.g., contact in existing list just changed roles → re-score → may promote tier)
- Quarterly recalibration of decay curves (signal recency weights drift with platform behavior)
- When reply-rate-by-tier ratio compresses (hot-tier reply rate drops to within 30% of cold-tier → axes are stale, recalibrate)
- After a closed-won deal — back-propagate: what was the closed prospect's intent score at first touch? Validates or invalidates axis weights

## Required inputs

1. **Lead-list spec** — output of `/source-lead-list` (CSV + manifest). The intent_score and intent_tier columns will be populated by this skill.
2. **`case_studies.agency_case_studies`** — for axis 4 (lookalike-success). If empty, axis 4 returns 0 for all leads and a gap flag is set in the manifest.
3. **Adjacent thought-leader engagement-monitoring data** — workspace-collected, maintained by content engine + lead-list-builder jointly. Stores: list of monitored adjacent accounts (5-15, anonymized in workspace files) + every like/comment/reshare from filtered prospects in last 60d.
4. **Company-event enrichment data** — funding announcements, exec hires, growth signals (Crunchbase / news enrichment / company LinkedIn page deltas).
5. **Operator availability** — none for batch scoring (fully automated). Single-lead re-score requires no operator. Quarterly decay-curve recalibration requires 30 min operator review.

## The scoring algorithm

Four signal axes, each scoring 0-25 points. Sum = 0-100 intent score. Tier assignment from sum. Axes mirror the four data streams an SDR-equivalent operator manually inspects before a personal DM.

## The build sequence

### Step 1 — Lead list ingestion (5 min)

Load lead-list CSV from `/source-lead-list`. Required columns: `row_id, linkedin_profile_url, icp_fit_score, icp_fit_tier, intent_signals_present (S1-S5 codes), signal_recency_days, dm_channel`. Optional enrichment pulls: role_change_history, company_event_history_90d, public_engagement_history_30d, lookalike_match_signal.

Pre-score filters: skip if `icp_fit_tier == "drop_tier"` (below 70 ICP-fit — don't waste compute); skip if already scored within 24h (idempotency guard).

### Step 2 — Signal axis 1: Role-change recency (0-25 points)

The strongest single intent signal in B2B outbound. A contact who recently changed roles is in a buying window: proving themselves in the new role (will buy tools/services that help them succeed fast), new budget allocation authority, not yet locked into vendor relationships from prior role.

```yaml
axis_1_role_change_recency:
  weight: 25
  data_source: "LinkedIn profile experience timestamps; cross-checked against profile-update timestamps"

  decay_curve:                            # linear decay
    days_0_30:    25                      # peak signal
    days_31_60:   20
    days_61_90:   15
    days_91_120:  10
    days_121_180: 5
    days_181_plus: 0
    no_change_observed: 0

  modifier_role_seniority:
    new_role_more_senior:    1.0          # baseline; promotion implies budget growth
    new_role_lateral:        0.85
    new_role_at_new_company: 1.1          # full budget reset = stronger signal (capped at 25)
    new_role_demotion:       0.5

  modifier_role_relevance_to_offer:
    role_directly_responsible_for_what_agency_sells: 1.0   # e.g. "VP Marketing" for marketing agency
    role_adjacent:                                    0.8   # e.g. "VP Sales" for marketing agency
    role_unrelated:                                   0.4   # e.g. "VP Engineering" for marketing agency

  output_field: axis_1_score              # 0-25
```

### Step 3 — Signal axis 2: Company-event recency (0-25 points)

Funding rounds, executive hires, growth announcements, acquisitions — each a discrete moment where the company has *new posture toward investment*. Founders who just raised are spending; companies who just hired a VP of Marketing are equipping that VP; companies who just acquired are restructuring.

```yaml
axis_2_company_event_recency:
  weight: 25
  data_source: "Company news enrichment + LinkedIn company page updates + Crunchbase or equivalent"

  event_type_base_points:
    funding_round_seed_to_a:       25
    funding_round_b_or_later:      22
    exec_hire_relevant_function:   22     # e.g. new CMO for a marketing agency
    exec_hire_adjacent_function:   15
    public_growth_milestone:       18     # "hit $10M ARR" etc.
    new_product_launch:            18
    market_expansion:              15
    acquisition_merger:            20     # high signal but messy timing — flag for operator
    layoff_or_restructure:         5      # low signal AND brand-risk for outreach — usually skip
    no_event:                      0

  recency_decay_multiplier:               # applied to base_points
    days_0_30:    1.0
    days_31_60:   0.8
    days_61_90:   0.6
    days_91_180:  0.3
    days_181_plus: 0.0

  multi_event_handling: "use highest-scoring single event; do NOT sum (stacking double-counts the same intent window)"
  output_field: axis_2_score              # 0-25 (clamped after multiplier)
```

### Step 4 — Signal axis 3: Public-engagement signal (0-25 points)

A contact who recently liked, commented, or reshared content on **adjacent topics** has self-declared interest in the agency's domain — without ever having interacted with the agency directly. Intent without overlap.

**Adjacent topics** = topics in the ICP's `pain_language_patterns` AND `desire_language_patterns` territory. Engagement on competitor / adjacent thought-leader content (NEVER named in artifacts — methodology descriptors only per `INVARIANTS.md` N-13) is high-signal.

```yaml
axis_3_public_engagement_signal:
  weight: 25
  data_source: "Workspace-monitored adjacent thought-leader account list (5-15 accounts, anonymized in workspace files); track every like/comment/reshare from filtered prospects in rolling 30-60d window"

  engagement_type_base_points:
    reshare_with_commentary:   12         # strongest — public endorsement
    long_comment_3_plus_lines: 10
    short_comment_1_2_lines:   6
    like_only:                 3
    profile_view_of_subject:   4          # if subject has visibility into who viewed their profile

  recency_decay_multiplier:               # applied per engagement event
    days_0_7:    1.0
    days_8_14:   0.85
    days_15_30:  0.6
    days_31_60:  0.3
    days_61_plus: 0.0

  topic_relevance_modifier:
    in_ICP_pain_language_patterns:   1.2  # strongest — direct pain alignment
    in_ICP_desire_language_patterns: 1.0
    adjacent_but_not_core:           0.6
    unrelated:                       0.0

  frequency_stacking: "sum across events, capped at 25 (repeated engagement = sustained interest, unlike axis 2)"
  output_field: axis_3_score              # 0-25 (capped)
```

### Step 5 — Signal axis 4: Lookalike-success signal (0-25 points)

The lead matches the profile of past closed clients. Pulls from `case_studies.agency_case_studies`. The closer the lead resembles a closed-won deal across firmographic + behavioral dimensions, the higher the lookalike score.

```yaml
axis_4_lookalike_success_signal:
  weight: 25
  data_source: "case_studies.agency_case_studies — only entries with use_in_sales=true and proof_assets present"

  prerequisite_check:
    if_zero_validated_case_studies:
      action: "return 0 points for all leads on this axis; set gap_flag=lookalike_axis_unavailable in manifest"
      rationale: "cannot compute lookalike without comparison set"

  similarity_dimensions:
    role_match:           weight: 6  # exact title match→6; closely-related→4; adjacent→2; mismatch→0
    company_stage_match:  weight: 4  # same band as closed-won→4; one off→2; further→0
    industry_match:       weight: 5  # same vertical as ≥2 closed-wons→5; same as 1→3; ICP-listed but no closed-won→1; mismatch→0
    buying_trigger_match: weight: 5  # same trigger event-type as closed-won (e.g. "last agency churned")→5; partial→3; absent→0
    revenue_band_match:   weight: 5  # same band as ≥2 closed-wons→5; same as 1→3; one off→1; further→0

  best_match_aggregation:
    method: "for each closed-won case study, compute 5-dimension match score (0-25). Lead's axis_4 score = MAX match across all case studies."
    rationale: "the lead resembles SOMEONE who closed; doesn't need to resemble everyone"

  staleness_modifier:
    case_studies_older_than_24mo: 0.7
    case_studies_older_than_36mo: excluded

  output_field: axis_4_score              # 0-25
```

### Step 6 — Score aggregation + tier assignment (instant)

Sum the four axes for total intent score. Assign tier.

```yaml
score_aggregation:
  intent_score: axis_1_score + axis_2_score + axis_3_score + axis_4_score   # range 0-100

  tier_assignment:
    cold_tier:   0-40
      label: cold
      meaning: "lead in list (ICP-fit OK) but no current intent signals. Default cold sequence. No prioritization."
      typical_share: "40-60%"
      reply_rate_expectation: "8-12% (Class C baseline per intent-taxonomy.md)"
    warm_tier:   41-70
      label: warm
      meaning: "≥1 moderate intent signal. Prioritize ahead of cold. Use warm-flavored M1 — reference signal directly."
      typical_share: "30-45%"
      reply_rate_expectation: "12-25% (cold sequence with warm M1 lift) OR 25-45% (if treated as Class W)"
    hot_tier:    71-100
      label: hot
      meaning: "multiple converging signals OR one extremely fresh signal. Operator-personal-DM territory. Do NOT VA-handle M1."
      typical_share: "5-15%"
      reply_rate_expectation: "30-60% (operator-personal handling lifts above templated warm)"

  diagnostic_distribution_check:
    hot_tier > 30%: "axes too generous — recalibrate decay curves"
    hot_tier < 3%:  "axes too stingy OR signal-collection coverage too thin — audit Step 4 monitoring list"
    cold_tier > 75%: "intent-signal density on underlying list is low (Gate 2 failure in /source-lead-list); ships with caveat"
```

### Step 7 — Per-tier M1 recommendation + sequence routing

Intent tier is the **routing key** for which DM-sequence M1 the lead receives. Where intent scoring becomes operational — changes who-touches-whom and what the first message says.

| Tier | Recommended sequence | M1 source | Handler | Timing |
|---|---|---|---|---|
| **cold** | Class C cold sequence (per intent-taxonomy.md) | Standard cold M1 with personalization line + pain hypothesis (per `/build-outbound-engine` Step 2) | VA, with operator review of M1 personalization line | Normal batch cadence, no priority |
| **warm** | Class C cold with warm-flavored M1 OR Class W warm if axis 3 dominant | See M1 templates per dominant axis below | VA-sent with operator approval of personalized M1 line | Prioritized ahead of cold-tier in same batch |
| **hot** | Operator-personal DM (no template). Treat as Class L or M equivalent depending on signal type | Operator writes from scratch, hydrating from lead's complete signal payload + ICP voice-of-customer language. Workspace generates draft brief; operator writes actual M1 | Operator-only for M1. VA may handle M2-M3 if M1 gets reply | Highest-priority — sent within 24-72h of scoring (signal decay matters) |

Warm-tier M1 templates by dominant axis:

- **Axis 1 dominant (role change):** "Saw you stepped into {new role} at {company} — congrats. Most {role}s in the first 90 days are figuring out {pain hypothesis from ICP}. True for you?"
- **Axis 2 dominant (company event):** "Saw {company} {event}. Usually means {pain/desire hypothesis}. Curious if that's where you are."
- **Axis 3 dominant (public engagement):** "Saw you commented on {topic} at {adjacent account}. The thing about {specific takeaway} — how's that landing for {company}?"
- **Axis 4 dominant (lookalike):** "I work with a few {similar role}s in {industry}. The one pattern is {pattern from case study, anonymized}. Sound familiar?"

The recommended sequence is **routing**, not auto-sending. Actual sequence selection at runtime is performed by `/classify-dm-intent` per `INVARIANTS.md` N-5 (never DM without intent classification). This skill's recommendation feeds the classifier as a strong prior.

### Step 8 — Output emission (5 min)

Two artifacts ship per scoring run.

**Artifact 1 — Scorecard CSV:** Columns: `row_id, linkedin_profile_url, icp_fit_score (carry forward), icp_fit_tier (carry forward), axis_1_score, axis_1_dominant_signal (e.g. "promoted to Head of Demand Gen 22d ago"), axis_2_score, axis_2_dominant_event (e.g. "Series A 41d ago"), axis_3_score, axis_3_engagement_summary (e.g. "3 likes + 1 long comment on adjacent-account posts in last 14d"), axis_4_score, axis_4_lookalike_match (e.g. "matches closed-won case study client_3 on 4 of 5 dimensions" — anonymized slug only per A-11), intent_score, intent_tier, recommended_sequence, m1_handler (VA | VA-with-operator-review | operator-only), touch_priority (1 = touch first), signal_decay_window_days (must-touch-by date), score_timestamp, notes`.

**Artifact 2 — Tier assignment + routing manifest:**

```yaml
scoring_manifest:
  scorecard_id: "{date}_{batch-slug}"
  source_list_id: "{date}_{scope}_{batch_slug}"
  scope: agency-leads | client-leads
  client_slug: null | "<slug>"
  scoring_date: YYYY-MM-DD

  tier_distribution:
    hot_tier_count: 0
    hot_tier_share: 0.0
    warm_tier_count: 0
    warm_tier_share: 0.0
    cold_tier_count: 0
    cold_tier_share: 0.0

  axis_diagnostics:
    avg_axis_1_score: 0.0
    avg_axis_2_score: 0.0
    avg_axis_3_score: 0.0
    avg_axis_4_score: 0.0
    leads_with_zero_signals: 0           # warning if high
    leads_with_3_plus_axes_scoring: 0    # highest-leverage subset

  routing_distribution:
    operator_only_handling: 0            # hot-tier needing operator M1
    va_with_review: 0                    # warm-tier
    va_only: 0                           # cold-tier

  must_touch_by_dates:                   # signal-decay-driven priority queue
    within_72h: 0
    within_7d: 0
    within_14d: 0
    no_decay_window: 0

  validation_gate_result: "passed | failed | not-yet-run"
  feeds_into: ["/classify-dm-intent (per-contact runtime)", "/run-client-dm-ops (Cycle 2 — TBD)"]
  gap_flags: []                          # e.g. lookalike_axis_unavailable if no case studies
```

The scorecard CSV ships to `outputs/{date}/lead-intent-scores/{batch-slug}.csv`. Manifest ships alongside as `{batch-slug}.manifest.yaml`. The intent_score + intent_tier columns are also written back to the source lead-list CSV (in-place column update) so downstream skills consume a single canonical row per contact.

## Validation gate (mandatory before declaring done)

Three checks. Scorecard ships only when all pass.

**Gate 1 — Distribution sanity.** Tier shares must fall within sane bands: hot 3-30%, warm 20-55%, cold 30-75%. If hot >30%: decay curves too generous OR axes too inclusive — halt scoring; recalibrate Steps 2-5; re-run. If hot <3% AND batch >50: signal-collection coverage too thin OR batch genuinely low-intent — ship with manifest gap flag; flag adjacent-account monitoring list for expansion review.

**Gate 2 — Top-tier handling integrity.** For every hot-tier lead: M1 handler must be operator-only (not VA); recommended sequence must NOT be standard cold; touch priority must be ≤ batch_size / 5 (top 20%). If any hot-tier auto-routed to VA-only or standard cold, halt — Step 7 routing has a bug. Per `INVARIANTS.md` A-13, hot-tier handling cannot bypass operator.

**Gate 3 — Signal decay vs. batch cadence alignment.** Compute must-touch-by date for every warm/hot lead. If >25% of warm/hot have a deadline inside next 72h AND VA daily capacity cannot absorb in 72h, flag the operator: list is too signal-rich for current outbound capacity. Either increase daily DM capacity for 1-2 days OR accept some signals will decay before touch. Default action: surface the conflict; do NOT silently let signals decay.

## Output format

```yaml
---
artifact_type: lead-intent-scorecard
scope: agency-leads | client-leads
client_slug: null | "<slug>"
scorecard_id: "{date}_{batch-slug}"
source_list_id: "{date}_{scope}_{batch_slug}"
scoring_date: YYYY-MM-DD
contacts_scored: 0
tier_distribution: {hot: 0.0, warm: 0.0, cold: 0.0}
must_touch_within_72h_count: 0
operator_only_m1_count: 0
validation_gates: {gate_1_distribution: passed|failed, gate_2_top_tier_routing: passed|failed, gate_3_decay_vs_capacity: passed|failed}
profiles_used: [ideal_customer_profile, case_studies.agency_case_studies, brand_voice.voice_of_customer]
frameworks_used: [intent-taxonomy, four-axis-intent-scoring, signal-decay-curves]
consumed_by: [/classify-dm-intent, /run-client-dm-ops]
confidence: HIGH | MEDIUM | LOW
gap_flags: [list]
---

{the populated scorecard CSV (Artifact 1) — preview top 25 leads sorted by intent_score desc + ship full file to output_path}

## Scoring manifest
{the populated manifest YAML (Artifact 2)}

## Tier breakdown narrative
{1-paragraph: how many hot, warm, cold; what's driving the hot tier (which axes are scoring); which leads need operator-personal DM in next 72h}

## Validation gate results
{Gate 1 distribution, Gate 2 routing integrity, Gate 3 decay vs. capacity}

## Operator action queue
{ordered list of hot-tier leads with: row_id, dominant signal summary, recommended M1 angle, must-touch-by date}

## Next actions
{Run /classify-dm-intent on hot/warm leads at runtime to confirm class assignment. Operator writes hot-tier M1s. VA picks up warm/cold per daily playbook.}
```

## Failure modes and guards

- **Axis double-counting.** Symptom: leads scoring 90+ when manual inspection suggests 60. Cause: same signal feeding multiple axes (e.g., role change AND comment on a post AT the new company — both axis 1 + axis 3 fire on same window). Guard: when axis 1 and axis 3 both fire on engagement *post-dating* the role change, axis 3 multiplied by 0.5.
- **Stale lookalike data.** Symptom: axis 4 scoring high-lookalike against case studies that are 3+ years old and no longer reflect current ICP. Guard: 24mo+ case studies get 0.7 multiplier; 36mo+ excluded.
- **Decay curves miscalibrated for the segment.** Symptom: hot-tier reply rate no better than warm-tier (lift <1.5x). Cause: 0-30d / 30-60d windows don't match this segment's actual buying-cycle. Guard: quarterly decay-curve recalibration. Operator reviews tier-vs-reply-rate data; tightens high-points windows if lift compressed.
- **Operator-bypass on hot tier.** Symptom: a hot-tier lead got a templated M1 from a VA. Guard: Gate 2 catches at scoring time. Runtime catch: VA daily playbook hard rule — hot-tier cannot send without operator-signed M1 line.
- **Lookalike axis without case studies.** Symptom: pre-revenue agency or zero closed-wons → axis 4 returns 0 → max possible score 75. Guard: gap flag in manifest. Tier thresholds remain unchanged (don't artificially compress) — agency knows axis 4 is dark and operates accordingly until first case study logs.
- **Engagement-monitoring list staleness.** Symptom: axis 3 returns 0 for most leads even when underlying segment is active on adjacent topics. Cause: monitored list too small (5 accounts) or wrong accounts. Guard: quarterly review; if axis 3 zero rate >70%, expand monitoring to 10-15 accounts.
- **Re-scoring drift.** Symptom: same lead scored two days returns materially different scores without underlying data change. Cause: enrichment data source instability. Guard: scoring is idempotent within 24h (Step 1 idempotency check); re-scores beyond 24h require enrichment-source timestamp validation.
- **Tier inflation over time.** Symptom: hot-tier share creeps from 8% → 22% over a quarter without underlying intent change. Cause: data-source staleness inflating signal counts. Guard: axes 1-3 require source URL + timestamp; signals without provenance return 0.
- **Real-name leakage in scorecard.** Symptom: closed-won case study client names appear in axis_4_lookalike_match column. Guard: lookalike match references use anonymized slugs only (e.g., "matches closed-won case study `client_3` on 4 of 5 dimensions"). Per `INVARIANTS.md` A-11.

## Cross-references

- `skills/source-lead-list/SKILL.md` — direct upstream; produces the lead-list this skill scores
- `skills/build-icp/SKILL.md` — upstream of upstream; defines what "adjacent topic" means in axis 3 and which dimensions matter in axis 4
- `skills/build-outbound-engine/SKILL.md` — sibling; defines the cold/warm/liked/comment sequences this skill routes leads to
- `skills/classify-dm-intent/SKILL.md` — runtime classifier that consumes this skill's recommended sequence as a prior
- `skills/run-client-dm-ops/SKILL.md` (Cycle 2 — TBD) — consumes the prioritized batch with intent_tier + recommended_sequence columns
- `reference/frameworks/dm/intent-taxonomy.md` — the C/W/L/M sequence taxonomy this skill routes into
- `reference/frameworks/dm/qualification-thread-design.md` — what happens after the M1 lands
- `agents/acquisition-head.md` — owns the acquisition pipeline; both lead-list-builder and dm-strategist roll up here
- `workflows/divisions/acquisition.md` — FSM: this skill operates between `NewProspect` and `IntentClassified`/`ColdSequence`/`WarmSequence`/`LikedPostSequence` states
- `INVARIANTS.md` N-3 (no invented data; signals require provenance), N-5 (intent classification before DM), A-11 (anonymized references), A-13 (hot-tier stays operator-led)
- `spec/CONTEXT-THRESHOLDS.md` — lead-list spec must exist before this skill runs

## Examples

See `examples/`:
- `example-batch-scorecard-saas-founder-segment.md` — full scoring run on 100-contact agency-leads batch, showing tier distribution + top 5 hot-tier with axis breakdowns
- `example-hot-tier-operator-brief.md` — workspace-generated brief for an operator on a single hot-tier lead, all 4 axes itemized + draft M1 angle (operator finalizes voice)
- `example-decay-curve-recalibration.md` — quarterly recalibration showing axis 1 decay curve shifted from 30/60/90 to 21/45/75 after segment data showed faster intent decay
- `example-axis-4-unavailable-pre-revenue-agency.md` — scorecard for a pre-revenue agency with zero case studies; axis 4 returns 0 for all and manifest carries the gap flag

---

*Skill version 1.0.0 — 2026-05-03*
