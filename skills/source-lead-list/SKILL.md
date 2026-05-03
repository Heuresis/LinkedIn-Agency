---
name: source-lead-list
slug: /source-lead-list
agent: lead-list-builder
department: acquisition
output_format: lead-list-csv-spec
required_profiles:
  ideal_customer_profile: 75              # recursive: agency-icp or client-icp (target scope)
allowed_scopes:
  - agency-leads                          # agency builds list to its own ICP (the agency's outbound)
  - client-leads                          # agency builds list for a client's account, to client's ICP
output_path:
  agency-leads: outputs/{date}/lead-lists/agency-{batch-slug}.csv
  client-leads: outputs/{date}/lead-lists/clients/{client_slug}/{batch-slug}.csv
---

# /source-lead-list — ICP-Precise Prospect List Build

> **Purpose.** Build a 100-500 contact prospect list that an outbound DM operation can run *immediately*. Output is CSV + manifest YAML + sourcing-trail audit log. Quality bar: **≥80% of contacts on the list match the ICP precisely AND ≥40% show recent intent signals.**
>
> **Bar.** A VA running the workspace's outbound engine against this list, on day 1, must hit at-or-above the cold-class baseline reply rate (8-12% per `reference/frameworks/dm/intent-taxonomy.md`). If reply rate falls below baseline, the failure is the list — not the sequence — and this skill re-runs.
>
> **The 1-of-1 primitive.** Most LinkedIn agencies pull a Sales Navigator search, dump 1,000 contacts, and DM all of them. This skill instead **filters → intent-layers → anti-revisits → ICP-fit-scores → channel-routes → batches**. The list that ships is the *output* of a discipline, not a search.

## When to invoke

- New outbound engine spun up (immediately downstream of `/build-outbound-engine` for same scope)
- Existing list exhausted (≥85% of contacts touched by a sequence in the last 60 days)
- Reply rate degrading on a previously-healthy list (likely staleness — refresh)
- Vertical pivot or new ICP segment (build a fresh list for the new segment)
- Quarterly source-quality audit (rotate top-3 source platforms; anti-overfitting to one channel)
- New client onboarding completes — once client-icp ≥75% and client-outbound engine exists, build client-leads list

## Required inputs

1. **ICP profile ≥75%** — `ideal_customer_profile.agency_icp` (for agency scope) OR `ideal_customer_profile.client_icps[{client_slug}]` (for client scope).
2. **Outbound engine spec** — for monthly volume targets, intent-class volume share, channel routing rules.
3. **Lead-list history file** — `outputs/lead-lists/_history.csv` (workspace-wide anti-revisit registry). Created on first run if absent.
4. **Source-platform credentials** — Sales Navigator (Premium tier+), enrichment data source, read access to operator's (or client's) own LinkedIn account for engagement-signal collection.
5. **Batch sizing target** — 25-50 contacts per daily VA-driven batch. Defaults to 30.
6. **Operator availability for first-batch QA** — 20 minutes to spot-check first 25 rows before VA hand-off.

## The build sequence

### Step 1 — ICP scope load (5 min)

Determine scope from invocation. Hydrate the target ICP profile fully — firmographics (role, seniority, company stage, revenue stage, industry, sales motion), demographics (geography), behavioral_patterns (buying_triggers, research_behavior), voice_of_customer (pain_language_patterns), market_sophistication (awareness_level).

If client-leads scope and client subprofile <75%, refuse per `spec/CONTEXT-THRESHOLDS.md` and trigger `/build-icp scope=client-icp client_slug={slug}`. The list is only as good as the ICP — generic ICP produces generic list.

### Step 2 — Filter chain construction (15 min)

Translate the loaded ICP into a layered Sales Navigator filter chain (or equivalent). Stacked, not flat — each layer cuts further. Order matters: cheapest, highest-cardinality filters first.

