---
template_name: va-daily-dm-playbook
used_by_skill: /run-client-dm-ops
audience: virtual-assistant
runtime_target: full workday (09:00 — 17:00 client local time)
output_artifact: today's DM ops cycle log + end-of-day metrics emission
---

# VA Daily DM Operations Playbook

## Purpose

Step-by-step instruction set for the virtual assistant running `/run-client-dm-ops` for a single client across a single workday. Codifies what the VA does each hour from morning intake to end-of-day metrics, where to escalate, what to never do. The playbook exists so a senior VA produces the same DM ops output a senior operator would produce manually — minus the operator's bottleneck — without operator interruption except where `INVARIANTS.md` demands it.

The playbook assumes one client per VA per day. If the VA covers multiple clients, run this cycle once per `client_slug` in series (not parallel — context-switching mid-step is a known cause of mis-classification and rate-limit flagging).

## When the operator/AM/VA uses it

- Daily — VA opens this playbook at 09:00 client-local-time and follows the timeline.
- Make-up cycle — when the VA was sick or out, the operator runs the playbook in compressed form (not 8 hours; 2-3 hours of priority steps only).
- Backfill — when 2-3 days were missed, the playbook runs once per missed day, capped at 3 days of backfill per `/run-client-dm-ops` (more triggers spam-pattern flags on LinkedIn).
- New-VA training — used as the trainable runbook; new VA shadows for 5 days before running solo.

## The template

### 09:00 — Morning intake (30 minutes)

**What to do:**

1. Open today's send list at `outputs/{date}/dm-ops-cycles/{client_slug}-{date}.md` (produced by `/run-client-dm-ops` overnight cron at 06:00 per `paperclip.manifest.yaml`).
2. Verify the lead count matches the daily target for this client: `{daily_target_dms}` per the client's outbound engine spec.
3. Verify the class distribution: confirm split across Class C (cold), Class W (warm), Class L (liked-post), Class M (comment-magnet) matches the spec. Anomalies (e.g., 100% Class C when spec says 60/30/5/5) → ping AM in `{escalation_channel}` before sending anything.
4. Open the qualification-thread tracker at `outputs/{date}/qualifying-threads/{client_slug}.md`. Note how many threads need follow-up today.
5. Open the application-gate tracker at `outputs/{date}/application-gates/{client_slug}.md`. Note any prospects who applied overnight.
6. Open the operator-review queue at `outputs/{date}/operator-review-queue/{client_slug}.md`. Note any pending decisions from yesterday — these block today's threads in those states.
7. Open LinkedIn in the client's account `{linkedin_account_handle}`. Confirm no warning banners, no rate-limit flags, no MFA prompts pending. If anything red → STOP, ping AM, do not send.

**Output of this step:** internal note in `outputs/{date}/dm-ops-cycles/{client_slug}-va-log.md` confirming counts + any anomalies flagged.

---

### 09:30 — Yesterday's reply triage (60 minutes)

**What to do:**

1. Open yesterday's `/classify-dm-intent` results at `outputs/{yesterday}/intent-classifications/{client_slug}/`.
2. Sort by `confidence` — handle high-confidence (≥0.85) classifications first, low-confidence (<0.85) last.
3. For each high-confidence classification:
   - Confirm the recommended next-action is sensible against the prospect's full thread context.
   - Execute the action: send the next message in the qualification thread, route to ApplicationGate, route to OperatorReview, or archive per FSM.
