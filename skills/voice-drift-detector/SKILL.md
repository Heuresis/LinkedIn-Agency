---
name: voice-drift-detector
slug: /voice-drift-detector
agent: account-manager
department: fulfillment
output_format: voice-drift-audit
required_profiles:
  brand_voice.client_voices[{client_slug}]: 70
  client_validated_posts: 20
  ghostwritten_posts_last_30d: 20
allowed_scopes:
  - single-client
output_path:
  single-client: outputs/{date}/voice-drift-audits/{client_slug}-{week}.md
---

# /voice-drift-detector — Weekly Per-Client Voice Audit

> **Purpose.** Measure whether the ghostwritten posts shipping for a client still sound like the client. Output is a 1-page brief: similarity score, per-axis breakdown, root-cause hypothesis if drift detected, recommended action. Runs every Monday across every active client.
>
> **Bar.** Most ghostwriting churn happens because drift goes unmeasured for 8-12 weeks. By then the client says "this doesn't sound like me" and exits. This skill measures drift weekly and surfaces it before the client notices. If the audit doesn't catch drift in time, the workspace's silent moat fails — and the agency reverts to the commodity-agency mean.

## When to invoke

- **Cron — Monday 09:00 (weekly per active client).** Per `paperclip.manifest.yaml`, this is the workspace's silent quality engine.
- Manually after any of: voice-extraction profile update, ghostwriter handoff, batch of >5 posts shipped in a single day, client-flagged "doesn't sound like me" feedback
- Mandatory before publishing any post on a client whose last audit was >7 days ago (per A-6)

## Required inputs

1. `brand_voice.client_voices.{client_slug}` profile, completeness ≥70% (per ENCODING.md)
2. Last 20 client-validated posts (the reference set — posts the client wrote OR explicitly approved as "sounds like me"). Stored at `_private/clients/{slug}/voice-reference-set/`.
3. Last 20 ghostwritten posts (or fewer if fewer have shipped — minimum 5 to compute) within last 30 days. Stored at `outputs/{date}/posts/clients/{slug}/`.
4. Client's `voice_drift_threshold` value (typically 0.78-0.85, set during `/onboard-client` Step 3 and tunable per client preference)
5. Workspace-level signature-phrase library + banned-phrase library for this client

## The audit sequence

### Step 1 — Recursive scope load

Hard-load **only** the client's subprofile. Do NOT pull `brand_voice.agency_voice` or any other client's voice. Per `INVARIANTS.md` N-1, recursive scope errors here cascade into false-positive drift OR false-negative passes.

```yaml
loaded:
  scope: client-voice
  client_slug: {slug}
  voice_profile_completeness: {0-100}
  voice_drift_threshold: {0.78-0.85}
  reference_set_count: {N — must be ≥10 for high-confidence audit, ≥5 for medium-confidence}
  ghostwritten_set_count: {N — must be ≥5}
```

If reference set <10 OR ghostwritten set <5, audit confidence is medium — flag the report.

### Step 2 — Cosine similarity computation

Vector-embed both sets (reference + ghostwritten). Compute pairwise cosine similarity:

```
similarity = mean( cos(reference_post_i, ghostwritten_post_j) for all i in N_ref, j in N_ghost )
```

Acceptable embedding methods:
- Workspace's local embedding model (preferred, no PII leaks)
- OpenAI / Anthropic embedding API (acceptable if client has consented to API processing)

**Output:** `similarity_score` ∈ [0, 1]. The headline number.

### Step 3 — Per-axis voice analysis

A single number isn't enough — drift can be uniform OR concentrated on a specific axis. Analyze 5 axes:

