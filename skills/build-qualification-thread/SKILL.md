---
name: build-qualification-thread
slug: /build-qualification-thread
agent: dm-strategist
department: acquisition
output_format: qualification-thread-spec
required_profiles:
  ideal_customer_profile: 75               # recursive: agency-icp or client-icp at the target scope
  product_strategy: 60                     # recursive: service_tier offer or per-client deliverable spec the thread funnels toward
allowed_scopes:
  - agency-qualification                   # the thread the agency runs for its OWN inbound DM replies
  - client-qualification                   # the thread the agency runs FOR a client account on the client's inbound DM replies
output_path:
  agency-qualification: company.yaml::content_strategy.agency_content.qualification_thread
  client-qualification: company.yaml::content_strategy.client_content[{client_slug}].qualification_thread
---

# /build-qualification-thread — Post-Reply Qualification Architecture

> **Purpose.** Build the qualification thread that runs *after* a prospect replies positively to a DM sequence. Multi-message conversational flow that surfaces fit, surfaces dis-qualifiers, transitions the prospect to the application gate (or a graceful exit), and prevents the calendar from filling with anyone-with-a-pulse calls. Without this thread, close rate collapses because half of booked calls are bad-fit.
>
> **Bar.** Of qualified replies that enter this thread, ≥40% should reach the application gate AND ≥75% of submitted applications should pass triage. Below either floor, the thread is too soft (bad-fit calls) or too hard (filtering out high-fit).
>
> **The 1-of-1 leverage.** Every other LinkedIn agency uses the calendar link as the qualifier — "book a time and we'll figure it out." Result: 50% no-show, 60% bad-fit, 15% close rate. This workspace inverts: qualification IS the conversation. The application IS the gate. The calendar comes after both. Per `INVARIANTS.md` N-12 — application required, no exceptions.

## When to invoke

- After `/build-outbound-engine` produces the engine spec and the qualification thread needs populating (`agency-qualification` scope)
- After a new client is signed and their inbound thread needs its own qualification flow (`client-qualification` scope)
- Close rate of booked calls drops below 25% for 30 consecutive days — qualification leaking bad-fit prospects to calendar
- Call-held rate drops below 70% — qualification failing to filter timeline-not-now or budget-not-here
- Operator surfaces a pattern: "I keep getting on calls with people who can't afford this" or "the call surfaced something I should have caught in DMs" → re-build with sharper axes
- New offer launches and qualifier set must funnel toward the new `target_avatar`

## Required inputs

1. **The ICP profile** at ≥75% completeness for the target scope (agency or per-client).
2. **The offer the thread funnels toward** at ≥60% — `product_strategy.service_tier.offers[*]` or per-client deliverable spec. Specifically need: `target_avatar`, `price_point_monthly`, `unique_mechanism`, `scope_guardrails`.
3. **The DQ criteria from `/build-icp` Step 2** (closed-lost contrast set) — explicit hard-DQ patterns.
4. **The application form** from `/build-application` — coordinate axes to avoid redundancy / contradictions.
5. **Past qualification-thread data** (if available) — last 30 threads tagged by outcome (booked / DQ'd / ghosted).
6. **The voice profile** at the target scope — thread must read in the operator's (or client's) voice, not a questionnaire.

If any input is below threshold → halt with `SKILL_BLOCKED` and recommend the upstream skill (`/build-icp`, `/design-retainer-offer`, or `/build-application`).

## Why a thread, not a questionnaire

The most common failure mode is to build qualification as a 5-question survey: "What's your revenue? What's your timeline? What's your budget? What have you tried? When do you want to start?"

This kills conversion. Why: reads as interrogation (trust drops with question density); surfaces qualifiers but fails to surface dis-qualifiers (people lie on surveys, less so in conversation); skips the relationship-building that makes the call easier to close; treats the prospect as a CRM row, not an operator considering a $7K-$25K/month decision.

The thread approach: 4-6 qualification AXES, surfaced across 2-4 conversational messages, embedded in operator voice. Each message asks 1-2 questions max, with reply-branch logic that adapts depth based on signal.

## The build sequence

### Step 1 — Qualification axes definition (4-6 axes) (15 min)

Define the axes the thread must surface. Derived from offer's `target_avatar` + ICP's closed-lost contrast set + hard-DQ filters.

Default axis set for a B2B services-agency retainer ($7K-$25K/mo):

