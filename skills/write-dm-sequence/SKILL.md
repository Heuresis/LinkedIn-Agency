---
name: write-dm-sequence
slug: /write-dm-sequence
agent: dm-strategist
department: acquisition
output_format: dm-sequence
required_profiles:
  outbound_engine_spec: exists              # from /build-outbound-engine — sequence shapes, volumes, voice constraints
  intent_class: declared                     # cold | warm | liked-post | comment-magnet — declared at invocation
allowed_scopes:
  - cold                                    # Class C — sourced from list, no prior interaction
  - warm                                    # Class W — interacted with content in last 30d
  - liked-post                              # Class L — liked a specific high-intent post
  - comment-magnet                          # Class M — commented trigger word on CTA post
output_path:
  default: outputs/{date}/dm-sequences/{class}-{slug}.md
---

# /write-dm-sequence — Per-Intent-Class DM Sequence Writer

> **Purpose.** Write the full DM sequence for a single declared intent class — every message draft, every reply branch, every no-reply tail, every personalization slot, every voice-calibration check. Ships ready to run in the inbox, with reply-branch routing back into the acquisition FSM.
>
> **Bar.** A VA running this sequence cold, with no prior context beyond the spec, must hit the per-class reply-rate baseline from `reference/frameworks/dm/intent-taxonomy.md` within 30 days. Verified by 7-day dry-run.
>
> **The 1-of-1 primitive.** Most agencies hand a VA "the cold sequence" — one sequence for everything. This skill produces *four structurally distinct sequences*, one per intent class, each calibrated to that class's reply-rate baseline, decay window, and conversion physics. Sending a class-C sequence to a class-L prospect is the single most common reason DM operations stall.

## When to invoke

- After `/build-outbound-engine` produces the engine spec and per-class sequences need populating (run 4× — one per class)
- A per-class reply rate falls 30% below baseline for 14 consecutive days (re-write that class)
- Quarterly refresh per class (LinkedIn detects template repetition; refresh M1-M3 every 90d)
- A new offer launches and existing sequences funnel toward the wrong outcome
- Per-client outbound: client signs and client's engine needs class-specific sequences (`client-outbound` scope)

## Required inputs

1. **The intent class** — declared at invocation. Skill blocks if not declared per `INVARIANTS.md` N-5.
2. **The outbound engine spec** at `company.yaml::acquisition_engine.{scope}`. Declares voice, volumes, qualification-thread shape.
3. **The ICP profile** at the right scope (agency or per-client). Voice-of-customer language is load-bearing for personalization slots.
4. **The voice profile** at the right scope. Sequence must read in the operator's (or client's) voice.
5. **The offer the sequence funnels toward** — call invite names the outcome of THIS offer, not generic.
6. **Past DM data for this class** (if available) — top-5 reply-getting M1s + bottom-5 silent M1s.
7. **Per-client subprofile** if scope is client-outbound — recursive context per A-6.

## Per-class sequence physics (the load-bearing primitive)

| Class | Length | Cadence | Span | M1 anatomy | Reply-rate target | Decay window |
|---|---|---|---|---|---|---|
| **C — Cold** | 5-7 msgs | 2-4d between | 14-21d | Pattern-interrupt opener → trigger-event personalization → 1-line pain hypothesis in ICP voice → "wrong?" close | 8-15% | None |
| **W — Warm** | 3-5 msgs | 3-4d between | 7-14d | Name-the-relationship opener → specific-engagement reference → light qualifier tied to topic → no pitch | 25-45% | 30d |
| **L — Liked-post** | 2-4 msgs | M1 <24h, fastest | 3-7d | Compliment-with-specific → direct reference to post + takeaway → 1-question qualifier → skip pleasantries | 50-70% (L+comment compound: 80%+) | 5-7d |
| **M — Comment-magnet** | 3-5 msgs | M1 <1h (auto), M2 +2d, M3 +4d, M4 +7d, M5 +10d | 5-10d | Magnet delivery only — no qualifier, no pitch, no preamble. Honor the contract. | 80-95% | 24h M1 deadline |

