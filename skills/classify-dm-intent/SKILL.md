---
name: classify-dm-intent
slug: /classify-dm-intent
agent: dm-strategist
department: acquisition
output_format: intent-classification
required_profiles: []                      # runtime classifier — no profile gate, runs per-thread on demand
allowed_scopes:
  - single-thread                          # one inbound DM thread → one classification
  - batch                                  # N inbound threads → N classifications (queue-mode)
output_path:
  default: outputs/{date}/intent-classifications/{thread-id}.md
---

# /classify-dm-intent — Runtime Intent Classifier

> **Purpose.** Auto-classify an inbound DM thread (or queue of threads) into the correct acquisition-FSM state. Drives every routing decision after a prospect engages: which sequence to send, which qualification thread to run, when to surface to operator review, when to gate to application, when to archive. Wrong classification = wrong sequence = lost prospect.
>
> **Bar.** ≥85% agreement with operator manual classification on a 50-thread blind-test sample. Below 85%, signals + thresholds need recalibration. Misrouting a high-fit prospect into the cold sequence costs the close.
>
> **Why it's runtime.** The four intent classes from `reference/frameworks/dm/intent-taxonomy.md` are the *outbound* primitive. This skill produces the *full FSM-state* classification for inbound — 13 states a prospect could be in. Each routes to a different downstream action.

## When to invoke

- Every inbound DM reply (webhook trigger: `dm.replied` per `paperclip.manifest.yaml`)
- Every prospect added to outreach queue (pre-classification before sequence assignment)
- Every application submission (route via `/triage-application`, but classify the source thread context)
- Batch mode: nightly sweep of inbox to catch missed-reply states + reclassify decayed-class prospects
- Operator-triggered: "what state is this prospect in?" advisory query
- Before invoking `/write-dm-sequence` for a new prospect — confirms which class to write for

## Required inputs

1. **The inbound message text** (verbatim, no paraphrase).
2. **Thread history** — every prior message with timestamps and sender (operator/VA/prospect).
3. **Prospect metadata** — at minimum:
   - Source: cold-sequence M1, warm DM, liked-post, comment-magnet, inbound-cold via profile-view, referral
   - Prior touches: count + nature (post engagements, profile views, comment activity)
   - ICP-fit score: cosine similarity to ICP profile (0-1)
   - Current FSM state (if previously classified)
4. **The acquisition FSM definition** at `workflows/divisions/acquisition.md` — for valid state transitions.
5. **The DQ criteria set** at `product_strategy.service_tier.offers[*].target_avatar` + `case_studies.agency_case_studies` — for hard-DQ trigger detection.
6. **The ICP profile** — uses `voice_of_customer.objection_patterns`, `behavioral_patterns.buying_triggers`, `firmographics`.

If any required input is missing → return classification with `confidence: LOW` and route to operator review queue.

## The 13 classification states (mapped to acquisition FSM)

Per `workflows/divisions/acquisition.md`:

| State | Definition | Downstream route |
|---|---|---|
| **New** | Identified, not yet contacted | → assign sequence by intent class via `/write-dm-sequence` |
| **Replied-cold** | First reply to a class-C cold sequence | → Qualifying state, run `/build-qualification-thread` |
| **Replied-warm** | First reply to a class-W warm sequence | → Qualifying state (light qualification — class W earned signal) |
| **Replied-liked-post** | First reply to a class-L sequence | → Qualifying state, fast-track to call invite if signal clear |
| **Replied-comment-magnet** | First reply to class-M (post-magnet, post-consumption-check) | → Qualifying state, qualification AFTER consumption confirmed |
| **Qualifying** | Qualification thread M2-M3 of post-reply active | → SoftFit / HighFit / HardDQ on next reply |
| **Soft-fit** | Some qualifiers passed, some uncertain | → ApplicationGate (per N-12, application required) |
| **High-fit** | All qualifiers passed | → ApplicationGate (still required — no calendar without app) |
| **Hard-DQ** | Tripped a hard-DQ in qualification thread | → OperatorReview (per A-13 — never auto-archive) |
| **Booked** | Calendar booked, call upcoming | → CallPrepBuilt via `/build-call-prep` |
| **NoShow** | Prospect didn't attend booked call | → NoShowRecovery via `/no-show-recovery` |
| **Closed** | Contract signed | → ClientOnboarding via `/onboard-client` |
| **NotAFit** | Mutual fit-not-found decision (post-call) | → archive with note |

## The build sequence

### Step 1 — Thread ingestion (the inbound message + thread history + prospect metadata) (3 min)