4. For each low-confidence classification:
   - Re-read the prospect's reply manually.
   - If the recommended action still looks right → execute.
   - If wrong → flag in `outputs/{date}/dm-ops-cycles/{client_slug}-va-log.md` with the corrected classification + reason. The flag becomes input to the next `/classify-dm-intent` recalibration cycle (per the skill's ≥85% accuracy bar).
5. Hard-DQ signals → SKIP execution. Route directly to OperatorReview queue per Step 16:30 below. Per `INVARIANTS.md` A-13: never auto-archive a hard-DQ.
6. New replies that came in overnight that the classifier hasn't yet processed → trigger `/classify-dm-intent` manually for each, then route per the result.

**Time budget per reply:** 2-3 minutes for high-confidence, 5-7 minutes for low-confidence. If the queue is >25 replies, the VA is behind — escalate to AM for triage support.

**Output of this step:** every reply has a state-transition logged. OperatorReview queue updated. Qualification-thread tracker updated.

---

### 10:30 — M1 sends, batch 1 (90 minutes)

**What to do:**

1. Open the send list. Send the first half of today's M1 batch (typically 12-25 sends depending on `{daily_target_dms}`).
2. **CRITICAL: send manually.** Open LinkedIn, navigate to the prospect's profile, click message, paste M1, send. **No automation tools, no LinkedIn-extension auto-senders, no Sales Navigator bulk-message hacks.** LinkedIn TOS violation = client account suspension = client churn.
3. **Pace: 1 send every 60-90 seconds.** Faster triggers LinkedIn's rate-limit heuristics. Set a timer if needed. Watching the clock between sends is part of the job.
4. Per send:
   - Verify the personalization slots in the M1 are filled (name, company, the personalization hook). A raw `{first_name}` template-token in a sent message is a fireable mistake — if you see one, halt the batch and flag the engine spec to AM.
   - Confirm the message renders correctly (line breaks intact, no broken formatting).
   - After send, update the prospect's status in `outputs/{date}/dm-ops-cycles/{client_slug}-{date}.md` from `pending-send` to `sent` with the timestamp.
5. If LinkedIn shows a "you've reached your weekly limit" warning at any point during the batch → STOP, ping AM, do not send another DM today on this account.
6. If the batch finishes early (<90 minutes) — do NOT start batch 2 here. Use the spare time to advance qualifying threads (jump to Step 13:00 early).

**Banned during this window:** multitasking, mass-tab work, switching between client accounts mid-batch. Single-client focus prevents misroutes (sending Client A's message from Client B's account is the most catastrophic DM ops error and has happened to every multi-account agency at least once).

**Output of this step:** N sends logged with timestamps. Pacing confirmed compliant. No rate-limit flags triggered.

---

### 12:00 — Lunch + reply check (60 minutes)

**What to do:**

1. Take lunch. The 60-90 sec pacing rule means the morning batch was attention-heavy — VA needs the break to maintain decision quality on afternoon work.
2. Before lunch ends (last 10 minutes), do a quick reply scan: open LinkedIn inbox for the client account. Anything urgent?
3. **Urgent = same-day-response required.** Examples:
   - A prospect explicitly asks "can we hop on a call this week?" — needs the application-gate routing within 2-4 hours per `/run-client-dm-ops` Step 6.
   - A prospect complains, expresses frustration, or asks for the operator by name — needs response within 2 hours, often via routing to operator.
   - A current-client (not prospect — a paying client whose account this is) sends an internal message — needs immediate forward to AM.
4. Anything urgent → handle now, don't wait for the 13:00 step. Anything non-urgent → leaves for the 13:00 step.

**Output of this step:** any urgent replies handled. Inbox scanned. Qualification-thread tracker updated for any urgent state-changes.

---

### 13:00 — Qualification thread continuations (120 minutes)

**What to do:**

1. Open the qualification-thread tracker. Sort by `prospect_last_replied_at` — oldest first (response speed matters; per `/run-client-dm-ops` Step 5, qualifying-state SLA is <4h response when prospect replies).
2. For each active thread:
   - Pull the qualification thread spec from the client's outbound engine at `company.yaml::client.outbound_engine_spec.qualification_thread`.
   - Identify which qualification axis is being explored on this thread (typically: budget, timeline, decision-authority, fit-for-mechanism).
   - Read the prospect's last reply.
   - Pull the next message from the qualification thread spec, fill personalization slots based on the prospect's reply.
   - Run a quick banned-vocab sweep against `spec/BANNED-VOCABULARY.md` + the client's `phrases_to_avoid`. Zero hits before sending.
   - Send manually. Same pacing rule: 1 every 60-90 sec.
   - Update thread state in the tracker.
3. If a thread's qualification reveals soft-fit or high-fit per `/classify-dm-intent` outputs → flag for ApplicationGate routing in Step 16:00.
4. If a thread's qualification reveals hard-DQ → flag for OperatorReview in Step 16:30. Do NOT continue the thread.
5. If a thread has not received a prospect reply in 7+ days → mark dormant, do not send a 4th nudge. The qualification thread spec caps at 3 nudges per the engine.

**Time budget:** ~10 min per active thread. If >12 active threads need work, escalate — either operator approves overflow into tomorrow, or AM approves temporary daily target reduction to clear the backlog.

**Output of this step:** every active qualifying thread advanced (or marked dormant / hard-DQ / soft-fit / high-fit).

---

### 15:00 — M1 sends, batch 2 (60 minutes)

**What to do:**

1. Same protocol as Step 10:30 (batch 1) — pace 1 every 60-90 sec, manual sends only, verify personalization slots, update tracker.
2. Send the second half of today's M1 batch.
3. Why split into two batches: spreading sends across morning + afternoon (instead of one 25-send block) reduces the pattern-uniformity LinkedIn's heuristics flag. Same daily volume, lower flag risk.
4. If the morning batch hit a rate-limit warning, the afternoon batch is BLOCKED — do not send. Confirm with AM before resuming tomorrow.

**Output of this step:** remaining N sends logged. Daily send target met (or shortfall flagged with reason).

---

### 16:00 — Application-gate routing (30 minutes)

**What to do:**

1. From the qualifying-thread work in Step 13:00 + any urgent intake from Step 12:00, pull every prospect flagged as soft-fit or high-fit awaiting ApplicationGate routing.
2. For each:
   - Generate the application-gate message using the client's spec (typically: 2-3 sentences confirming you'd like to talk + the application URL with personalized framing).
   - Verify the application URL is the correct one for this client (not a stale link, not a wrong-client link).
   - **Per `INVARIANTS.md` N-12: the message contains the application URL, NEVER a calendar link.** Even if the prospect explicitly asks for "your calendar" — the response is "happy to set that up after a 2-minute application: {URL}." No exceptions without operator override (and overrides log to `outputs/{date}/n-12-overrides.md`).
   - Send manually. Same pacing.
   - Log the application-gate send in `outputs/{date}/application-gates/{client_slug}.md` with the prospect identifier + timestamp.