Voice constraints: C = casual-direct, no pitch in M1-M3. W = specific (name the post by topic). L = direct, skip pleasantries. M = value first, qualify second; inverting destroys trust.

## The build sequence

### Step 1 — Class declaration + ICP scope (3 min)

Confirm invocation parameters. Halt if missing per N-5.

```yaml
sequence_build_inputs:
  intent_class: cold | warm | liked-post | comment-magnet    # mandatory
  scope: agency-outbound | client-outbound
  client_slug: null | "<slug>"                               # required if scope=client-outbound
  outbound_engine_spec_path: company.yaml::acquisition_engine.{scope}
  icp_profile_path: ideal_customer_profile.{agency_icp | client_icps[slug]}
  voice_profile_path: brand_voice.{agency_voice | client_voices[slug]}
  target_offer_slug: ""
  past_dm_data_available: true | false
```

If `intent_class` is null → halt with `INVARIANT_VIOLATION_N5`. If `scope=client-outbound` and `client_slug` is null → halt with `RECURSIVE_CONTEXT_MISSING` per A-6. If recursive subprofile is below threshold (e.g., `client_icps[slug] < 75`) → halt with `SKILL_BLOCKED` and recommend `/build-icp` for that client.

### Step 2 — M1 architecture (the highest-leverage message — class-specific anatomy) (15 min)

M1 carries 70%+ of sequence leverage. M2-Mn are physics; M1 is where personalization, voice, and intent-match converge.