Load the thread context. Classification is a function of THE FULL THREAD, not just the latest message. A reply to a class-M consumption check has different meaning than a reply to a class-C value-give.

```yaml
ingestion:
  thread_id: "<unique-id>"
  inbound_message:
    sender: prospect
    timestamp: <ISO-8601>
    body: "<verbatim>"
    medium: linkedin-dm | linkedin-inmail | manychat-relay
  thread_history:
    - sender: operator | va | prospect
      timestamp: <ISO-8601>
      body: "<verbatim>"
      message_type: <opener | qualifier | value-give | call-invite | bow-out | reply>
      sequence_class: cold | warm | liked-post | comment-magnet | post-reply
      sequence_position: M1-M7 | post-reply-Qx
  prospect_metadata:
    source: cold-list | warm-engagement | liked-post-trigger | comment-magnet-trigger | inbound-cold | referral
    prior_touches: <int>
    icp_fit_score: 0.0-1.0
    current_fsm_state: <one of 13, or "Unclassified">
    prior_classifications: [<list with timestamps>]
```

### Step 2 — Source classification (where did this prospect come from?) (4 min)

Source determines the *base class*. Current message + thread state determines the *current FSM state*.

Decision tree extending `intent-taxonomy.md` for inbound:

```
Is the inbound a reply to a sequence message?
├── YES ↓
│   What was the source class?
│   ├── Cold (C) → base class C, transitioning to Replied-cold
│   ├── Warm (W) → base class W, transitioning to Replied-warm
│   ├── Liked-post (L) → base class L, transitioning to Replied-liked-post
│   └── Comment-magnet (M) → base class M, transitioning to Replied-comment-magnet
│
├── NO (no prior outbound) ↓
│   Is this prospect's first message ever?
│   ├── YES → state: New (inbound-cold). Route: assign inbound-acknowledgment + qualification thread.
│   ├── NO ↓
│   │   Is prospect already in a downstream FSM state?
│   │   ├── Booked → message is pre-call inbound (reschedule? cancel? confirm?)
│   │   ├── NoShow → message is post-no-show (re-engaged? excuse? rebook?)
│   │   ├── Closed → route to fulfillment (in client communication, not acquisition)
│   │   └── Other → re-classify based on FSM transition rules
│   └──
└──
```

Edge cases: Multi-source ambiguity (cold list AND liked recent post) → highest-conversion class (L > W > C). Stale thread re-engagement (60+d silent) → re-classify from current message + fresh signal. Cross-account confusion → classify per the account this thread is on.

### Step 3 — Intent signal extraction (4 buckets) (10 min)

From inbound + thread history, extract signals across four buckets. Each signal increments confidence.

**Bucket A — Explicit-interest (positive):** direct asks ("what does it cost?", "send the link", "what's the next step?"); calendar / call language ("happy to chat", "send a slot"); trigger-event mentions ("we just raised", "our last agency churned"); specific offer language (mentions a feature, deliverable, guarantee); mechanism resonance ("the part about {mechanism} hit").

**Bucket B — Qualifying (mixed — informational):** revenue tier statement; role / decision authority ("I'm the founder", "I'd need to run by my partner"); timeline statement; budget tier signal; mechanism-fit signal ("we're already doing X", "we tried Y").

**Bucket C — Objection (mixed — surface for routing):** generic objection ("send me more info"); specific objection (preferred — easier to handle, e.g., "we tried agencies before and got burned"); pricing objection; timing objection; authority objection.

**Bucket D — DQ (negative — hard-DQ triggers):** hard-DQ criterion match (revenue below floor, no budget authority + no named decision-maker, wrong vertical, timeline >6mo with no urgency); brand-fit DQ (values contradict agency positioning); behavior DQ (hostile, extractive, demanding free work); spam-bot signature.

Output: per-bucket signal extraction with verbatim quotes. Each signal carries a weight; weighted sum drives next-state decision.

### Step 4 — State transition decision (per acquisition FSM) (5 min)

Apply FSM transition rules from `workflows/divisions/acquisition.md` to source class + extracted signals.

| Current state | Signal pattern | New state |
|---|---|---|
| InThread (any class) | First reply | Replied-{class} |
| Replied-{class} | Bucket A or B dominant, no D | Qualifying |
| Replied-{class} | Bucket D dominant | HardDQ |
| Qualifying | All qualifiers passed (Bucket B all positive, no D, ≥1 Bucket A) | High-fit |
| Qualifying | Some qualifiers passed (mixed Bucket B, no D) | Soft-fit |
| Qualifying | Bucket D triggers | HardDQ |
| Soft-fit / High-fit | App submitted | (handoff to `/triage-application`) |
| Soft-fit / High-fit | Ghost (7d+) | AppGhost |
| HardDQ | (always) | OperatorReview (per A-13 — never auto-archive) |
| Booked | Pre-call inbound | (route to operator if reschedule/cancel) |
| NoShow | Post-no-show re-engagement | NoShowRecovery |
| CallHeld outcomes | Per call summary | Closed / ProposalSent / CallObjection / NotAFit |

