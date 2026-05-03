# Voice Drift Detection

> The methodology behind weekly per-client voice audits — why cosine similarity, why per-axis breakdown, why weekly cadence, and what the threshold logic must do to catch drift before the client churns.

## Why this framework exists

Voice drift is the dominant churn mechanism in LinkedIn ghostwriting. The pattern is predictable: a ghostwriter takes on a client, the first 5 posts are tightly voice-matched because the voice profile is fresh, and then — over weeks 4 through 12 — the ghostwriter's defaults gradually contaminate the output. By post 20 the client reads a draft and thinks "this doesn't sound like me anymore." By post 30 they churn.

Most agencies do not measure drift. They wait for the client to complain. By the time the complaint arrives, the trust is broken and the engagement is unrecoverable. Per `INVARIANTS.md` N-2, this workspace runs a mandatory weekly audit per active client with cosine-similarity scoring, per-axis breakdown, and threshold-driven halts.

The framework exists because cosine similarity alone is not enough — single-number drift can hide axis-specific failures (e.g., the ghostwriter is matching sentence structure but using vocabulary the client banned). And weekly cadence is not arbitrary — drift compounds inside the 4-week churn window, and any cadence slower than weekly lets the agency miss the inflection point.

This framework is implemented by `/voice-drift-detector`. The skill operationalizes; this framework explains the why.

## The 7 rules

### Rule 1 — Weekly cadence (not monthly)

The math: drift compounds inside a 4-week window. A weekly audit catches inflection at week 1 or week 2, leaving 2-3 weeks of remediation runway before the client notices. A monthly audit catches inflection at week 4 — same week the client notices — leaving zero runway.

Per `INVARIANTS.md` A-12, the audit runs every Monday per active client. Cron-triggered via `paperclip.manifest.yaml`. Manual triggers also valid (after voice-extraction profile update, ghostwriter handoff, batch >5 posts shipped in a single day, or client-flagged "doesn't sound like me" feedback).

The cost of weekly cadence is low (~5 minutes of operator + AM attention per audit when audit passes). The cost of monthly cadence is high (single client churn = $5K-$10K MRR loss + case-study density damage). The trade is correct.

### Rule 2 — Cosine similarity vs. last 20 client-validated posts

The reference set is the load-bearing artifact. It contains posts the client wrote OR explicitly approved as "sounds like me." Stored at `_private/clients/{slug}/voice-reference-set/`.

Why client-validated specifically: posts the client wrote may not actually sound like the version of themselves they want to project on LinkedIn (operator-side editing of past content is common). Posts the client explicitly tagged "this is the voice I want" capture what the client wants the ghostwriter to converge on, not just what the client historically produced.

Why 20: below 20, sample-size noise dominates the similarity score. Above 30, the reference set becomes outdated faster than it gets replenished. 20 is the band where reliability and currency balance.

The audit computes pairwise cosine similarity between the reference set and the ghostwritten set (last 20 ghostwritten posts within last 30 days, or fewer if fewer have shipped — minimum 5 to compute). Mean of pairwise similarities = the headline `similarity_score` ∈ [0, 1].

### Rule 3 — 5-axis breakdown (single number isn't enough)

A single similarity score can hide axis-specific failures. Five axes, each independently analyzed:

| Axis | What it catches |
|---|---|
| **Signature-phrase usage** | Does the ghostwriter use the client's known phrases at the same rate as reference? Pass: ≥70% of reference frequency. |
| **Sentence-structure variance** | Are sentence lengths + structures (declarative / interrogative / fragment) within client's range? Pass: KL-divergence ≤0.15 against reference distribution. |
| **Vocabulary do/don't violations** | Did the ghostwriter use any phrase from `phrases_to_avoid`? Pass: zero violations. |
| **Decision-frame consistency** | Does the post argue with the client's known decision-frame? (e.g., client always frames B2B SaaS through unit-economics; ghostwritten post frames it through brand-marketing — drift.) Pass: ≤2 frame violations in 5 posts. |
| **Story-shape consistency** | Do narrative posts follow the client's preferred shape (build-up vs. payoff-first vs. anecdote-then-frame)? Pass: ≤2 shape violations in 5 posts. |