**Cold M1** (per `(Cycle 2 — TBD)` cold-sequence-architecture.md):
- L1: First-name + 4-7 word context anchor (operator's actual phrase from voice profile)
- L2-3: Trigger-event personalization — must reference one specific thing from prospect's profile or last 30d activity (post, role change, hire, raise, comment, milestone). NOT firmographics.
- L4-5: One-sentence pain hypothesis in ICP's verbatim voice-of-customer language. Pulled from `ideal_customer_profile.*.voice_of_customer.pain_language_patterns`. Never paraphrase.
- L6: Soft-DQ close — "wrong?" / "no need to reply if it's a miss." Removes ask burden.

**Warm M1** (per `(Cycle 2 — TBD)` warm-sequence-architecture.md):
- L1: Name-the-relationship — "saw your comment on the {specific post}" / "thanks for the like on {specific post}"
- L2-3: Specificity payload — name the post by topic, name the comment by what it said, name the profile-view by date. Specificity is the trust currency.
- L4-5: Light qualifier tied to engagement topic — "the thing in the {post} about {takeaway} — does that map to anything you're running into?"
- No pitch, no call invite, no magnet. Just the question.

**Liked-post M1** (per `(Cycle 2 — TBD)` liked-post-sequence-architecture.md):
- L1: Compliment-with-specific — "saw the like on the {post about specific takeaway}"
- L2-3: 1-sentence bridge from post takeaway to prospect's likely situation
- L4: Direct 1-question qualifier — "does {takeaway} map to {situation}?"

**Comment-magnet M1** (per `(Cycle 2 — TBD)` comment-for-lead-magnet-sequence-architecture.md):
- L1: Acknowledge comment + name magnet — "Thanks for commenting {trigger word} — here's the {magnet name}."
- L2: The magnet itself.
- L3: One sentence on how to use it ("the part on page X is where most operators find the wedge").
- That is the entire M1. No qualifier. No pitch. Honor the contract.

Output: M1 draft (verbatim) + the 3-5 personalization slots that must be hand-filled per prospect.

### Step 3 — M2-Mn cadence (per-class message count + spacing + content beats) (20 min)

For the declared class, build the rest per the physics table.

**Cold M2-M7:**

| M# | Day | Purpose | Content beat |
|---|---|---|---|
| 2 | +4 | Soft-DQ angle | "Most {role}s I work with don't have {specific symptom}. Curious if that's you?" |
| 3 | +8 | Value-give | One specific insight or one free resource. No ask. |
| 4 | +13 | Soft-DQ + offer intro | "If {symptom} is true, this is the kind of thing we do" — link to ONE case study, not the offer page |
| 5 | +18 | Call invite | Direct ask, named outcome, time-boxed (20 min) |
| 6 | +22 (opt) | Bow-out + door-open | "I'll stop pinging — if {trigger} ever happens, here's the reply that gets you to the front" |
| 7 | +60 (opt) | Reactivation | Single re-open with new angle if engagement signal surfaced |

**Warm M2-M5:**

| M# | Day | Purpose | Content beat |
|---|---|---|---|
| 2 | +3-4 | Brief value-give tied to engagement topic | One specific insight extending the post they engaged with |
| 3 | +7 | Soft call invite | "Worth 20 min to dig into {specific topic}?" |
| 4 | +12 (opt) | Door-open bow-out | Brief, no-pressure, leaves the door open |

**Liked-post M2-M4:**

| M# | Day | Purpose | Content beat |
|---|---|---|---|
| 2 | +2-3 | If reply: confirm fit, propose 20-min call. If no reply: short value drop tied to original post. |
| 3 | +5-7 | Bow-out + door-open + reference to original post |

**Comment-magnet M2-M5:**

| M# | Day | Purpose | Content beat |
|---|---|---|---|
| 2 | +2 | Consumption check | "Did the {magnet} land okay? Anything stick out?" |
| 3 | +4 | Qualification bridge | If reply: "What part of {magnet topic} are you working on right now?" If no reply: short value extension. |
| 4 | +7 | Call invite tied to magnet's topic | "The {magnet} shows the framework. The 20-min call shows which parts apply to your {situation}." |
| 5 | +10 | Door-open bow-out | "I'll stop pinging — DM 'WAIT' if you want me to circle back when {trigger}" |

For each M#: full draft + voice-calibration check (matches ICP voice-of-customer? matches operator/client voice profile?) + reply-rate target.

### Step 4 — Reply branching (every message routes through `/classify-dm-intent` on reply) (10 min)

Every message has two branches:

```yaml
- message_number: N
  branches:
    if_reply:
      action: pause_sequence
      route: /classify-dm-intent                     # re-classify the thread
      rationale: "Reply state changes the prospect's class — re-classify before continuing"
      next_state: Replied (per acquisition FSM)
    if_no_reply:
      action: continue_sequence
      next_message: M(N+1)
      next_send_after_days: <per-class cadence>
```

Critical rule: a reply at any sequence position never auto-advances. The reply re-routes through `/classify-dm-intent` per `workflows/divisions/acquisition.md` (Replied → Qualifying state). Pre-written sequence is for *no-reply* progression only. Why: reply may indicate class promotion (C → W after engagement), an objection, a hard-DQ trigger, or buy signal. Auto-advancing past a reply blows the conversion.

Output: per-message branch map showing if_reply / if_no_reply routes for every M1-Mn.

### Step 5 — No-reply tail (sequence end + transition to NoReply state per acquisition FSM) (5 min)

After the final message:

```yaml
no_reply_tail:
  trigger: "M{final}+{cadence_days} elapsed with zero replies"
  state_transition: InThread → NoReply
  actions:
    - log_to: outputs/{date}/dm-sequences/{class}-{slug}-final.md
    - tag_in_crm: "no-reply-{class}-{date}"
    - schedule_reactivation: 60-day re-engagement check (only fires on fresh engagement signal)
  archive_decision:
    - if no engagement signal in 60d → archive (NoReply → [*])
    - if engagement signal in 60d → re-classify via /classify-dm-intent → new sequence per new class
```

Per-class reactivation defaults: Cold = 60d. Warm = 30d. Liked-post = none (signal was post-specific). Comment-magnet = none (magnet was the conversion event).

### Step 6 — Banned-vocab sweep across every message (5 min)

Run every message through the banned-vocab linter per `spec/BANNED-VOCABULARY.md` and `INVARIANTS.md` N-4. Non-negotiable.

DM-specific bans to flag: "Quick question..." opener, "I'll be honest...", "Just wanted to..." / "Just reaching out...", "I hope this finds you well.", "I'd love to..." (begging cadence), "10X your..." / "skyrocket your..." / "transform your...", "🚀 / 💯 / 🔥" standalone visual-spam, generic "We help {role}s achieve {outcome}", any phrase pattern-matching a comment-bot, any opener beginning with an emoji.

A match returns `BANNED_VOCABULARY_DETECTED` with phrase + message number. Halt and rewrite. Re-sweep until clean. Output: linter report + clean-pass confirmation.

### Step 7 — Personalization slot audit (every message ≥1 personalization slot — never raw template) (5 min)

Per N-5 and the cold-M1-templated failure mode in `intent-taxonomy.md`: every message must have at least one personalization slot requiring per-prospect input. Pure templates pattern-match comment-bots and depress reply rate ~50%.

Slot types accepted:
- **Profile-data slot:** verbatim from prospect's headline, recent post, role, milestone, hire announcement, raise announcement
- **Engagement-reference slot:** the specific post / comment / takeaway
- **Trigger-event slot:** the specific event (raised, posted about hiring SDR, replaced agency, etc.)
- **Magnet-context slot:** for class M, the specific part of the magnet mapping to their stated situation

Slot count minimums per class: Cold M1 = 2+ slots. Warm M1 = 2+ slots. Liked-post M1 = 2+ slots. Comment-magnet M1 = 1 slot. M2-Mn (all classes) = 1+ slot each.

If any message fails minimum → halt and re-write. Output: slot-audit table per message (count, types, sample filled / sample blank).

### Step 8 — Output emission (sequence YAML + per-message draft + reply-branch map) (5 min)

Hydrate and write to `outputs/{date}/dm-sequences/{class}-{slug}.md`.

```yaml
sequence:
  intent_class: <class>
  scope: <agency-outbound | client-outbound>
  client_slug: <null | slug>
  funnels_to_offer: <offer_slug>
  total_messages: <N>
  total_span_days: <N>
  reply_rate_target: <baseline from intent-taxonomy>
  decay_window_days: <per-class>
  messages:
    - message_number: 1
      send_after_days: 0
      message_type: "<pattern-interrupt | name-the-relationship | compliment-with-specific | magnet-delivery>"
      template: |
        <full draft with [PERSONALIZATION_SLOT_NAME] markers>
      personalization_slots:
        - { slot_name, slot_type, source_field }
      voice_calibration_notes: ""
      voice_of_customer_phrases_used: []
      branches:
        if_reply: { action: pause_sequence, route: /classify-dm-intent, next_state: Replied }
        if_no_reply: { action: continue_sequence, next_message: 2, next_send_after_days: <cadence> }
      banned_vocab_check: passed
    # ... M2 through M{N}
  no_reply_tail:
    final_state: NoReply
    archive_after_days: <60 | 30 | none>
    reactivation_eligibility: <conditions>
```

## Validation gate (mandatory before declaring done)

Three checks before the sequence ships:

1. **Voice-of-customer match.** Pull 5 verbatim phrases from ICP `voice_of_customer.pain_language_patterns` (or `desire_language_patterns` for warm/liked-post). At least 3 of 5 must appear verbatim across M1-M3. Below 3 → re-run Step 2 with ICP profile open.

2. **7-day dry-run.** Send M1 to 25-50 ICP-matching prospects in this class. At day 7, reply rate must be within 30% of baseline (cold ≥6%, warm ≥18%, liked-post ≥35%, comment-magnet ≥56%). Below floor → diagnose and re-write M1.

3. **Voice-drift check (client-outbound only).** Per N-2: run `/voice-drift-detector` on M1-M3 against client voice profile. Below client's `voice_drift_threshold` → halt and re-write.

## Output format

```yaml
---
artifact_type: dm-sequence
intent_class: cold | warm | liked-post | comment-magnet
scope: agency-outbound | client-outbound
client_slug: null | "<slug>"
funnels_to_offer: "<offer_slug>"
build_date: YYYY-MM-DD
validation_gate: passed | failed | not-yet-run
profiles_used: [ideal_customer_profile, brand_voice, product_strategy]
frameworks_used: [intent-taxonomy, <class-specific-architecture>]
banned_vocab_lint: passed | failed
personalization_slot_audit: passed | failed
confidence: HIGH | MEDIUM | LOW
gap_flags: [list]
---

{populated sequence YAML from Step 8}

## Per-message drafts
{full text M1 through M{final} with personalization markers visible}

## Reply-branch map
{table of every if_reply / if_no_reply route across all messages}

## VA-facing send playbook
{daily operating procedure for the VA running this class — when to send, how to fill slots, how to flag replies}

## Validation gate result
{voice-of-customer match + 7-day dry-run + voice-drift check (if client-outbound)}

## Next actions
{schedule dry run, tag prospects in CRM with class, deploy week-1 sends, schedule day-7 reply-rate review}
```

## Failure modes and guards

- **Wrong-class-for-prospect.** Cold sequence to class-L = warmest signal squandered. Guard: invocation requires explicit class declaration; classifier (`/classify-dm-intent`) decides class. Per N-5.
- **Templated M1.** Cold M1 with `{first_name}` + stock pain hypothesis = 2-4% reply rate. Guard: Step 7 personalization-slot audit + slot count minimums.
- **Pitch in M1 (cold/warm).** Adding offer in M1 collapses reply rate. Guard: M1 anatomy excludes pitch; M3-M5 carry the bridge.
- **Magnet-class qualification before delivery.** "Before I send this, can I ask…" breaks trust. Guard: Class-M M1 is delivery-only.
- **Liked-post lag.** M1 sent 4 days after like = 0% reply rate (signal decayed). Guard: liked-post cadence enforces M1 within 24h, ideally <6h.
- **Voice contamination (client-outbound).** Agency voice creeps into client sequences. Guard: voice-drift check in validation gate per N-2.
- **Auto-advance past reply.** Continuing pre-written sequence after reply kills conversion. Guard: Step 4 forces every reply to re-route through `/classify-dm-intent`.
- **Skipping no-reply tail.** Sequence ends with no archive + no reactivation = CRM leak. Guard: Step 5 mandatory; NoReply wired to FSM.
- **No banned-vocab sweep.** "I hope this finds you well" leaks in, signals template. Guard: Step 6 lint is a hard-block.

## Cross-references

- `reference/frameworks/dm/intent-taxonomy.md` — the four classes, baselines, classifier logic
- `reference/frameworks/dm/cold-sequence-architecture.md` `(Cycle 2 — TBD)` / `warm-sequence-architecture.md` `(Cycle 2 — TBD)` / `liked-post-sequence-architecture.md` `(Cycle 2 — TBD)` / `comment-for-lead-magnet-sequence-architecture.md` `(Cycle 2 — TBD)` — per-class anatomy deep-dives
- `reference/swipe-file/dm-threads/` — annotated examples per class
- `workflows/divisions/acquisition.md` — the FSM (InThread, Replied, NoReply states) this skill drives
- `INVARIANTS.md` N-2 (voice-drift), N-4 (banned vocab), N-5 (intent classification first), N-6 (no voice contamination), A-6 (recursive context), A-13 (operator-in-loop on hard DQs)
- `agents/acquisition-head.md` — owner of acquisition pipeline; routes sequences by intent class
- `skills/build-outbound-engine/SKILL.md` — engine spec this skill consumes
- `skills/classify-dm-intent/SKILL.md` — runtime classifier every reply routes through
- `skills/build-qualification-thread/SKILL.md` — post-reply thread after Replied → Qualifying transition
- `skills/voice-drift-detector/SKILL.md` — runs on client-outbound drafts
- `spec/BANNED-VOCABULARY.md` — lint list for Step 6
- `spec/CONTEXT-THRESHOLDS.md` — gating thresholds for ICP and voice profiles per scope

## Examples

See `examples/`:
- `example-cold-sequence-agency-outbound.md` — full 6-message cold sequence for agency outbound to B2B founders
- `example-warm-sequence-client-outbound-saas.md` — 4-message warm sequence for SaaS-founder client account
- `example-liked-post-sequence-agency.md` — 3-message liked-post sequence with sub-6h M1 cadence
- `example-comment-magnet-sequence-agency.md` — 5-message comment-for-magnet sequence with ManyChat M1 delivery

---

*Skill version 1.0.0 — 2026-05-03*