```yaml
filter_chain:
  layer_1_role:
    role_titles: []                       # 5-12 verbatim titles from ICP firmographics.role
    seniority_levels: []                  # CXO | VP | Director | Head | Owner | Founder
    title_negative_keywords: []           # exclusions: "consultant", "advisor", etc.
  layer_2_company:
    company_size_employees: ""            # range from ICP firmographics.company_stage
    company_revenue_band: ""              # if available
    industry_codes: []                    # ICP firmographics.industry → platform taxonomy
    industry_negative: []
  layer_3_geography:
    countries: []                         # from ICP demographics.geography
    regions_or_metros: []
    timezone_alignment: ""                # if real-time DM cadence requires same business hours
  layer_4_recency_signals:
    posted_on_linkedin_in_last_30d: true  # active on platform — dead profiles waste DM volume
    profile_updated_in_last_90d: true     # active job + active LinkedIn presence
  layer_5_anti_filter:
    excluded_company_list: []             # competitor lists, current/ex-client lists
    excluded_persona_list: []             # gatekeepers, students, irrelevant titles

filter_chain_output:
  estimated_universe_after_filters: 0
  target_universe_for_this_batch: 100-500
  pruning_rate_per_layer: {}              # diagnostic: which filter cut the most
```

Diagnostic rule: if any single layer prunes >85%, upstream filter is too tight (or ICP over-constrained). If layer 4 (recency) prunes >50%, segment may be under-active on LinkedIn — flag.

### Step 3 — Intent layer (15 min)

For each filtered contact, capture intent signals (the WHEN signals; ICP-fit is the WHO signal). Five canonical signals per contact:

| Code | Signal | Source |
|---|---|---|
| S1 | Role change in last 90d | LinkedIn profile experience timestamps |
| S2 | Company funding round in last 90d | News enrichment, LinkedIn company page updates |
| S3 | Company hiring signal in adjacent role | Company LinkedIn careers page, job-board scrape |
| S4 | Public engagement on adjacent topic in last 30d | Monitored adjacent thought-leader account list (5-15 anonymized accounts) |
| S5 | Company growth signal (new product, market expansion, acquisition, public revenue milestone) | Crunchbase, news enrichment |

Per contact: capture *what* signals are present (with timestamp + source URL) — do not score yet. Scoring is the next skill (`/score-lead-intent`). Signals without provenance are dropped per `INVARIANTS.md` N-3.

### Step 4 — Anti-dupe + anti-revisit check (10 min)

Run every contact against `outputs/lead-lists/_history.csv` — the workspace's master anti-revisit registry. Schema columns: `linkedin_profile_url, first_touched_date, last_touched_date, last_intent_class, last_sequence_position, last_outcome, cooldown_until, scope, notes`.

Anti-revisit rules (apply in order):

| Rule | Condition | Action | Rationale |
|---|---|---|---|
| 1 | Touched within 180d | Drop from current batch | Re-touching risks LinkedIn spam-flagging + brand damage |
| 2 | Last outcome = `dq_hard` | Drop permanently | Hard-DQs are operator-confirmed (per `INVARIANTS.md` A-13) |
| 3 | Last outcome = `not_a_fit` | Drop unless re-fit trigger (S2 funding OR S5 growth) — material change re-opens |
| 4 | `no_reply` within 90d | Drop (cooldown = last_touch + 90d) |
| 5 | `dq_soft` within 180d | Drop unless re-fit trigger |
| 6 | Outcome IN `[held, proposal_sent, closed, not_a_fit]` | Drop from outbound; route through nurture instead |
| 7 | Cross-scope appearance (in BOTH agency-leads AND client-leads) | Route to higher-fit scope only — never DM same prospect from two accounts |

If anti-revisit drop rate >50% of filtered universe, agency is hitting same segment repeatedly — flag and recommend (a) ICP segment expansion, (b) longer cooldowns, (c) refresh source platforms.

### Step 5 — ICP-fit scoring per contact (15 min)

For each surviving contact, compute ICP-fit score (0-100). The WHO score — separate from WHEN score in `/score-lead-intent`.