If signal pattern is ambiguous (mixed buckets, no clear dominance) → state: `Qualifying` + flag for operator review with confidence <70%. If inbound is a reply to comment-magnet M1 (the magnet itself) → state: `MagnetDelivered` (still pre-Replied — they have not engaged yet).

### Step 5 — Hard-DQ trigger check (per `INVARIANTS.md` A-13) (3 min)

If Step 3 surfaced any Bucket D signal:

```yaml
hard_dq_check:
  triggered: true | false
  triggers_matched:
    - dq_criterion: "<from offer's target_avatar or agency-DQ list>"
      evidence: "<verbatim from inbound>"
      severity: hard | soft
  routing_per_A13:
    decision: ROUTE_TO_OPERATOR_REVIEW
    rationale: "Per A-13, hard-DQ replies surface to operator. Founder-led DQ stays a founder call, not an algorithm call."
    operator_brief:
      thread_summary: "<2-sentence>"
      dq_evidence: "<verbatim quote>"
      recommended_action: "<confirm DQ + bow-out | override DQ + continue | flag for re-engage in 6mo>"
```

Hard-DQ NEVER auto-archives. Always OperatorReview state with brief.

Soft-DQ (e.g., timeline >6mo with no urgency, but otherwise high-fit) → SoftFit state with timeline flag, route to ApplicationGate with reactivation tag.

### Step 6 — Confidence scoring (0-100) (5 min)

Per A-8, classifier carries explicit confidence.

Confidence factors:
- **Signal density (40%):** ≥3 signals = HIGH; 1-2 = MEDIUM; 0 (e.g., one-word reply "yes") = LOW
- **Signal clarity (30%):** signals consistent (all pointing to same state) = HIGH; mixed = MEDIUM-LOW
- **Source clarity (20%):** single-source = HIGH; multi-source = MEDIUM
- **Prior-state coherence (10%):** valid FSM transition = HIGH; weird jump = LOW + flag

Score thresholds:
- **HIGH (85-100):** auto-classify, route to next state immediately
- **MEDIUM (70-84):** auto-classify, log to nightly review queue (operator spot-checks daily)
- **LOW (<70):** route to operator review queue immediately, do NOT auto-route

Cost asymmetry: wrong auto-classification at HIGH confidence costs less than wrong auto-classification at LOW confidence. LOW always escalates per A-13's spirit.

### Step 7 — Output emission (classification + state transition + confidence + recommended next action) (3 min)

Hydrate and write to `outputs/{date}/intent-classifications/{thread-id}.md`.

```yaml
classification:
  thread_id: "<id>"
  classified_at: <ISO-8601>
  classified_by: dm-strategist (auto) | operator (manual override)
  inbound_message_quote: "<verbatim, max 200 chars>"
  source_class: cold | warm | liked-post | comment-magnet | inbound-cold | referral
  prior_state: "<from FSM>"
  new_state: "<one of 13>"
  state_transition_valid: true | false
  signal_extraction:
    bucket_a_explicit_interest: [<verbatim signals>]
    bucket_b_qualifying: [<list>]
    bucket_c_objection: [<list>]
    bucket_d_dq: [<list>]
  hard_dq_triggered: true | false
  hard_dq_routing: null | OperatorReview-with-brief
  confidence_score: 0-100
  confidence_tier: HIGH | MEDIUM | LOW
  recommended_next_action:
    skill: "<which skill runs next>"
    target_state: "<next FSM state>"
    auto_execute: true | false
    operator_review_required: true | false
```

For batch mode, emit one artifact per thread + a batch-summary at `outputs/{date}/intent-classifications/_batch-{timestamp}.md`.

## Validation gate (mandatory before declaring done — runs on classifier itself, weekly)

Three checks calibrate the classifier monthly:

1. **Blind-test sample.** Pull 50 inbound threads from last 30 days. Operator classifies manually. Classifier classifies. Agreement ≥85% = pass. Below 85% = recalibrate signal weights or threshold rules.

2. **Hard-DQ false-negative check.** Of threads operator manually flagged as hard-DQ in last 30 days, what % did the classifier also flag? Target ≥95%. Below 95% = expand DQ-signal patterns in Step 3 Bucket D.

