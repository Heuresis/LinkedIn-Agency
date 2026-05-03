# Qualification Thread Design

> The structural rules for the multi-message DM thread that runs after a positive reply, surfaces fit and dis-qualifiers conversationally, and routes the prospect to the application gate (or a graceful exit). Replaces the bad pattern of "send me a fit form" or "let's hop on a quick call."

## Why this framework exists

The single largest close-rate compressor in B2B services acquisition is treating the calendar link as the qualifier. "Book a time and we'll figure it out" produces 50% no-shows, 60% bad-fit calls, and a close rate that hovers at 12-15% no matter how good the operator is on the call. The math is downstream of the funnel: half the calendar slots are spent on prospects who would never have made it through a real qualification step, and the operator runs out of energy before the qualified ones land.

The qualification thread inverts this. It runs in DMs *after* a positive reply and *before* the application gate. It surfaces the 4-6 axes that predict fit (revenue, role-authority, urgency, mechanism-fit, DQ filters, budget tier) across 2-4 conversational messages. By the time the prospect reaches the application gate, both sides know it's worth filling. By the time they hit the calendar, both sides know it's worth holding. Per `INVARIANTS.md` N-12, no calendar without an application — the thread is the bridge between reply and application.

This framework is the design protocol for that bridge.

## The 7 rules

### Rule 1 — Conversational not interrogative (≤2 questions per message)

The thread reads as a 1:1 DM exchange between peers, not as a CRM intake form translated into chat. The hard rule: maximum 2 questions per message. Beyond 2, the message reads as a survey, trust drops, and the prospect's reply quality collapses (they batch-answer, skipping the parts that matter most).

Bad: "Got it — couple things to figure out fit. What's your revenue, who else is involved in the decision, what have you tried before, and what's your timeline to start?"

Good: "Quick context — closer to $1M ARR or $5M+? Just so I know where in the playbook we'd start." (1 question, conversational ranges, soft-DQ baked into the framing.)

The 2-question ceiling forces the thread to spread across 2-4 messages, which is the design intent. Each message is a small ask; cumulative depth comes from sequence, not density.

### Rule 2 — Surface dis-qualifiers as comfortably as qualifiers (the comfort principle)

A prospect who cannot afford the offer must feel safe saying so. A prospect at $400K ARR considering a $7K/mo retainer must feel safe saying "I'm too early." If the question framing makes the wrong answer feel like rejection, the prospect lies (says "yes, I'm a fit") to preserve face — and the operator burns a discovery call learning what the DM should have surfaced.

Comfort is engineered through three patterns: (1) ranges with a comfortable bottom ("$1M ARR or earlier?" not "Are you over $1M?"), (2) anchor-with-percentages framing ("about 70% of {role}s I work with are at $1.5M+ — is that you, or earlier stage?"), (3) explicit door-open language for the no answer ("totally hear you, early stage — if {trigger} happens, here's the reply that gets you to the front of the line").

Comfort is not softness. The thread still surfaces the disqualifier — it just makes the disqualifier emotionally cheap to admit.

### Rule 3 — Sequential axes: revenue → role-authority → urgency → mechanism-fit → DQ filters → budget tier

The thread surfaces 4-6 axes in this default order: A1 revenue tier, A2 role authority, A3 urgency / trigger event, A4 mechanism-fit, A5 hard-DQ filters (typically embedded), A6 budget tier. Order matters: revenue and authority are firmographic and least personal; urgency and mechanism are about the prospect's situation; budget is the heaviest lift and lands last when the prior context has built reciprocity.

Reversing the order (budget first, revenue last) produces the classic "are you serious?" reply pattern from prospects who haven't yet decided whether to invest emotional capital in the thread. Save the heaviest axis for the message where it matters — and where the prior context makes the answer meaningful.

Example axis 3 (urgency): "What made you start thinking about this now vs. 6 months ago? Anything specific in the last 30 days?" — surfaces trigger event without sounding like a sales question.

### Rule 4 — Each axis = 1-2 messages, not 1 message

Each axis is its own surface, not a bullet in a multi-axis question. A1 (revenue) gets its own conversational moment. A2 (authority) gets its own. The thread is structured so the axis-to-message mapping looks like: M1 surfaces A1 (and possibly A3 in a paired-warm framing); M2 surfaces A2 + A4; M3 surfaces A6 + transitions to application. A5 (hard-DQ) is woven through, not ghettoized.

