---
name: build-outbound-engine
slug: /build-outbound-engine
agent: dm-strategist
department: acquisition
output_format: outbound-engine-spec
required_profiles:
  ideal_customer_profile: 75              # recursive: agency-icp or client-icp
  brand_voice: 60                         # recursive
  product_strategy.service_tier: 60       # need to know what we're funneling toward
allowed_scopes:
  - agency-outbound                       # agency runs DMs to its own ICP
  - client-outbound                       # agency runs DMs for a client's account to client's ICP
output_path:
  agency-outbound: company.yaml::acquisition_engine.agency_outbound
  client-outbound: company.yaml::acquisition_engine.client_outbound[{client_slug}]
---

# /build-outbound-engine — DM Operations Engine

> **Purpose.** Build the canonical outbound DM operations engine — the intent taxonomy, the per-intent sequences, the qualification thread, the booking gate, the lead-list spec, and the operator/VA handoff protocol. This is the engine that runs ~300-500 DMs/month per account at >25% qualified-reply rate.
>
> **Bar.** A VA running this engine, with the spec + lead list, must produce more qualified booked calls than a $5K/month outsourced SDR. Verified by side-by-side comparison.
>
> **The 1-of-1 primitive.** Most LinkedIn agencies run one cold sequence to one list. This engine runs **four sequences keyed to four intent classes**. The classification is the leverage; the sequences are the execution.

## When to invoke

- New operator onboarding (build for `agency-outbound`)
- New client signed → after the client's ICP is built (build `client-outbound`)
- DM reply rate <8% (re-build — sequences stale or ICP-list mismatch)
- Booking-gate leak (calls booked but no-show rate >35%) — re-build qualification thread
- Quarterly DM-sequence refresh (LinkedIn detects template repetition; refresh prevents shadow-banning)

## Required inputs

1. ICP profile ≥75% (from `/build-icp`) — voice-of-customer language is load-bearing
2. Voice profile ≥60% (from `/extract-founder-voice`) — DMs in the wrong voice destroy authority
3. Service-tier offer ≥60% (from `/design-retainer-offer`) — DMs funnel toward this offer
4. **Past DM data** (if available) — last 100 DM threads, win/loss tagged
5. **Top-3 lead-list sources** the operator uses (Sales Nav saved searches, Apollo lists, ICP databases)
6. Operator/VA available for build session (60 min)

## The DM Intent Taxonomy (the load-bearing primitive)

Every DM sequence in this workspace targets one of four intent classes. **Sending a class-A sequence to a class-B prospect is the most common reason DM operations fail.**

### Class C — Cold

**Definition.** Prospect has never interacted with subject's account or content. Sourced from a lead list.
**Volume.** Highest — 60-75% of total DM volume.
**Reply rate baseline.** 5-12% (anything below 5% = list bad or sequence bad).
**Sequence length.** 5-7 messages over 14-21 days.
**Sequence shape:** Pattern-interrupt opener → soft-disqualifier → value-give → soft-DQ ask → call invite → polite bow-out.
**Voice constraint.** Must read as written by a real person, not a template. Single-sentence personalization in M1 is mandatory.

### Class W — Warm

**Definition.** Prospect has interacted with subject's content in the last 30 days (post like, comment, profile view).
**Volume.** 15-25% of total DM volume.
**Reply rate baseline.** 25-45%.
**Sequence length.** 3-5 messages over 7-14 days.
**Sequence shape:** Specific-engagement reference opener → soft qualification → value-give → call invite → bow-out.
**Voice constraint.** Reference the specific post/comment/action — never generic "saw you engaging with my content."

### Class L — Liked-your-post

**Definition.** Prospect liked a *specific* high-intent post (case study post, offer-bridge post, named-mechanism post).
**Volume.** 5-15% of total DM volume — highest-value class.
**Reply rate baseline.** 50-70%.
**Sequence length.** 2-4 messages over 3-7 days.
**Sequence shape:** Direct-reference opener acknowledging the like + the post → 1-question qualifier → call invite or lead-magnet drop.
**Voice constraint.** Move fast (engagement signal decays in 5-7 days). Skip pleasantries.

### Class M — Comment-for-lead-magnet