3. **State-transition coherence check.** Pull every classification from last 7 days. Is each new_state a valid FSM transition from prior_state per `workflows/divisions/acquisition.md`? Target 100%. Any incoherent transition = bug in Step 4 transition matrix.

## Output format

```yaml
---
artifact_type: intent-classification
thread_id: "<id>"
classified_at: <ISO-8601>
new_state: "<FSM state>"
confidence_score: 0-100
confidence_tier: HIGH | MEDIUM | LOW
hard_dq_triggered: true | false
operator_review_required: true | false
auto_routed: true | false
profiles_used: [ideal_customer_profile, product_strategy.service_tier]
frameworks_used: [intent-taxonomy, acquisition-fsm, dq-criteria]
---

{populated classification YAML from Step 7}

## Signal extraction detail
{per-bucket verbatim signal list with thread-position references}

## Recommended next action
{which skill runs next, what state the prospect moves to, whether operator review gate triggers}

## Operator brief (if hard-DQ or LOW confidence)
{2-sentence thread summary + DQ evidence + recommended decision options for operator}
```

## Failure modes and guards

- **One-word-reply ambiguity.** "yes" / "ok" / "sure" with no context → confidence LOW, surface to operator. Guard: confidence floor on signal density.
- **Hard-DQ auto-archived (NEVER).** Per A-13, hard-DQ ALWAYS routes to OperatorReview. Guard: Step 5 enforces `decision: ROUTE_TO_OPERATOR_REVIEW` non-negotiably when DQ signals trigger.
- **Wrong source class.** Multi-source prospect classified as Cold → wrong sequence after re-engagement. Guard: Step 2 multi-source rule prefers highest-conversion class (L > W > C).
- **Stale-state classification.** Booked 60d ago, never showed, re-engages now → still showing Booked. Guard: classifier checks prior-state freshness; states older than {state-specific TTL} get re-evaluated.
- **Spam-bot misclassification.** Spam pitch back classified as Replied → triggers qualification thread to a bot. Guard: Bucket D includes spam-bot-signature detection; matches → state: archive-as-spam.
- **Confidence inflation.** Returns HIGH on weak signal because rule scored generously. Guard: Step 6 uses signal density AS REQUIRED component (not optional); ≥3 signals required for HIGH.
- **No FSM-transition validity check.** Transition Closed → Replied-cold (impossible per FSM). Guard: Step 4 always checks against transition matrix; invalid = LOW confidence + operator review.
- **Inbound from existing client mistakenly classified as prospect.** Signed client's DM routed to acquisition. Guard: Step 2 source classification checks Closed state → routes to fulfillment.

## Cross-references

- `reference/frameworks/dm/intent-taxonomy.md` — the four base classes the classifier extends with FSM states
- `workflows/divisions/acquisition.md` — the 13-state FSM this skill drives transitions for
- `INVARIANTS.md` N-5 (intent classification before DM ops), A-8 (confidence + gap flags), A-13 (operator-in-loop on hard-DQ)
- `agents/acquisition-head.md` — owner of acquisition pipeline; consumes classifier output for routing
- `skills/build-outbound-engine/SKILL.md` — the engine spec defining source classes
- `skills/write-dm-sequence/SKILL.md` — sequences this skill routes prospects toward
- `skills/build-qualification-thread/SKILL.md` — runs after Replied → Qualifying transition
- `skills/build-application/SKILL.md` — runs after Soft-fit / High-fit → ApplicationGate
- `skills/triage-application/SKILL.md` — runs after ApplicationSubmitted → AppTriaged
- `skills/build-call-prep/SKILL.md` — runs after Booked → CallPrepBuilt
- `skills/no-show-recovery/SKILL.md` — runs on NoShow → NoShowRecovery
- `paperclip.manifest.yaml` — webhook (`dm.replied`) + cron (`dm.no-reply.7d`) triggers that fire this skill

## Examples

See `examples/`:
- `example-classification-cold-reply-positive.md` — cold M2 reply with strong Bucket A signals → Qualifying with HIGH confidence
- `example-classification-warm-reply-objection.md` — warm M1 reply with Bucket C objection → Qualifying with objection flagged
- `example-classification-comment-magnet-consumption.md` — class M M2 consumption-check reply with Bucket B qualifier → Qualifying
- `example-classification-hard-dq-route.md` — Bucket D dominant signals → HardDQ → OperatorReview with brief
- `example-classification-low-confidence-one-word.md` — "yes" with no thread context → LOW confidence → OperatorReview
- `example-batch-classification-nightly-sweep.md` — batch mode processing 40-thread overnight queue

---

*Skill version 1.0.0 — 2026-05-03*