If an axis needs a probe (the answer was ambiguous), the probe is its own message — never crammed into the next axis question. "Got it — and is that revenue growing, flat, or — total context — declining?" is a standalone probe, not a tag-along to the authority question.

This design produces a 3-message thread (default) or a 4-message thread (program-tier with peer-fit + cohort-timing axes). Threads shorter than 3 messages skip surface; threads longer than 4 messages exhaust the prospect.

### Rule 5 — Reply branching per axis (positive → next axis; soft → probe; DQ → soft-DQ branch OR hard-DQ → operator review per A-13)

Every question has three branches that determine what gets sent next: **pass** (axis surfaced cleanly, advance to next axis), **probe** (answer ambiguous, send a one-line clarifier before advancing), **fail** (axis tripped, route to soft-DQ branch OR hard-DQ branch).

Pass is automatic. Probe is a single follow-up question that closes ambiguity without re-opening the axis ("got it — and is that 'recently raised' or 'planning to raise in 6 months'?"). Fail is split: **soft-DQ** routes to a graceful-exit message with door-open language and a reactivation tag (early-stage → "if you cross $1M, DM 'WAIT' and I'll re-open the front of the line"); **hard-DQ** routes to OperatorReview per A-13, never to auto-archive.

The branching matrix is the runtime guide for the dm-strategist (or VA running the thread) — it tells them what to send next based on the prospect's reply, without requiring fresh judgment for every reply.

### Rule 6 — Application-gate transition (qualified routes to application URL, NOT direct calendar)

After the final axis surfaces and all qualifiers pass, the thread transitions the prospect to the application form — not to the calendar. Per N-12, this is non-negotiable. The transition message frames the application as a benefit, not as a hurdle: "because this is a 6-12 month commitment on both sides, I have a short app that helps me pre-build the call so we both get more out of the 30 min."

The framing matters. "Fill this out so we can decide if you qualify" pattern-matches a job application and kills conversion. "Most agencies skip this and waste your 30 min" frames the application as a quality signal. "5 min to fill, I'll review same-day, calendar opens after" sets expectations and reduces drop-off.

If a prospect responds to the application transition with "can't I just get on a call?" — the answer is no. That request is the highest-frequency leak; the thread template has a hard-coded reply: "I used to do that. The hit rate was bad for both of us. The app is fast — promise." Never break N-12 to satisfy a single prospect's friction.

### Rule 7 — Operator-escalation triggers (any hard-DQ, borderline cases, high-stakes prospects)

