# Context Thresholds

> The unlock gates. Each skill declares minimum profile completeness before it can execute. This spec is the master table.
>
> **Enforcement.** Hard-block. The workspace runtime checks profile scores at skill invocation; below threshold = halt + activate interview-mode to fill the gap.

## How thresholds work

Every profile is scored 0-100 per `ENCODING.md`. For recursive scopes (per-client subprofiles), the score is computed per-client, not globally.

A skill declares its requirements in its `SKILL.md` frontmatter:

```yaml
required_profiles:
  brand_voice.client_voices[{client_slug}]: 80
  ideal_customer_profile.client_icps[{client_slug}]: 75
  content_strategy.client_content[{client_slug}]: 60
```

At runtime, the workspace evaluates each requirement. If ANY requirement fails, the skill returns:

```
SKILL_BLOCKED — context_threshold_unmet

Required: brand_voice.client_voices[acme] ≥ 80
Current:  brand_voice.client_voices[acme] = 55

Recommended: run /extract-founder-voice with scope=client-voice client_slug=acme.
```

The operator can then either run the prerequisite OR explicitly override with `--override` (logged + flagged for review).

## Master threshold table

### Foundations skills

| Skill | Profile | Threshold |
|---|---|---|
| `/extract-founder-voice` (any scope) | brand_voice (the target scope) | 0 (this skill builds it) |
| `/build-icp` (any scope) | ideal_customer_profile (the target scope) | 0 (this skill builds it) |
| `/build-positioning` | business_context | 50 |
| | ideal_customer_profile.agency_icp | 70 |
| | brand_voice.agency_voice | 60 |
| `/define-mission` | business_context.basic_info | 50 |

### Offers & Programs skills

| Skill | Profile | Threshold |
|---|---|---|
| `/design-retainer-offer` | business_context | 50 |
| | ideal_customer_profile.agency_icp | 70 |
| | brand_voice.agency_voice | 60 |
| | case_studies.agency_case_studies | 30 (≥1 validated) |
| `/design-program-offer` | business_context | 50 |
| | ideal_customer_profile.agency_icp | 75 |
| | product_strategy.service_tier | 70 (program is downstream of service tier) |
| | case_studies.agency_case_studies | 60 (≥3 validated) |
| `/build-value-stack` | product_strategy.service_tier (target offer) | 50 |
| `/design-guarantee` | product_strategy (target offer) | 60 |
| | case_studies.agency_case_studies | 60 |
| `/build-offer-ladder` | product_strategy.service_tier | 70 |
| | product_strategy.program_tier | 50 |

### Marketing (Content) skills

| Skill | Profile | Threshold |
|---|---|---|
| `/build-content-engine` (agency-content) | brand_voice.agency_voice | 60 |
| | ideal_customer_profile.agency_icp | 70 |
| | product_strategy.service_tier | 50 |
| `/build-content-engine` (client-content) | brand_voice.client_voices[slug] | 70 |
| | ideal_customer_profile.client_icps[slug] | 70 |
| | product_strategy.service_tier (target offer) | 50 |
| `/build-hook-library` | brand_voice (target scope) | 70 |
| | content_strategy (target scope) | 60 |
| `/build-posting-calendar` | content_strategy (target scope) | 70 |
| `/write-linkedin-post` (founder post) | brand_voice.agency_voice | 80 |
| | content_strategy.agency_content | 70 |
| | ideal_customer_profile.agency_icp | 60 |
| `/ghostwrite-client-post` | brand_voice.client_voices[slug] | 85 |
| | content_strategy.client_content[slug] | 70 |
| | ideal_customer_profile.client_icps[slug] | 60 |
| `/repurpose-to-x` | source post artifact | exists |
| | brand_voice (target scope) | 60 |
| `/repurpose-to-ig` | same as `/repurpose-to-x` | |
| `/score-hook` | content_strategy (target scope) | 60 |

### Acquisition (Sales) skills