```yaml
icp_fit_score_axes:
  axis_1_role_match:        weight: 25  # exact title→25; adjacent→15; senior-but-wrong-function→5; mismatch→0
  axis_2_company_stage:     weight: 20  # in band→20; one band off→10; two+ off→0
  axis_3_industry_match:    weight: 20  # ICP→20; ICP-adjacent (target_markets)→12; out→0
  axis_4_geography:         weight: 10  # in→10; adjacent geo, same timezone→7; out→0
  axis_5_sales_motion:      weight: 10  # match→10; one-off→5; mismatch→0
  axis_6_buying_trigger:    weight: 15  # trigger observed last 90d→15; last 180d→8; absent→0

scoring_tier_assignment:
  primary_tier:    85-100   # target ≥60% of batch; first-touch priority; operator-personal M1 if 100
  secondary_tier:  70-84    # target ≤40% of batch; VA-handled with workspace M1 + operator review
  drop_tier:       <70      # do not include — wrong WHO; sequence quality cannot rescue wrong-WHO list
```

Diagnostic: if <60% of batch hits primary tier, filter chain (Step 2) was too loose — re-tighten layer 1 (role) or layer 2 (company size).

### Step 6 — Channel-route assignment (10 min)

Each contact gets a channel-route based on platform-presence + role-norms.

| Route | Eligibility | Use | Cost / Limit |
|---|---|---|---|
| `linkedin_dm` | LinkedIn profile + posted last 30d + 1st/2nd-degree (or open profile) | DEFAULT >80% | Free; <100 sends/day per account (LinkedIn detection) |
| `linkedin_inmail` | 3rd-degree + Sales Nav credits | Primary tier only — credits scarce | Credit-budgeted (~10-50/mo per seat) |
| `email` | Verified work email + role checks email regularly | Secondary; supplements LinkedIn for high-tier unresponsive | Enrichment cost; <50 cold/day per sender domain |
| `x_dm` | Active X presence + adjacent topics + open DMs | Tertiary — for X-primary contacts | X rate limits |

Decision tree: route_1 default → route_2 if primary tier and InMail credit available → route_3 if primary tier and verified email exists and routes 1-2 unavailable → route_4 if X-heavy and LinkedIn-dormant → otherwise drop. Distribution target per batch: 75-90% LinkedIn DM, 0-15% InMail, 0-20% email, 0-10% X DM.

Forcing wrong channel is worse than dropping. A founder who lives on LinkedIn ignored on email = wasted send + wasted brand impression.

### Step 7 — Batch organization (5 min)

Split surviving, scored, channel-routed contacts into VA-runnable daily batches per `skills/run-client-dm-ops/SKILL.md (Cycle 2 — TBD)`.

```yaml
batch_organization:
  batch_size_default: 30                  # contacts per VA per day
  batch_size_range: [25, 50]
  batches_per_list_run: "ceil(total_contacts / batch_size)"
  intra_batch_composition:
    primary_tier_share: ">=60%"           # mirrors scoring distribution
    intent_signal_distribution: "even spread across S1-S5; don't bunch all hot signals on one day"
    channel_distribution: "linkedin_dm <=80% of single batch (avoid rate-limit collision)"
  batch_naming_convention:
    pattern: "{date}_{scope}_{batch_number}_{intent_class_target}"
    example_agency: "2026-05-03_agency-leads_b001_cold"
    example_client: "2026-05-03_client-leads_{client_slug}_b001_cold"
  batch_handoff:
    operator_first_batch_qa: "Operator reviews first 25 rows of batch 1 before VA send. Spot-check ICP-fit + intent-signal accuracy."
    consumed_by: "/run-client-dm-ops (Cycle 2 — TBD) for client scope; equivalent agency playbook for agency scope"
```

### Step 8 — Output emission (5 min)

Three artifacts ship per list build:

**Artifact 1 — CSV (the deliverable VA actually uses):** Columns: `row_id, linkedin_profile_url, name_anonymized, role, seniority_band, company_anonymized, company_stage, company_revenue_band, industry, geography, icp_fit_score, icp_fit_tier, intent_signals_present (S1-S5 codes), signal_recency_days, intent_score (placeholder), intent_tier (placeholder), dm_channel, source_platform, source_filter_signature, sourced_date, first_touch_target_date, notes`. Name + company columns ship anonymized in workspace template files (`{first}_{last_initial}` / `{company_descriptor}`); operator-owned CSV instances carry actual values.