The thread escalates to the operator in three cases. **Hard-DQ trip** (any axis surfaces a hard-DQ pattern): routes to OperatorReview per A-13, never auto-archives. Operator confirms DQ, overrides and continues, or flags for re-engagement. SLA: <24h. **Borderline state** (`/classify-dm-intent` confidence <70 OR 2+ axes in probe state): routes to OperatorReview as advisory (not blocking) so operator can choose continue / nurture / manual call invite. SLA: <48h. **High-stakes prospect** (enterprise tier defined as revenue >10× offer floor, OR named-account from operator's pre-flagged list, OR referral from existing client): routes to operator from M1, regardless of qualification state — these prospects deserve operator-touch, never VA-driven flow. SLA: <12h.

Every escalation produces an operator brief: 2-sentence thread summary, trigger evidence, recommended decision options. Operators read briefs and decide; they don't re-read full threads.

## Thread spec template

```yaml
qualification_thread:
  scope: agency-qualification | client-qualification
  client_slug: null | "<slug>"
  funnels_to_offer: "<offer_slug>"
  funnels_to_application: "<application_form_path>"
  total_messages: 2-4
  axes:
    - { id: A1, name: revenue_tier, source: "offer.target_avatar.revenue_floor", pass_threshold, probe_threshold, fail_threshold }
    - { id: A2, name: role_authority, source: "icp.behavioral_patterns.decision_making_process", ... }
    - { id: A3, name: urgency_trigger, source: "icp.behavioral_patterns.buying_triggers", ... }
    - { id: A4, name: mechanism_fit, source: "offer.unique_mechanism", ... }
    - { id: A5, name: hard_dq_filters, source: "icp.disqualifier_set", ... }
    - { id: A6, name: budget_tier, source: "offer.price_point_monthly", ... }
  messages:
    - message_number: 1
      purpose: "acknowledge + warm transition + first axis"
      trigger: "Replied state from /classify-dm-intent"
      questions: [A1]
      template: |
        <draft message in operator voice, ≤2 questions, comfort-engineered framing>
      branches:
        A1_pass:  { action: continue, next_message: M2, tag: a1_passed }
        A1_probe: { action: send_probe, probe_text: "...", then: M2 if growing else soft_dq }
        A1_fail:  { action: route_soft_dq, soft_dq_message: "...", next_state: NotAFit, tag: a1_failed }
      voice_calibration_notes: ""
      expected_reply_window_hours: 24-72
    - message_number: 2
      purpose: "deeper qualification + mechanism fit"
      questions: [A2, A4]
      template: |
        <draft message>
      branches: { A2_pass/probe/fail, A4_pass/probe/fail }
    - message_number: 3
      purpose: "budget axis + transition to application"
      questions: [A6]
      template: |
        <draft message including application transition framing>
      branches: { A6_pass: route_to_application_gate, A6_probe: probe_then_route, A6_fail: soft_dq }
  application_gate_transition:
    if_high_fit:        { final_message: "...", next_state: ApplicationGate, operator_review: false }
    if_soft_fit:        { final_message: "...", next_state: ApplicationGate, operator_review: spot_check }
    if_unqualified_soft:{ final_message: "...", next_state: NotAFit_with_reactivation_tag }
    if_hard_dq:         { action: route_to_operator_review, operator_brief: {...}, next_state: OperatorReview }
  escalation_triggers:
    hard_dq:    { fires_when: "any axis surfaces hard-DQ pattern", routing: OperatorReview, sla_hours: 24 }
    borderline: { fires_when: "classifier confidence <70 OR 2+ axes in probe", routing: OperatorReview_advisory, sla_hours: 48 }
    high_stakes:{ fires_when: "revenue >10x offer floor OR named-account OR referral", routing: OperatorReview_from_M1, sla_hours: 12 }
```

## Failure modes

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| Survey-style thread | Prospect reply quality collapses; one-word answers | All-questions-in-M1 or >2 questions per message | Re-spread across 2-4 messages; enforce Rule 1 ceiling |
| Soft-DQ reads as rejection | Prospect ghosts after the soft-DQ message; brand damage; no referrals | Fail-branch language is "you don't qualify" not "not yet" | Rewrite per Rule 2: door-open + reactivation trigger + operator phrase |
| Calendar leak before application | Prospect books direct on calendar; no-show rate spikes | Final transition message links to calendar instead of app | Restore Rule 6 application gate; remove all calendar links from thread templates |
| Hard-DQ auto-archived | Workspace silently archives hard-DQ trips; A-13 violated | Branching logic routes to archive instead of OperatorReview | Hardcode hard_dq → OperatorReview in branch matrix; never `archive` as a default |
| Budget axis lands cold | Prospect goes silent after budget question | Budget surfaced too early, before reciprocity built | Re-sequence per Rule 3: budget is A6, never A1 |
| High-stakes on VA flow | Enterprise prospect or referral runs through standard thread, gets no operator touch | Step 6 escalation triggers not wired | Wire high_stakes trigger to fire from M1; operator owns these from message 1 |
| Voice contamination on client thread | Client's qualification thread reads in agency voice | Voice loader pulled agency_voice instead of client_voices[slug] | Per A-6, hydrate from `brand_voice.client_voices[slug]`; refuse if subprofile <60% |
| Probe absent, ambiguous answer treated as pass | Prospect with ambiguous revenue answer routed to next axis as if qualified | Branching matrix has only pass/fail, no probe state | Add probe branch to every axis; probe = single clarifier message |

## Cross-references

- `skills/build-qualification-thread/SKILL.md` — the build skill that produces threads from this framework
- `skills/build-application/SKILL.md` — the application form this thread funnels to (coordinate axes to avoid redundancy)
- `skills/classify-dm-intent/SKILL.md` — runs on every reply; thread continues only if state remains Qualifying
- `skills/triage-application/SKILL.md` — runs on application submission, owned by acquisition-head
- `reference/frameworks/dm/intent-taxonomy.md` — the upstream classifier that determines whether a reply enters this thread
- `reference/frameworks/dm/booking-gate-construction.md` — the application form architecture this thread coordinates with
- `reference/swipe-file/dm-threads/qualification/` — annotated example threads per archetype
- `workflows/divisions/acquisition.md` — the FSM (Replied → Qualifying → SoftFit/HighFit/HardDQ → ApplicationGate)
- `INVARIANTS.md` N-4 (banned vocab), N-12 (application required), A-6 (recursive context for client scope), A-13 (operator-in-loop on hard-DQ)

---

*Framework version 1.0.0 — 2026-05-03*