| Axis | What it measures | Method | Pass threshold |
|---|---|---|---|
| Signature-phrase usage | Does the ghostwriter use the client's known phrases at the same rate as reference? | Frequency match against profile's `signature_phrases` list | ≥70% of reference frequency |
| Sentence-structure variance | Are sentence lengths + structures (declarative / interrogative / fragment) within client's range? | Distribution comparison | KL-divergence ≤0.15 |
| Vocabulary do/don't violations | Did the ghostwriter use any phrase from `phrases_to_avoid`? | Lexical scan | Zero violations |
| Decision-frame consistency | Does the post argue with the client's known decision-frame? (e.g., client always frames B2B SaaS through unit-economics; ghostwritten post frames it through brand-marketing — drift) | Pattern match against profile's `decision_frames` | Drift = >2 frame violations in 5 posts |
| Story-shape consistency | Do narrative posts follow the client's preferred shape (build-up vs. payoff-first vs. anecdote-then-frame)? | Pattern match against profile's `story_shapes` | Drift = >2 shape violations in 5 posts |

Per-axis output: pass / soft-fail / hard-fail.

### Step 4 — Threshold check

```
if similarity_score >= voice_drift_threshold AND zero hard-fail axes:
    audit_result: PASS
elif similarity_score < voice_drift_threshold OR ≥2 soft-fail axes:
    audit_result: SOFT_DRIFT
elif ≥1 hard-fail axis OR similarity_score < (voice_drift_threshold - 0.10):
    audit_result: HARD_DRIFT
```

Hard-drift halts ghostwriting per N-2 until remediation completes.

### Step 5 — Drift root-cause hypothesis

Map the audit pattern to one of 4 root causes:

| Pattern | Likely cause | Remediation |
|---|---|---|
| Signature-phrase rate dropped + sentence-structure unchanged | Ghostwriter using template defaults; voice profile not loaded properly | Recalibrate ghostwriter against last 10 reference posts; verify recursive scope loaded correctly |
| All axes degraded uniformly | Voice profile gone stale (client's actual voice has shifted but profile not updated) | Book re-extraction interview within 7 days; pause new ghostwriting until profile refresh |
| Vocabulary violations + decision-frame violations | Ghostwriter pulled from agency-voice OR another client's voice | Recursive scope error per N-1; halt + audit which voice was loaded; rerun |
| Single-axis hard-fail, others pass | Sample-size noise OR a specific post triggered detection (e.g., one outlier post) | Re-audit in 7 days with fresh ghostwritten batch; if persists, escalate |

### Step 6 — Recommended action

Per audit result + root cause:

| Result | Default action | Operator override allowed? |
|---|---|---|
| PASS | Note in audit log; continue ghostwriting; no client-facing communication | N/A |
| SOFT_DRIFT | Halt next 3 posts pending recalibration; AM messages ghostwriter with reference set; re-audit in 7 days | Operator can override to "continue with monitoring" if root cause is sample-size noise |
| HARD_DRIFT | Halt ALL new ghostwriting for this client; book re-extraction interview within 5 days; remove last 5 ghostwritten posts from approval queue if not yet published | Operator approval required to override (rare — would require operator-confirmed false positive) |

### Step 7 — Brief generation

The 1-page brief the AM and operator read Monday morning:

```markdown
# Voice Drift Audit — {Client Slug} — Week of {date}

**Result:** PASS / SOFT_DRIFT / HARD_DRIFT
**Similarity score:** {0-1} (threshold: {0.78-0.85})
**Audit confidence:** HIGH / MEDIUM (medium if reference set <10 or ghostwritten set <5)

## Per-axis breakdown

| Axis | Result | Notes |
|---|---|---|
| Signature-phrase usage | pass / soft / hard | {observation} |
| Sentence-structure variance | pass / soft / hard | {observation} |
| Vocabulary do/don't | pass / soft / hard | {observation, including any specific violations} |
| Decision-frame consistency | pass / soft / hard | {observation} |
| Story-shape consistency | pass / soft / hard | {observation} |

## Root-cause hypothesis (if drift)

{1 of 4 patterns from Step 5} — {evidence}.

## Recommended action

{Per Step 6 default} — {operator override required: yes/no}.

## Posts in queue affected

{N posts paused / removed} pending action.

## Next audit

{date + 7 days}.
```

### Step 8 — Output emission + state-machine integration

Output paths:
- The brief: `outputs/{date}/voice-drift-audits/{client_slug}-{week}.md`
- The audit YAML record: `outputs/{date}/voice-drift-audits/{client_slug}-{week}.yaml` (machine-readable for trend analysis)
- If HARD_DRIFT: triggers `/extract-founder-voice` re-booking via the FSM, halts queue in `post-approval-tracker`

Trend tracking: rolling 12-week similarity-score chart per client. Sustained downward trend (3+ weeks declining) = treat as drift even if individual audits pass.

## Verification checklist

1. Recursive scope correct: only client's voice profile loaded — never agency-voice or another client's voice (per N-1)
2. Reference set ≥10 posts (or HIGH confidence flag dropped)
3. Ghostwritten set ≥5 posts within last 30 days
4. Similarity score computed and recorded
5. All 5 axes analyzed and recorded
6. Threshold check applied per client's specific `voice_drift_threshold` (not workspace default)
7. Root-cause hypothesis assigned if drift
8. Recommended action set and surfaced to AM by Monday 11:00 local
9. State-machine integration: HARD_DRIFT halts approval queue
10. Blind Output Test mention: drift-passing posts still must pass BOT before publish (per A-6) — this audit does NOT replace BOT

## Common failure modes

| Failure | Likely cause | Fix |
|---|---|---|
| False positive (audit flags drift, client + operator say posts sound right) | Reference set contains posts client doesn't actually love; threshold too tight | Audit the reference set with client; tune threshold downward 0.02 |
| False negative (audit passes, client says "this doesn't sound like me") | Reference set too narrow; profile thin; embedding method missing nuance | Expand reference set to 25+ posts; rerun voice-extraction; consider second embedding method for cross-check |
| Audit produces inconsistent results week-to-week | Ghostwritten sample too small; high variance | Increase audit window to 14 days; require ≥10 ghostwritten posts in window |
| Drift detected but root cause unclear | All 4 patterns plausible | Run 1-week diagnostic: hold ghostwriter constant, check if drift persists across new batch; if yes → profile stale; if no → ghostwriter calibration |
| HARD_DRIFT triggered on a single outlier post | Single post mismatched but rest of batch fine | Operator override allowed with explicit reason logged; re-audit in 7 days |

## Cross-skill routing

- **Upstream:** `/onboard-client` Step 3 sets the threshold; `/extract-founder-voice` produces the profile this audit measures against
- **Sibling:** `/post-approval-tracker` reads the audit result before allowing posts through; `/ghostwrite-client-post` Gate 7c runs a pre-publish drift check that uses the same scoring fabric
- **Downstream on drift:** Re-runs `/extract-founder-voice` for profile refresh; halts `/ghostwrite-client-post` until cleared; updates `client.{slug}.voice_drift_threshold` if root cause is profile-stale
- **Reports into:** `/build-client-report` monthly drift trend section; `/pipeline-pulse` Layer 3 (encoding bottleneck audit)

## Cross-references

- `agents/account-manager.md` (Cycle 2 — TBD) — runs the audit
- `agents/fulfillment-head.md` — escalation owner if drift unresolved 2+ weeks
- `INVARIANTS.md` N-1 (no client ghostwriting without voice profile), N-2 (mandatory voice-drift check), A-6 (operator review for client-bound)
- `ENCODING.md` — `brand_voice.client_voices[{client_slug}]` schema
- `spec/BLIND-OUTPUT-TEST.md` — drift audit ≠ BOT; both required
- `skills/extract-founder-voice/SKILL.md` — profile source
- `skills/ghostwrite-client-post/SKILL.md` — Gate 7c uses this audit's scoring fabric
- `skills/post-approval-tracker/SKILL.md` — reads audit result before approval flow
- `skills/build-client-report/SKILL.md` — monthly drift trend section
- `paperclip.manifest.yaml` — Monday 09:00 cron trigger

---

*Skill version 1.0.0 — 2026-05-03*