**Artifact 2 — Manifest YAML (audit trail):**

```yaml
manifest:
  list_id: "{date}_{scope}_{batch_slug}"
  scope: agency-leads | client-leads
  client_slug: null | "<slug>"
  build_date: YYYY-MM-DD
  icp_source_path: ideal_customer_profile.agency_icp | ...client_icps[{slug}]
  icp_completeness_at_build_time: 0-100
  filter_chain_used: "<reference to Step 2 block>"
  source_platforms: []
  universe_after_filters: 0
  contacts_after_anti_revisit: 0
  contacts_in_primary_tier: 0
  contacts_with_at_least_one_intent_signal: 0
  channel_distribution: {linkedin_dm: 0, linkedin_inmail: 0, email: 0, x_dm: 0}
  batch_count: 0
  validation_gate_result: "passed | failed | not-yet-run"
  consumed_by_skill: "/score-lead-intent (next), /classify-dm-intent (per-contact runtime), /write-dm-sequence (per intent class)"
```

**Artifact 3 — Sourcing-trail audit log** (`outputs/{date}/lead-lists/_audit/{list_id}.log`): per-contact line `{linkedin_url}, {filter_layers_passed}, {intent_signals_captured}, {anti_revisit_rule_evaluated}, {icp_fit_score}, {channel_assigned}, {batch_assigned}`. Forensic record — when reply rate falls below baseline 30 days from now, this log explains why each contact was on the list.

Anti-revisit history file `_history.csv` is appended with every contact added to a batch (status `queued` → flipped to `touched` when VA logs first send).

## Validation gate (mandatory before declaring done)

Three checks. List ships only when all pass.

**Gate 1 — ICP-fit precision.** Manual spot-check 25 random rows. Auditor answers: "would this person plausibly hire {agency} to do {service}?" Pass: ≥80% yes. Fail (60-79%): re-tighten layer 1 (role) or layer 2 (company size). Hard fail (<60%): ICP itself may be miscalibrated — recommend `/build-icp` re-run.

**Gate 2 — Intent-signal density.** Share of batch carrying ≥1 of the five intent signals from Step 3. Pass: ≥40%. Fail (25-39%): extend adjacent-account engagement-monitoring list, add fresh enrichment source, or accept lower density with manifest flag. Hard fail (<25%): segment may be genuinely low-signal — flag and ship as cold-only batch with explicit lower reply-rate expectation.

**Gate 3 — Anti-revisit cleanliness.** Of all contacts in batch, how many appear in `_history.csv` within last 180 days? Pass: 0 (any non-zero is a Step 4 bug). Fail: halt batch — re-touching is the fastest way to get the account spam-flagged.

## Output format

```yaml
---
artifact_type: lead-list-csv-spec
scope: agency-leads | client-leads
client_slug: null | "<slug>"
list_id: "{date}_{scope}_{batch_slug}"
profile_completeness: 0-100
build_date: YYYY-MM-DD
universe_after_filters: 0
contacts_in_list: 0
primary_tier_share: 0.0
intent_signal_density: 0.0
channel_distribution: {linkedin_dm: 0.0, linkedin_inmail: 0.0, email: 0.0, x_dm: 0.0}
batch_count: 0
validation_gates: {gate_1_icp_precision: passed|failed, gate_2_intent_density: passed|failed, gate_3_anti_revisit: passed|failed}
profiles_used: [ideal_customer_profile]
frameworks_used: [intent-taxonomy, anti-revisit-registry, channel-routing-decision-tree]
consumed_by: [/score-lead-intent, /classify-dm-intent, /run-client-dm-ops]
confidence: HIGH | MEDIUM | LOW
gap_flags: [list]
---

{the populated CSV (Artifact 1) — preview first 25 rows + ship full file to output_path}

## Manifest
{the populated manifest YAML (Artifact 2)}

## Sourcing-trail audit log preview
{first 10 lines of audit log + path to full log}

## Validation gate results
{Gate 1 spot-check result, Gate 2 density measurement, Gate 3 anti-revisit verification}

## Next actions
{Run /score-lead-intent on this list to populate intent_score + intent_tier columns. Then route batches to VA with daily-DM playbook.}
```