| Axis | What it surfaces | Source |
|---|---|---|
| **A1 — Revenue tier** | Firmographic fit (revenue floor for offer) | offer's `target_avatar.revenue_floor` |
| **A2 — Role authority** | Decision authority + decision-making process | ICP's `behavioral_patterns.decision_making_process` |
| **A3 — Urgency / trigger event** | Buying-trigger present? Or "just exploring"? | ICP's `behavioral_patterns.buying_triggers` |
| **A4 — Fit-for-mechanism** | Does the offer's unique mechanism actually solve their stated problem? | offer's `unique_mechanism` + prospect's stated pain |
| **A5 — Hard-DQ filters** | Any of the explicit DQ patterns from closed-lost set? | ICP's `disqualifier set` from `/build-icp` Step 2 |
| **A6 — Budget tier** | Can prospect actually invest at offer's price point? | offer's `price_point_monthly` + prospect's stated context |

Customize per offer. Program-tier qualification has different axes (cohort timing, peer-fit, learning-style, current revenue tier, fulfillment-bottleneck). Custom-tier qualification adds equity / scope / strategic-fit axes.

Output: axis table with per-axis definition + per-axis source criterion + per-axis pass/probe/fail thresholds.

### Step 2 — Question architecture (each axis → 1-2 surfacing questions) (20 min)

For each axis, write 1-2 questions designed to surface the answer naturally. Design rule: questions must surface dis-qualifiers AS COMFORTABLY as qualifiers. A prospect who cannot afford the offer must feel safe saying so.

Question design principles:
- **Use ranges, not point values.** "Ballpark — more like $1M ARR or $5M+?" beats "What's your revenue?"
- **Use either/or framing.** "Is this a 'start in 30 days' kind of conversation, or 'maybe Q3' exploration?" beats "What's your timeline?"
- **Embed the soft-DQ in the question.** "Most {role}s I work with are at $1.5M+ ARR — is that you, or earlier stage?" makes the floor visible AND gives the prospect an exit if they're under.
- **Anchor with number-of-clients framing.** "Of the {role}s I work with, about 70% have {symptom}. Is that you?" surfaces fit without making it about price or qualification.
- **Use mechanism-test questions.** "We work primarily with {role}s who have already tried {prior approach} and want a different mechanism — does that fit?" tests fit without sounding gate-keepy.

Default question set per axis (customize per offer):

| Axis | Q1 | Pass | Probe | Fail |
|---|---|---|---|---|
| **A1 revenue** | "Quick context — closer to $1M ARR or $5M+? Just so I know where in the playbook we'd start." | revenue ≥ offer floor | revenue near floor (within 20%) | revenue < (floor × 0.7) → soft-DQ branch |
| **A2 authority** | "Who else is involved in a decision like this on your end? Solo, partner-consult, or team sign-off?" | solo OR named decision-maker accessible | "run by my partner" with named partner | "let me ask my boss" with no named boss → soft-DQ |
| **A3 urgency** | "What made you start thinking about this now vs. 6 months ago? Anything specific in the last 30 days?" | specific trigger named (raised, churn, hire, milestone) | "thinking about this for a while" — surface trigger | "just exploring" with no trigger → SoftFit, nurture |
| **A4 mechanism** | "What have you tried before for {area} that didn't quite work, and what felt off about it?" | prior approach maps to a problem the offer's unique_mechanism solves | prior approach unclear → second probe | prior approach worked + still in place → not-a-fit, graceful exit |
| **A5 hard-DQ** | (typically embedded in other questions) | no DQ patterns | borderline DQ | hard-DQ trigger → HardDQ → OperatorReview per A-13 |
| **A6 budget** | "If we found a fit, the work for {offer} is in the {price-range}/mo zone. Is that in your zone, or are we talking different math?" | budget ≥ offer price | "tight but possible" → second probe (financing? sliding scale?) | budget materially below floor → soft-DQ → graceful exit + reactivation tag |

Output: per-axis Q1 (and Q2 if needed) drafts in operator voice + per-axis pass/probe/fail decision rules.

### Step 3 — Message sequencing (2-4 messages, conversational not interrogative) (15 min)

Spread the 4-6 axes across 2-4 messages. Never put all questions in one message. Conversation density rule: max 2 questions per message.

Default sequencing template (4-axis path, 3 messages):