3. Verify yesterday's application-gate sends: did the prospect submit? If yes → already routed by `/triage-application` overnight. If no → on day-3 of waiting, send the gentle follow-up template; on day-7, archive per FSM.

**Critical:** the calendar leak (sending a calendar link without an application) is the single most common N-12 violation across VA cycles. If you catch yourself about to paste a calendar link, STOP. Pull the application URL.

**Output of this step:** every soft-fit / high-fit prospect has either received the application-gate message today, has a follow-up scheduled, or has been archived.

---

### 16:30 — Hard-DQ surfacing to OperatorReview (15 minutes)

**What to do:**

1. From the work today (reply triage at 09:30, qualification continuations at 13:00, intake at 12:00), pull every prospect flagged as hard-DQ.
2. For each:
   - Append to `outputs/{date}/operator-review-queue/{client_slug}.md` with: prospect identifier (descriptor only — never real names per `INVARIANTS.md` A-11), the thread context (last 3 messages), the hard-DQ trigger (which DQ criterion was tripped), the recommended action (confirm-DQ-and-archive vs. operator-may-want-to-override).
   - Tag the queue entry with `urgency: standard` unless the prospect has explicitly asked for a same-day decision, in which case `urgency: same-day`.
3. **Per `INVARIANTS.md` A-13: never auto-archive a hard-DQ trip.** Operator decides. Even if the trip looks unambiguous (e.g., "we don't have budget"), operator gets the call. Founder-led DQ stays a founder call, not an algorithm call.
4. Notify the operator via `{operator_notification_channel}` if same-day-urgency hard-DQs were added today.

**Output of this step:** OperatorReview queue updated. Operator notified if urgent.

---

### 17:00 — End-of-day metrics emission (30 minutes)

**What to do:**

1. Tally the day's activity per `/run-client-dm-ops` Step 9 spec:

```yaml
date: {date}
client: {client_slug}
va_handle: {va_descriptor}
metrics:
  dms_sent_today: N
  dms_sent_by_class:
    C: N
    W: N
    L: N
    M: N
  replies_received_24h: N
  reply_rate_24h: %
  qualifying_threads_advanced: N
  applications_submitted: N
  applications_triaged:
    Booked: N
    AppDQ: N
    AppOperatorReview: N
  hard_dqs_surfaced: N
  no_shows_recovered: N (rebooked) | N (archived)
  operator_interrupts_required: N
  operator_interrupt_rate_pct: % (target <5%)
  rate_limit_warnings: N (target 0)
  n12_overrides: N (target 0)
  banned_vocab_hits_caught: N
flags_for_operator:
  - {any anomaly the operator should see}
notes:
  - {any context worth preserving for tomorrow's cycle}
```

2. Write to `outputs/{date}/dm-ops-cycles/{client_slug}-eod-metrics.md`.
3. If `operator_interrupts_required` is >5% of total decisions today — this is a signal the workspace's encoding has a leak per `/run-client-dm-ops` Step 9. Flag in the EOD metrics with a 1-2 sentence note on which decision types kept needing operator input.
4. Confirm tomorrow's send list has been generated overnight (cron at 06:00). If not — ping AM tonight, not tomorrow morning.
5. Close LinkedIn, log out of the client account, end-of-day.

**Output of this step:** metrics emitted, file logged at the canonical path. Day complete.

---

## Escalation triggers (when to ping AM/operator mid-day)

Stop the cycle and escalate immediately if any of these fire — do not "push through" or wait for the next checkpoint:

- LinkedIn rate-limit warning OR account warning banner shown at any time
- A current paying client (not prospect) sends an internal message that needs operator attention
- A prospect threatens legal action, escalates a complaint, or asks for the operator by name with urgency
- A hard-DQ trip with `urgency: same-day` added to OperatorReview
- The send list for today has wrong client_slug data (cross-client leak)
- The qualification-thread spec or application URL appears stale or wrong
- More than 25 unprocessed replies in the morning queue (overflow signal)
- Any moment the VA is unsure whether a decision is the VA's call or the operator's call — escalate by default; per the encoding-leak signal, repeated escalations on the same decision type drive a `/build-outbound-engine` refresh

Escalation channel: `{escalation_channel — e.g. dedicated Slack DM, dedicated WhatsApp, dedicated email}`. Response SLA from AM: <2 hours during client-local business hours.

## Common mistakes to avoid

| Mistake | Why it matters | Prevent it by |
|---|---|---|
| Using LinkedIn automation tools (Phantom-Buster, Dux-Soup, browser auto-clickers) | TOS violation → client account suspension → client churn | Manual sends only. Always. The 60-90 sec pacing isn't a productivity bottleneck; it IS the product. |
| Sending the calendar link instead of the application URL | N-12 violation. Application is the qualification thread; calendar without it = vibe-call = collapsed close rate | At Step 16:00, before pasting, ask "is this the application URL?" Every time. |
| Sending a generic personalization (e.g., "hey {first_name}, saw your profile") | Reads as automation = blocked = wasted highest-fit prospect | Verify slots filled at every send. A raw `{slot}` token = halt the batch. |
| Skipping `/classify-dm-intent` on a new reply because it "looks obvious" | Misroutes happen most on the replies that look obvious. The classifier catches what intuition misses | Every new reply runs through the classifier. No exceptions. |
| Auto-archiving a hard-DQ ("they're clearly not a fit") | A-13 violation. Operator decides on hard-DQs, not the VA, not the algorithm | Hard-DQ = OperatorReview queue. Always. Even if it seems unambiguous. |
| Sending faster than 60-90 sec per DM | LinkedIn rate-limit pattern flag → account warning → restricted account → client churn | Use a timer. Set it on the desk. Watch the clock. The pace is the safety. |
| Multi-client context-switching mid-batch | Cross-client leak (Client A's message from Client B's account) is catastrophic and unrecoverable | One client per session. Finish the batch, log out, log into the next client. |
| Letting the OperatorReview queue grow unprocessed for >48h | Hard-DQ prospects sitting in queue too long = lost trust + signal that the operator-loop is broken | Daily ping to operator if queue is non-empty. Escalate if queue grows 2+ days running. |
| Filing operator paraphrase as VOC quote | Per `voice-of-customer-extraction.md` Rule 2: paraphrase ≠ VOC. Bank pollution drives downstream content-fail | If the operator says "they tell me X," it's paraphrase. Quotes go in the `pending-verification` holding queue, not the bank. |
| Skipping the EOD metrics emission "because nothing changed" | The metrics file is the audit trail. Missing days = no compounding signal for `/build-client-report` | Emit even on slow days. Empty metrics with `0`s is the correct output, not a missing file. |

## Quality bar

- Daily send target met (or shortfall flagged with reason in EOD metrics).
- Zero LinkedIn rate-limit warnings triggered (>0 = the day's pacing failed).
- Zero N-12 violations (calendar without application).
- Zero A-13 violations (auto-archived hard-DQ).
- Operator-interrupt rate <5% of decisions today.
- Every new reply ran through `/classify-dm-intent` before action.
- OperatorReview queue updated with all hard-DQs from the day, descriptors-only per A-11.
- EOD metrics file emitted to canonical path by 17:30 client local time.
- Tomorrow's send list confirmed to exist (cron success) — if missing, AM is pinged tonight not tomorrow.

## Cross-references

- `skills/run-client-dm-ops/SKILL.md` — the parent skill this playbook implements at the human-execution layer
- `skills/classify-dm-intent/SKILL.md` — runs on every inbound reply during Step 09:30
- `skills/build-qualification-thread/SKILL.md` — the spec consumed during Step 13:00
- `skills/build-application/SKILL.md` — the application form gating Step 16:00
- `skills/triage-application/SKILL.md` — handles applications submitted overnight
- `skills/no-show-recovery/SKILL.md` — runs alongside the daily cycle for booked-then-no-showed prospects
- `INVARIANTS.md` N-5 (intent classification mandatory), N-12 (application before calendar), A-11 (descriptors only), A-13 (operator on hard-DQ)
- `spec/BANNED-VOCABULARY.md` — the sweep run against every send
- `workflows/divisions/acquisition.md` — the FSM the VA's state-transitions hydrate
- `paperclip.manifest.yaml` — the daily 06:00 cron that produces today's send list