**Definition.** Prospect commented the trigger word on a content CTA post (e.g., "PLAYBOOK" / "INFO" / "GUIDE").
**Volume.** Variable (driven by content engine output) — ideally 10-30/week.
**Reply rate baseline.** 80-95% (they self-selected — a low rate means the lead magnet was misrepresented).
**Sequence length.** 3-5 messages over 5-10 days.
**Sequence shape:** Lead-magnet delivery in M1 → consumption check in M2 → soft qualification in M3 → call invite or DQ in M4.
**Voice constraint.** Deliver value first (the magnet), THEN qualify. Inverting destroys trust.

The classifier (`/classify-dm-intent`) runs on every inbound reply and routes to the right sequence.

## The build sequence

### Step 1 — Lead-list specification (10 min)

For each intent class that requires sourced leads (primarily Class C), define:

```yaml
lead_list_sources:
  - source_name: ""                         # Sales Nav | Apollo | LinkedIn Live attendee export | etc
    filter_criteria: {}                     # specific firmographic + behavioral filters
    intent_signal: ""                       # what makes this list higher-than-cold (e.g. "posted about hiring an SDR last 30d")
    refresh_cadence_days: 30
    ICP_match_threshold: 0.75               # cosine similarity to ICP profile required to keep on list
    approximate_volume_per_refresh: 0
```

Cross-reference `skills/source-lead-list/SKILL.md` for list-build protocol.

### Step 2 — Cold sequence build (Class C) (15 min)

Build the 5-7 message sequence. Each message:

```yaml
- message_number: 1
  send_after: "0 days from connection accept"
  message_type: "pattern-interrupt opener"
  template: |
    {first_name} — quick context.
    
    {specific personalization line — must reference something from their profile or recent post — non-templated}
    
    {one-line hypothesis about their pain in their own ICP language}
    
    Wrong? (No need to reply if it's a miss.)
  voice_calibration: "casual-direct, no pitch, single hypothesis"
  expected_reply_rate: "8-15%"
  next_step_if_reply: "M2 in 1 day, voice-check"
  next_step_if_no_reply: "M2 in 4 days, soft-DQ angle"
```

Repeat for M2-M7. Each message's purpose is distinct — never two consecutive messages with the same purpose. Common sequence shape:

| M# | Day | Purpose | What it does |
|---|---|---|---|
| 1 | 0 | Pattern-interrupt opener | One-sentence personalization + one-sentence pain hypothesis + "wrong?" |
| 2 | 4 | Soft-DQ angle | "Most {role}s I work with don't have {specific symptom}. Curious if that's you?" |
| 3 | 8 | Value-give | One free resource or one specific insight (no ask attached) |
| 4 | 13 | Soft DQ + intro to offer | "If {specific symptom is true}, this is the kind of work we do" — links to one specific case study (not the offer page) |
| 5 | 18 | Call invite | Direct ask, named outcome, time-boxed |
| 6 | (optional) | Bow-out with door-open | "I'll stop pinging — if {specific trigger} ever happens, here's the reply that gets you to the front" |
| 7 | (optional) | Reactivation 60 days later | If they engaged in the meantime, single re-open with new angle |

### Step 3 — Warm sequence build (Class W) (10 min)

3-5 messages, 7-14 days. Sequence template:

| M# | Day | Purpose |
|---|---|---|
| 1 | 0 | Specific-engagement reference + light qualification question |
| 2 | 3-4 | Brief value-give tied to the engagement topic |
| 3 | 7 | Soft call invite ("worth 20 min?") |
| 4 | (optional, if no reply) | Door-open bow-out |

### Step 4 — Liked-your-post sequence build (Class L) (10 min)

2-4 messages, 3-7 days. Sequence template:

| M# | Day | Purpose |
|---|---|---|
| 1 | 0-1 | Direct: "Saw you liked the {specific post}. The thing in there about {specific takeaway} — does that map to anything you're working on?" |
| 2 | 2-3 | If reply: confirm fit, propose 20-min call. If no reply: short value drop. |
| 3 | 5-7 | Bow-out with door-open |

### Step 5 — Comment-for-lead-magnet sequence build (Class M) (10 min)

3-5 messages, 5-10 days. Sequence template:

| M# | Day | Purpose |
|---|---|---|
| 1 | 0 (within 1 hour of comment) | Deliver the magnet. Nothing else. |
| 2 | 2 | Consumption check ("did the {magnet} land okay?") + invite reply |
| 3 | 4 | If reply: qualifying question. If no reply: short value extension. |
| 4 | 7 | Call invite tied to the magnet's topic |
| 5 | 10 | Door-open bow-out |

### Step 6 — Qualification thread design (10 min)

Across all classes, the qualification thread is the **mid-sequence questions** that determine whether to invite to a call OR disqualify. Three questions max, embedded across 2-3 messages so they don't read as an interrogation.

Default qualification question set (customize per service offer):
- **Q1:** A revenue/stage qualifier ("ballpark — are you doing more like $1M ARR or $5M+?") — surfaces firmographic fit
- **Q2:** A trigger qualifier ("what made you start thinking about LinkedIn now vs. 6 months ago?") — surfaces buying triggers
- **Q3:** A budget/decision qualifier ("if you found the right partner for this, are you in a position to invest $X/mo or is this a 'maybe Q2' kind of thing?") — surfaces decision-making + budget

Hard DQs (auto-bow-out, never invite to call):
- Revenue below {floor} (defined in offer's `target_avatar`)
- No budget authority (refers to "asking my boss" without naming the boss)
- Timeline >6 months (re-engage in 6 months — flag for reactivation)
- Wrong vertical (no client case study in their vertical AND vertical isn't on the agency's expansion list)

### Step 7 — Booking gate construction (5 min)

After qualification:
- **High-fit reply:** route to direct booking link (Calendly w/ application question OR Savvycal w/ qualification questions embedded)
- **Medium-fit reply:** route to application form (`skills/build-application/`) — no calendar link until app submitted
- **Low-fit reply:** bow-out with door-open
- **Hard-DQ reply:** polite bow-out + flag in CRM as "DQ — re-engage if {trigger}"

The booking gate IS the qualification — calendars without gates leak no-shows.

### Step 8 — Operator/VA handoff protocol (5 min)

Define who runs each part:
- **Lead-list build:** Operator-led for first 3 months, VA-handed-off after.
- **Sequence sends:** VA-led from week 1, with operator review of M1 personalization (manual) until rep flow stabilizes.
- **Reply triage:** Workspace-classified (`/classify-dm-intent`), VA-routed to right sequence, operator-flagged for call-invite-stage messages.
- **Call invites:** Operator-sent for first 90 days (founder-led). VA can send after operator has signed-off on 30 invites.
- **Hard DQs:** Workspace-flagged, operator-confirmed (founder DQ stays a founder call per `INVARIANTS.md` A-13).

### Step 9 — Output the engine spec

Hydrate the outbound engine in `company.yaml`:

```yaml
{scope_path}:
  intent_taxonomy:
    cold:
      sequence: []                        # from Step 2
      lead_list_sources: []               # from Step 1
      target_volume_per_week: 0
      target_reply_rate: 0.10
    warm:
      sequence: []                        # from Step 3
      target_volume_per_week: 0
      target_reply_rate: 0.30
    liked_post:
      sequence: []                        # from Step 4
      target_volume_per_week: 0
      target_reply_rate: 0.55
    comment_for_lead_magnet:
      sequence: []                        # from Step 5
      target_volume_per_week: 0
      target_reply_rate: 0.85
  qualification_thread:
    questions: []                         # from Step 6
    hard_dqs: []
  booking_gate:
    high_fit_route: ""                    # from Step 7
    medium_fit_route: ""
    low_fit_route: ""
    hard_dq_route: ""
  handoff_protocol:
    lead_list_build: ""                   # from Step 8
    sequence_sends: ""
    reply_triage: ""
    call_invites: ""
    hard_dq_handling: ""
  monthly_volume_target:
    cold_dms: 0
    warm_dms: 0
    liked_post_dms: 0
    comment_dms: 0
    qualified_replies: 0
    calls_booked: 0
    calls_held: 0
    closes: 0
```

## Validation gate (mandatory before declaring done)

Three checks:

1. **The "could a human SDR do better?" test.** Project monthly outputs (calls booked, qualified replies) against what a $5K/month SDR could deliver in the same period. Workspace must beat the SDR on calls/dollar AND quality-of-call (close rate of booked calls). If projection is unfavorable, sequences are too generic — re-run Step 2 with sharper personalization rules.

2. **The voice-of-customer match test.** Pull 5 verbatim phrases from the ICP's `voice_of_customer.pain_language_patterns`. For each, find them in the cold sequence's M1 or M2 hypothesis lines. At least 3 of 5 must appear. Below 3, re-run Step 2 with the ICP profile open.

3. **The 100-DM dry run.** Send the cold sequence's M1 to 100 ICP-matching prospects. Reply rate at 7 days must be ≥8%. Below 8%, the personalization or pain hypothesis is off — diagnose with the operator.

## Output format

```yaml
---
artifact_type: outbound-engine-spec
scope: agency-outbound | client-outbound
client_slug: null | "<slug>"
profile_completeness: 0-100
build_date: YYYY-MM-DD
validation_gate: passed | failed | not-yet-run
projected_monthly_volume: { cold: 0, warm: 0, liked_post: 0, comment: 0, qualified: 0, calls: 0 }
profiles_used: [ideal_customer_profile, brand_voice, product_strategy.service_tier]
frameworks_used: [intent-taxonomy, qualification-thread, booking-gate]
confidence: HIGH | MEDIUM | LOW
---

{the populated engine YAML from Step 9}

## Operator brief
{1-page narrative — sequence logic, volume targets, what the operator vs. VA owns}

## VA-facing playbook
{step-by-step daily operating procedure for the VA running the sequences}

## Validation gate result
{the SDR-comparison projection, the voice-of-customer match check, the 100-DM dry run results}

## Next actions
{usually: schedule the dry run, set the lead-list-source build, deploy week-1 sequences}
```

## Failure modes and guards

- **Single-sequence-for-all trap.** The most common LinkedIn-agency failure mode. Engine MUST have 4 sequences. If the operator wants "just one," surface that the cost is 50%+ reply-rate compression.
- **Cold sequence written in templated voice.** A cold M1 that reads as a template kills reply rate. M1 personalization must be human-written or workspace-generated with verified ICP-data input — never variable substitution into a generic frame.
- **Missing class-L sequence.** Class L is the highest-value class and the most commonly skipped. If the content engine produces high-engagement posts, class L volume will be material. Build the sequence even if volume is low at start.
- **Comment-for-lead-magnet without ManyChat or equivalent automation.** M1 must arrive within 1 hour of comment. Manual delivery breaks the chain.
- **Hard-DQ list too soft.** If hard-DQs aren't actually hard (operator overrides them), the qualification thread is theater and close rate stays low. Audit DQ-override rate quarterly.
- **No booking gate.** Calendly link in M5 with no qualification = no-show rate >40% and burned operator time. Always run gate.

## Cross-references

- `reference/frameworks/dm/intent-taxonomy.md` — full taxonomy doc with classifier logic
- `reference/frameworks/dm/cold-sequence-architecture.md`
- `reference/frameworks/dm/warm-sequence-architecture.md`
- `reference/frameworks/dm/liked-post-sequence-architecture.md`
- `reference/frameworks/dm/comment-for-lead-magnet-sequence-architecture.md`
- `reference/frameworks/dm/qualification-thread-design.md`
- `reference/frameworks/dm/booking-gate-construction.md`
- `reference/swipe-file/dm-threads/` — annotated examples per class
- `reference/templates/va-daily-dm-playbook.md`
- `INVARIANTS.md` N-5, N-12, A-13 — outbound rules
- `skills/source-lead-list/SKILL.md` — list-build skill
- `skills/classify-dm-intent/SKILL.md` — runtime classifier
- `skills/write-dm-sequence/SKILL.md` — per-class sequence writer
- `skills/build-application/SKILL.md` — application form (booking gate)

## Examples

See `examples/`:
- `example-agency-outbound-engine-hybrid.md` — engine for the agency's own outbound
- `example-client-outbound-engine-saas-founder.md` — engine for a SaaS-founder client account
- `example-client-outbound-engine-services-founder.md` — engine for a services-founder client account

---

*Skill version 1.0.0 — 2026-05-03*