| Skill | Profile | Threshold |
|---|---|---|
| `/build-outbound-engine` (agency) | ideal_customer_profile.agency_icp | 75 |
| | brand_voice.agency_voice | 60 |
| | product_strategy.service_tier | 60 |
| `/build-outbound-engine` (client) | ideal_customer_profile.client_icps[slug] | 75 |
| | brand_voice.client_voices[slug] | 60 |
| | product_strategy (per client deliverable spec) | 60 |
| `/write-dm-sequence` | outbound engine spec | exists |
| | intent class | declared |
| `/classify-dm-intent` | (no profile required — runtime classifier) | — |
| `/build-qualification-thread` | ideal_customer_profile (target scope) | 75 |
| | product_strategy (target offer) | 60 |
| `/source-lead-list` | ideal_customer_profile (target scope) | 75 |
| `/score-lead-intent` | lead-list spec | exists |
| `/build-call-prep` | ideal_customer_profile (target scope) | 70 |
| | product_strategy (target offer) | 70 |
| | case_studies.agency_case_studies | 50 |
| `/summarize-discovery-call` | call recording or transcript | exists |
| `/build-application` | ideal_customer_profile (target scope) | 70 |
| | product_strategy (target offer) | 70 |
| | DQ criteria from `/build-icp` Step 2 | exists |
| `/triage-application` | application form | submitted |
| | DQ criteria | exists |
| `/no-show-recovery` | call record + reason | exists |

### Fulfillment skills

| Skill | Profile | Threshold |
|---|---|---|
| `/onboard-client` | offer signed | true |
| | client_slug | declared |
| | initial intake form | submitted |
| `/voice-drift-detector` | brand_voice.client_voices[slug] | 70 |
| | last 20 client-validated posts | available |
| | last 20 ghostwritten posts | available |
| `/post-approval-tracker` | client_slug | declared |
| | client approval workflow defined | true |
| `/client-content-sourcing-pulse` | client_slug | declared |
| `/build-client-report` | client_slug | declared |
| | attribution data for reporting period | available |
| | offer deliverables spec | exists |
| `/build-attribution-trace` | client metric source data | available |
| `/run-client-dm-ops` | client outbound engine spec | exists |
| | lead list | exists |

### Program skills

| Skill | Profile | Threshold |
|---|---|---|
| `/onboard-program-student` | enrollment confirmed | true |
| | student_slug | declared |
| | program curriculum spec | exists |
| `/program-cohort-pulse` | active cohort exists | true |
| `/build-curriculum` | product_strategy.program_tier | 70 |
| | case_studies.agency_case_studies | 70 (curriculum builds from real wins) |
| `/build-first-win-trigger` | program curriculum spec | exists |
| `/build-retention-pulse` | product_strategy.program_tier | 70 |

### Cross-cutting skills

| Skill | Profile | Threshold |
|---|---|---|
| `/pipeline-pulse` | (reads all profiles, no threshold) | — |
| `/strategic-advisory` | (reads all profiles, no threshold) | — |
| `/diagnose-bottleneck` | metric or symptom provided | exists |

## Override protocol

Operator can override any threshold via:

```bash
/skill-name --override-threshold profile.path=current_score
```

Every override:
1. Logs to `outputs/{date}/threshold-overrides.log` with reason
2. Adds the artifact to a quarterly review queue
3. Flags the artifact metadata as `OVERRIDE_USED`
4. The downstream skill that consumes the artifact re-checks if profile has improved before re-execution

The override is for edge cases (operator confidence, time pressure on a low-stakes asset). Persistent override usage = the threshold is wrong and needs to move (operator + foundations-head review quarterly).

## Why the thresholds are set where they are

The thresholds are calibrated against the cost asymmetry of false positives vs. false negatives.

**False positive (skill blocked when it could have produced acceptable work):** operator runs the upstream skill they didn't strictly need. Cost = 30-90 minutes of interview time.

**False negative (skill executed when it should have been blocked):** workspace ships a generic, voice-off, or ICP-mismatched artifact. Cost = client churn (months of revenue) + brand damage + corrupted feedback signal.

Since false negative cost ≫ false positive cost, thresholds err HIGH. The operator who feels they're "always being interviewed" is in the early cycles of a workspace that compounds — by Cycle 3, profile completeness stays high and the interview cadence drops sharply.

## Cross-references

- `ENCODING.md` — the 6-profile schema
- `INVARIANTS.md` A-5 — dependency chain
- `agents/foundations-head.md` — owns threshold gating logic
- `skills/_INDEX.md` — full skill catalog with per-skill threshold declarations

---

*Spec version 1.0.0 — 2026-05-03*