| M# | Purpose | Trigger | Content beat | Voice / window |
|---|---|---|---|---|
| **1** | acknowledge + warm transition + first axis | Replied state from `/classify-dm-intent` | [Acknowledge reply specifically, not generic "thanks for reaching out"] + [Bridge to qualification with operator's actual phrase] + [Q1 — A1 revenue OR A3 urgency, whichever feels more natural for thread tone] | warm + curious, not interrogative; 24-72h reply window |
| **2** | deeper qualification + mechanism fit | Reply received to M1 | [React to M1 reply specifically — verbatim acknowledgment] + [Q2 — A2 authority, framed conversationally] + [Q3 — A4 mechanism, framed as curiosity about prior approach] | operator-as-peer; 24-48h |
| **3** | budget axis + transition to application | Reply received to M2 with no DQ surfaced | [React to M2 reply] + [Q4 — A6 budget, framed as zone-check] + [if all qualifiers passed → transition to application with specific framing] | direct + transparent on price; application-framed as "this is how we make sure we're a fit before both invest 30 min"; 24-48h |

For 6-axis paths (program-tier, custom-tier), spread across 4 messages. For 4-axis paths (light qualification, warm-class re-engagement), 2 messages with 2 axes each is sometimes correct.

Output: per-message draft with embedded questions, in operator voice, with reply-window expectations.

### Step 4 — Reply-branch logic per question (positive / probe / DQ branches) (15 min)

Every question has three branches. The branch determines the next message OR the next state transition.

```yaml
- question: "Q1 — A1 revenue tier"
  branches:
    if_pass:                                   # revenue ≥ floor
      action: continue_to_next_axis
      next_message: M2
      tag: "axis_a1_passed"
    if_probe:                                  # revenue near floor, ambiguous
      action: send_probe_question
      probe_question: "Got it — and is that {revenue} growing, flat, or — total context — declining?"
      then_route_to: M2 if growing | soft-DQ branch if flat/declining
      tag: "axis_a1_probe"
    if_fail:                                   # revenue materially below floor
      action: route_to_soft_dq_branch
      soft_dq_message: |
        [Acknowledge gracefully — "totally hear you, early stage"]
        [Reframe as not-yet rather than not-ever]
        [Door-open language — "if {trigger event} happens, here's the reply that gets you to the front"]
      next_state: NotAFit (per acquisition FSM) → archive with revenue-floor reason + reactivation tag
      tag: "axis_a1_failed_soft_dq"
```

Repeat per question per axis. The branch matrix becomes the runtime guide for the dm-strategist agent (or VA running the thread) to know what to send next.

Critical rule from `intent-taxonomy.md`: every reply re-routes through `/classify-dm-intent` first. The reply-branch logic here is for routing AFTER classification confirms the prospect is still in Qualifying state.

Output: per-question branch table covering pass / probe / fail routes for every Q across M1-M{final}.

### Step 5 — Application-gate transition (qualified → application; unqualified → graceful exit) (10 min)

After the final qualification message, branch on outcome:

```yaml
application_gate_transition:
  if_high_fit:                                 # all axes passed
    final_message: |
      [Acknowledge fit — specifically reference what fits]
      [Frame the application — "because this is a 6-12 month commitment on both sides, I have a short app that helps me pre-build the call so we both get more out of the 30 min"]
      [The application link]
      [What to expect — "5 min to fill, I'll review same-day, calendar opens after"]
    next_state: ApplicationGate
    operator_review: not required for HIGH-fit
    
  if_soft_fit:                                 # most axes passed, 1-2 ambiguous
    final_message: |
      [Same as high-fit, but with one explicit acknowledgment of the ambiguous axis]
      [The application link, with note that one of the questions in the app addresses {ambiguous axis}]
    next_state: ApplicationGate
    operator_review: spot-check on app submission
    
  if_unqualified_soft:                         # one axis materially failed, no hard-DQ
    final_message: |
      [Reframe as not-yet, not not-ever]
      [Specific reactivation trigger — "if {trigger} happens, DM 'WAIT' and I'll re-open"]
      [Door-open language — operator's own phrase]
    next_state: NotAFit → archive with reason + reactivation tag
    operator_review: not required
    
  if_hard_dq:                                  # any hard-DQ trigger (per A-13)
    action: route_to_operator_review
    operator_brief: { thread_summary, dq_evidence, recommended_decision }
    next_state: OperatorReview
    operator_review: REQUIRED per A-13
```

The application IS the qualification gate. Per N-12: never accept a discovery call without an application form on file. The qualification thread funnels to the application; the application gates the calendar.

Why-application framing matters: "this 5-question app helps me pre-build the call" frames the app as a benefit. "Most agencies skip this and waste your 30 min" frames it as a quality signal. Never frame as "fill this out so we can decide if you qualify" — that pattern-matches a job application.

### Step 6 — Operator-escalation triggers (any hard-DQ, any borderline, any high-stakes) (5 min)

Thread escalates to operator review in three cases per A-13:

| Trigger type | When it fires | Routing | Operator decision options | SLA |
|---|---|---|---|---|
| **hard_dq** | any axis surfaces a hard-DQ pattern | OperatorReview (per A-13 — never auto-archive) | confirm_dq / override_dq_and_continue / flag_for_re_engagement | <24h |
| **borderline** | `/classify-dm-intent` confidence < 70 OR 2+ axes in probe state | OperatorReview (advisory only, not blocking) | continue_to_app / route_to_nurture / manual_call_invite | <48h |
| **high_stakes** | enterprise tier (revenue > 10× offer floor) OR named-account (operator's pre-flagged target list) OR referral from existing client | OperatorReview (always, regardless of qualification state) — high-stakes prospects deserve operator-touch from M1, not VA-driven flow | continue_normal_flow / escalate_to_personal_invite / custom_path | <12h |

Every escalation produces an operator brief — 2-sentence thread summary, trigger evidence, recommended decision options. Operators don't read full threads; they read briefs and decide.

### Step 7 — Output emission (qualification thread YAML + per-message draft + branch map + escalation triggers) (5 min)

Hydrate the artifact and write to the scope-appropriate path:
- `agency-qualification` → `company.yaml::content_strategy.agency_content.qualification_thread`
- `client-qualification` → `company.yaml::content_strategy.client_content[{client_slug}].qualification_thread`

```yaml
qualification_thread:
  scope: agency-qualification | client-qualification
  client_slug: null | "<slug>"
  funnels_to_offer: "<offer_slug>"
  funnels_to_application: "<application form path>"
  total_messages: 2-4
  axes: [<A1-A6 with pass/probe/fail thresholds>]
  messages:
    - message_number: 1
      purpose: ""
      trigger: ""
      questions: [<axes asked in this message>]
      template: |
        <full draft>
      branches: <per-question branch table from Step 4>
      voice_calibration_notes: ""
      expected_reply_window_hours: 24-72
    # ... M2-M{final}
  application_gate_transition: { high_fit, soft_fit, unqualified_soft, hard_dq }
  escalation_triggers: { hard_dq, borderline, high_stakes }
```

## Validation gate (mandatory before declaring done)

Three checks before the thread ships:

1. **Conversational-not-interrogative test.** Read the full thread aloud as a single conversation. If any message reads as a survey, re-write Step 3 to spread questions further or soften framing. Pass: thread reads as peer-to-peer conversation, not CRM intake form.

2. **Dis-qualifier-comfort test.** For each axis with a fail branch, the question + branch combination must let a prospect who fails feel safe saying so. Pull fail-branch language and check: does it sound like graceful exit + door-open, or rejection? Below pass = re-write fail branch.

3. **30-thread historical replay.** If past qualification-thread data exists, replay the new design against the last 30 actual threads. Of those 30, how many would reach application gate vs. graceful exit vs. operator review under the new design? Compare against actual outcomes. Disagreement >30% = the new design has materially shifted thresholds — confirm with operator before deploying.

## Output format

```yaml
---
artifact_type: qualification-thread-spec
scope: agency-qualification | client-qualification
client_slug: null | "<slug>"
funnels_to_offer: "<offer_slug>"
funnels_to_application: "<application path>"
build_date: YYYY-MM-DD
validation_gate: passed | failed | not-yet-run
profiles_used: [ideal_customer_profile, product_strategy, brand_voice]
frameworks_used: [intent-taxonomy, qualification-thread-design, dq-criteria, fsm-acquisition]
banned_vocab_lint: passed | failed
confidence: HIGH | MEDIUM | LOW
gap_flags: [list]
---

{populated thread YAML from Step 7}

## Per-message drafts
{full text M1 through M{final} with all branches visible}

## Branch map
{table of every pass / probe / fail / hard-DQ route across all questions}

## Application-gate transition flows
{the four branches at thread end with their final-message templates}

## Operator escalation playbook
{the three escalation triggers + operator decision options + SLAs}

## Validation gate result
{conversational-not-interrogative + dis-qualifier-comfort + 30-thread historical replay}

## Next actions
{deploy thread, schedule day-30 outcome-rate review, coordinate with /build-application to ensure no question redundancy}
```

## Failure modes and guards

- **Survey-style thread.** Reads as interrogation, kills conversion. Guard: Step 3 sequencing rule (max 2 questions per message) + Step 7 conversational-not-interrogative validation.
- **All-questions-in-M1.** Dumping 4 axes into the first post-reply message destroys conversational quality. Guard: M1 = at most 1 axis; M2 = 1-2; M3 = 1-2.
- **Hard-DQ auto-archive (NEVER).** Per A-13, hard-DQ is operator-decided. Guard: Step 6 escalation triggers route hard-DQ to OperatorReview, never auto-archive.
- **Calendar without application.** Sending a calendar link before application = N-12 violation, no-show >40%. Guard: Step 5 application-gate transition is the only path to calendar; final messages always link to app, never calendar directly.
- **Soft-DQ as rejection.** Fail-branch language reading as "you're not good enough" creates brand damage and kills referrals. Guard: Step 4 fail branches use not-yet language with explicit reactivation triggers.
- **Application redundancy.** Asking same question in DM AND on app form = friction. Guard: Step 1 axis definition references `/build-application` schema; coordinate so thread surfaces 4-6 axes and app surfaces 4-6 *different* axes.
- **Voice contamination (client-qualification).** Agency voice creeps into client thread. Guard: Step 3 voice_calibration is per-scope; client-qualification hydrates from client voice subprofile per A-6.
- **Banned-vocab in qualification questions.** "Hop on a quick call" / "circle back" / "synergize" leak in. Guard: standard banned-vocab lint per N-4 + spec/BANNED-VOCABULARY.md runs before output emission.
- **High-stakes prospect on VA flow.** Enterprise or referral gets standard VA thread, no operator touch. Guard: Step 6 high-stakes escalation trigger fires automatically and routes to operator from M1.
- **Unqualified-but-no-graceful-exit.** Thread ends with no message after fail signal — prospect ghosted, brand damage. Guard: Step 5 unqualified_soft branch is mandatory; every fail path has a graceful exit message.

## Cross-references

- `reference/frameworks/dm/intent-taxonomy.md` — Replied → Qualifying state transitions feed this thread
- `reference/frameworks/dm/qualification-thread-design.md` — `(Cycle 2 — TBD)` framework deep-dive on axis design
- `workflows/divisions/acquisition.md` — the FSM (Replied → Qualifying → SoftFit/HighFit/HardDQ → ApplicationGate) this thread drives
- `INVARIANTS.md` N-4 (banned vocab), N-12 (application required), A-6 (recursive context for client scope), A-13 (operator-in-loop on hard-DQ)
- `agents/acquisition-head.md` — owner of qualification + application gate; consumes thread output for routing
- `skills/build-outbound-engine/SKILL.md` — engine spec defining the thread's downstream (application + calendar)
- `skills/write-dm-sequence/SKILL.md` — per-class sequences leading INTO this thread on Replied state
- `skills/classify-dm-intent/SKILL.md` — runs on every reply within thread; thread continues only if state remains Qualifying
- `skills/build-application/SKILL.md` — application form this thread funnels to (coordinate axes to avoid redundancy)
- `skills/triage-application/SKILL.md` — runs on application submission, owned by acquisition-head
- `skills/build-call-prep/SKILL.md` — runs after Booked state (post-application, post-calendar)
- `spec/CONTEXT-THRESHOLDS.md` — threshold gating for ICP and product profiles per scope
- `spec/BANNED-VOCABULARY.md` — lint list for question + branch language

## Examples

See `examples/`:
- `example-agency-qualification-thread-services-retainer.md` — 3-message thread for $7K-$15K/mo services retainer, 4-axis path
- `example-agency-qualification-thread-program-enrollment.md` — 4-message thread for $20K program, 6-axis path with peer-fit axis
- `example-client-qualification-thread-saas-founder-account.md` — 3-message thread for SaaS-founder client account, custom DQ on tech-stack-fit
- `example-client-qualification-thread-services-founder-account.md` — 2-message thread for established services-founder client (warm-class re-engagement, lighter qualification)
- `example-hard-dq-route-operator-review.md` — example thread surfacing hard-DQ in M2, routing to operator review with brief

---

*Skill version 1.0.0 — 2026-05-03*