## Failure modes and guards

- **Filter-chain leak (over-broad).** Symptom: ICP-fit precision <80% in Gate 1. Cause: layer 1 role-title list too inclusive. Guard: enforce per-layer pruning audit in Step 2.
- **Filter-chain over-tightening.** Symptom: universe <50 contacts. Cause: too-many AND-stacked filters. Guard: if universe <100, surface to operator before proceeding.
- **Anti-revisit registry not maintained.** Symptom: VA reports DMing same person twice in 60d from same account. Cause: `_history.csv` not appended on send. Guard: VA daily playbook includes mandatory write-back step. Per `INVARIANTS.md` A-10.
- **Intent signal capture is templated/generic.** Symptom: Gate 2 density looks high but downstream reply rate doesn't lift. Cause: signals marked "present" without verification. Guard: signal capture requires timestamp + source URL — no provenance, no signal.
- **Channel routing forces wrong channel.** Symptom: cold email to founder who never opens email. Guard: route_1 is always first-priority unless explicitly disqualified. Email is supplemental.
- **Cross-scope DM collision.** Symptom: same prospect receives DM from agency-account AND client-account in same week. Guard: `_history.csv` is workspace-wide, scope-tagged, deduplicated on `linkedin_profile_url` regardless of scope.
- **Source platform monoculture.** Symptom: 100% from one source → quality degrades over quarters. Guard: quarterly source-quality audit; never run >75% from one source for >90 days.
- **Real-name leakage in artifacts.** Symptom: contact names appear in workspace examples / templates. Guard: name + company columns anonymized in template files; actual values only in operator-owned CSV instances. Per `INVARIANTS.md` A-11.

## Cross-references

- `skills/build-icp/SKILL.md` — upstream; produces the ICP profile this skill consumes
- `skills/build-outbound-engine/SKILL.md` — upstream; produces the outbound engine spec defining monthly volume + intent-class share
- `skills/score-lead-intent/SKILL.md` — direct downstream; consumes this list and populates intent_score + intent_tier
- `skills/classify-dm-intent/SKILL.md` — runtime per-contact classifier (works in concert with intent score)
- `skills/run-client-dm-ops/SKILL.md` (Cycle 2 — TBD) — consumes batches for VA-driven outreach
- `reference/frameworks/dm/intent-taxonomy.md` — defines C/W/L/M classes; this skill primarily produces C-class candidates (cold) since W/L/M emerge organically from content engagement
- `reference/templates/va-daily-dm-playbook.md` — VA-facing operating procedure
- `agents/acquisition-head.md` — owns the acquisition pipeline; `lead-list-builder` is a delegate
- `workflows/divisions/acquisition.md` — FSM entry point: `NewProspect` state created when this skill emits a contact
- `INVARIANTS.md` N-3 (no invented data; signals require provenance), N-5 (intent classification before DM), N-13 (no real competitor names), A-10 (always log), A-11 (anonymized methodology descriptors)
- `spec/CONTEXT-THRESHOLDS.md` — ICP ≥75% gate enforced here

## Examples

See `examples/`:
- `example-agency-leads-saas-founder-segment.md` — list build for agency's own ICP segment
- `example-client-leads-services-founder-segment.md` — list build for a client whose ICP is B2B services founders
- `example-anti-revisit-rejection-log.md` — annotated audit log showing how anti-revisit rules dropped 30% of a candidate batch
- `example-filter-chain-tightening.md` — before/after filter chain when Gate 1 failed at 65% precision and was re-tightened to 84%

---

*Skill version 1.0.0 — 2026-05-03*