Per-axis output: `pass / soft-fail / hard-fail`. The single number is the headline; the per-axis breakdown is the diagnosis.

### Rule 4 — Threshold per client (typically 0.78-0.85)

The threshold is set during `/onboard-client` Step 3 and tunable per client preference. Some founders want a tight 0.85 (high-distinctiveness voice, low tolerance for variance). Some accept 0.75 (more conversational voice, broader band of acceptable variance).

Threshold logic at audit time:

```
if similarity_score >= voice_drift_threshold AND zero hard-fail axes:
    audit_result: PASS
elif similarity_score < voice_drift_threshold OR ≥2 soft-fail axes:
    audit_result: SOFT_DRIFT
elif ≥1 hard-fail axis OR similarity_score < (voice_drift_threshold - 0.10):
    audit_result: HARD_DRIFT
```

Workspace default is 0.80; client-specific override stored in `clients.{slug}.voice_drift_threshold`. The audit always uses the client's specific threshold, never the workspace default for that client.

### Rule 5 — Root-cause hypothesis when drift detected (4 patterns)

Drift detection is not enough. The audit must surface a hypothesis about what caused the drift, because the remediation differs by cause. Four canonical patterns:

| Pattern | Likely cause | Remediation |
|---|---|---|
| Signature-phrase rate dropped + sentence-structure unchanged | Ghostwriter using template defaults; voice profile not loaded properly | Recalibrate ghostwriter against last 10 reference posts; verify recursive scope loaded correctly per N-1 |
| All axes degraded uniformly | Voice profile gone stale (client's actual voice has shifted but profile not updated) | Book re-extraction interview within 7 days; pause new ghostwriting until profile refresh |
| Vocabulary violations + decision-frame violations | Ghostwriter pulled from agency-voice OR another client's voice | Recursive scope error per N-1; halt + audit which voice was loaded; rerun |
| Single-axis hard-fail, others pass | Sample-size noise OR a specific outlier post triggered detection | Re-audit in 7 days with fresh ghostwritten batch; if persists, escalate |

If none of the four patterns fits, the audit surfaces "diagnostic-required" and the AM books a 1-week diagnostic: hold ghostwriter constant, check if drift persists across new batch; if yes → profile stale; if no → ghostwriter calibration.

### Rule 6 — Hard-drift halts ghostwriting (per N-2)

Per `INVARIANTS.md` N-2, hard-drift triggers a hard halt:
- All new ghostwriting for this client paused
- Re-extraction interview booked within 5 days
- Last 5 ghostwritten posts removed from the approval queue if not yet published
- AM and operator notified

Operator override is allowed in the rare case of confirmed false positive (e.g., a single outlier post triggered detection but the operator confirms the rest of the batch is voice-matched). Override requires explicit written reason logged to the audit record. Override does not bypass the requirement to re-audit in 7 days.

Soft-drift triggers a soft halt: next 3 posts paused pending recalibration; AM messages ghostwriter with reference set; re-audit in 7 days. Operator override allowed if root cause is sample-size noise.

### Rule 7 — Trend matters (3-week downward trend = drift)

Individual audits can pass while the trajectory is downward. A client whose audits read 0.86 → 0.83 → 0.81 → 0.79 across 4 weeks is drifting even though every single audit passes a 0.78 threshold.

Trend tracking: rolling 12-week similarity-score chart per client. Sustained downward trend (3+ consecutive weeks declining) is treated as drift even if individual audits pass. AM surfaces the trend in the Monday brief; operator decides whether to trigger early recalibration or wait for the next audit cycle.

Trend-drift remediation = same as soft-drift remediation: ghostwriter recalibration against fresh reference set, no hard halt unless an individual audit also hard-fails.

## Output template

```yaml
voice_drift_audit:
  client_slug: ""
  audit_date: ""
  audit_week: ""
  audit_result: PASS | SOFT_DRIFT | HARD_DRIFT
  audit_confidence: HIGH | MEDIUM         # Medium if reference set <10 OR ghostwritten set <5
  similarity_score: 0.00
  voice_drift_threshold: 0.00             # The client's specific threshold
  reference_set_count: 0                  # ≥10 for HIGH confidence
  ghostwritten_set_count: 0               # ≥5 to compute
  per_axis_breakdown:
    signature_phrase_usage: pass | soft-fail | hard-fail
    sentence_structure_variance: pass | soft-fail | hard-fail
    vocabulary_violations: pass | soft-fail | hard-fail
    decision_frame_consistency: pass | soft-fail | hard-fail
    story_shape_consistency: pass | soft-fail | hard-fail
  drift_root_cause_hypothesis: ""         # 1 of 4 canonical patterns OR "diagnostic-required"
  recommended_action: ""
  operator_override_required: true | false
  trend_data:
    similarity_score_trailing_12_weeks: []
    trend_direction: stable | upward | downward
    consecutive_downward_weeks: 0
  posts_in_queue_affected: 0
  next_audit_due: ""
```

## Failure modes

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| False positive (audit flags drift, client + operator say posts sound right) | Refund-time disputes about whether voice matched | Reference set contains posts client doesn't actually love; threshold too tight | Audit reference set with client; tune threshold downward 0.02 |
| False negative (audit passes, client says "this doesn't sound like me") | Client churn in month 3-4 despite passing audits | Reference set too narrow; profile thin; embedding method missing nuance | Expand reference set to 25+ posts; rerun voice-extraction; consider second embedding method for cross-check |
| Audit produces inconsistent results week-to-week | AM and operator lose trust in the audit | Ghostwritten sample too small; high variance | Increase audit window to 14 days; require ≥10 ghostwritten posts in window |
| Drift detected but root cause unclear | Remediation paralysis; ghostwriting halts indefinitely | All 4 patterns plausible | Run 1-week diagnostic per Rule 5; choose remediation based on diagnostic outcome |
| HARD_DRIFT triggered on a single outlier | Operator overrides repeatedly | Single-axis hard-fail from one post | Per Rule 5 pattern 4 — operator override allowed with explicit reason logged; re-audit in 7 days |
| Trend missed because individual audits pass | Late churn after weeks of slow drift | Audit looks at single-week scores only (Rule 7 ignored) | Surface trailing-12-week chart in every Monday brief; treat 3-week downward trend as drift |
| Monthly cadence used instead of weekly | Drift caught only after client complaint | Cadence weakened (Rule 1 violated) | Restore weekly cron; AM responsible for completion within 24 hrs of trigger |
| Recursive scope error (agency voice loaded for a client) | Vocabulary + decision-frame violations across the board | N-1 recursive scope error | Halt audit; verify which voice was loaded; rerun with correct client subprofile |

## Cross-references

- `skills/voice-drift-detector/SKILL.md` — operationalizes this framework (the build skill)
- `skills/onboard-client/SKILL.md` Step 3 — sets the per-client threshold during onboarding
- `skills/extract-founder-voice/SKILL.md` — produces the voice profile audited against
- `skills/ghostwrite-client-post/SKILL.md` Gate 7c — pre-publish drift check using the same scoring fabric
- `skills/post-approval-tracker/SKILL.md` — reads audit result before approval
- `skills/build-client-report/SKILL.md` — monthly drift trend section
- `INVARIANTS.md` N-1 (no client ghostwriting without voice profile), N-2 (mandatory voice-drift check), A-6 (operator/AM review for client-bound), A-12 (weekly cadence)
- `ENCODING.md` — `brand_voice.client_voices.{client_slug}` schema + reference-set storage path
- `paperclip.manifest.yaml` — Monday 09:00 cron trigger

---

*Framework version 1.0.0 — 2026-05-03*
