---
name: no-show-recovery
slug: /no-show-recovery
agent: call-prep-specialist
department: acquisition
output_format: no-show-recovery-sequence
required_profiles:
  call_record_and_reason: exists          # primary input — runtime artifact, not a profile
allowed_scopes:
  - single-prospect                       # one prospect, one no-show event
output_path: outputs/{date}/no-show-recovery/{prospect-slug}.md
---

# /no-show-recovery — No-Show Re-Engagement Sequence

> **Purpose.** When a `Booked → CallHeld` transition fails (prospect didn't show), this skill produces a context-classified re-engagement sequence. Sequence shape varies sharply by no-show context: a first no-show with no notice is most likely a calendar conflict (warm tone, easy reschedule); a second no-show is statistically a ghost (direct tone, final exit-rampup); a proactive cancel WITH a stated reason is a different conversation entirely (respect the reason, route accordingly — sometimes that means archive cleanly, sometimes that means a single-message redirect).
>
> **Bar.** A first no-show recovered cleanly converts to a held call ~50% of the time when handled in the right tone. Mishandled (guilt-trip tone, generic "missed you" template, or radio silence), it converts <10%. The differential is the difference between $5K-$25K/mo retainer recovered vs. archived. The second-no-show recovery rate is lower (~15%) but worth pursuing once with a final exit-rampup that respects the prospect's time.
>
> **The 1-of-1 primitive.** Most agencies run one "missed you" template for every no-show. This skill runs **three differentiated sequences** keyed to no-show context — and respects the auto-archive trigger after 2+ no-shows per the FSM.

## When to invoke

- **Auto-triggered** by `call.no-show` event on every `Booked → NoShow` transition (per `workflows/divisions/acquisition.md` and `paperclip.manifest.yaml`)
- Auto-triggered when calendar tooling registers a `meeting.cancelled` event ≥2 hours before scheduled start (proactive-cancel branch)
- Manual invoke if the operator marks a no-show retroactively (e.g., calendar tool didn't fire the webhook)
- Never invoke without a reason field populated — runtime sets it to `unknown` if not surfaced; that's still a valid input

## Required inputs

1. **Call record** — the booked call's metadata (date, time, scheduled duration, scheduling tool, original booking source)
2. **No-show context** — categorical: `first-no-show` | `second-no-show` | `third-or-later-no-show` | `proactive-cancel-with-reason` | `proactive-cancel-no-reason`
3. **Reason field** — free-text if prospect provided one (cancellation note, email, DM); `unknown` if no notice given
4. **DM thread history** — the prior thread leading to the booking, for tone-continuity in the recovery messages
5. **Application form data** — the qualification answers, used to confirm fit-status before deciding how hard to recover
6. **Reschedule link** — operator's calendar with auto-conflict-check (re-using the original booking link is acceptable; a fresh link with refreshed availability is better)
7. **Archive trigger config** — from `company.yaml.acquisition_engine` — currently the FSM auto-archives on 2+ no-shows per `workflows/divisions/acquisition.md`

## The build sequence

### Step 1 — No-show context classification (3 min)

Classify the no-show into one of five contexts. The classification drives every downstream decision.

```yaml
no_show_classification:
  context: ""                             # first-no-show | second-no-show | third-or-later | proactive-cancel-with-reason | proactive-cancel-no-reason
  prospect_no_show_count_lifetime: 0      # 1 = first-no-show; 2 = second; 3+ = third-or-later
  notice_given: ""                        # NONE (just didn't show) | LATE (<2hr before) | EARLY (>2hr before)
  reason_provided: ""                     # verbatim if given; "unknown" if not
  reason_classification: ""               # genuine-conflict (work emergency, family) | timing-shift (not now) | fit-doubt (raised concern) | ghosted (no message ever) | other
  application_status: ""                  # was this prospect fit-strong, fit-soft, or operator-overrode-DQ
```

The five contexts and what they imply:

| Context | What it usually means | Recovery posture |
|---|---|---|
| **first-no-show, no notice** | Calendar conflict, forgot, one-off | Warm + reschedule, no guilt, assume non-malicious |
| **first-no-show, late notice** | Genuine conflict, prospect was respectful | Warm + reschedule, mirror the apology, keep it light |
| **first-no-show, early notice** | Functional cancel — usually = proactive-cancel branch | Route to proactive-cancel sub-branch |
| **second-no-show** | Pattern emerging — likely ghosting, possibly disorganized | Direct + one final exit-rampup, no third attempt |
| **third-or-later** | Auto-archive per FSM | No sequence — archive with reason logged |
| **proactive-cancel-with-reason** | Prospect engaged enough to communicate | Respect reason + route accordingly (resource, redirect, or clean archive) |
| **proactive-cancel-no-reason** | Prospect cooled but bothered to cancel | Single warm bow-out with door-open, no re-pursue |

### Step 2 — Sequence template selection (3 min)

Three primary templates, each with sub-variations. The skill selects one based on Step 1 classification.

```yaml
template_selected:
  template_class: ""                      # template-A-warm-reschedule | template-B-final-exit-rampup | template-C-respect-the-reason
  sub_variation: ""                       # specific sub-variation per classification
  rationale: ""                           # 1 sentence on why this template was selected
```

**Template A — Warm Reschedule** (used for first-no-show variations):
- Sub A1: first-no-show, no notice
- Sub A2: first-no-show, late notice (mirror apology)
- Sub A3: first-no-show, prospect signaled high-fit pre-call (slightly warmer + offers operator personal assist with rescheduling)

**Template B — Final Exit Rampup** (used for second-no-show):
- Sub B1: second-no-show, original fit-strong (one final reach with named gravity)
- Sub B2: second-no-show, original fit-soft (one polite door-close, faster archive)

**Template C — Respect the Reason** (used for proactive-cancel-with-reason):
- Sub C1: reason = genuine-conflict → reschedule offer with extended availability
- Sub C2: reason = timing-shift → archive with named re-engagement trigger ("when {trigger} happens, here's the message")
- Sub C3: reason = fit-doubt → route to lower-friction resource (case study, DFY mini-explainer, NOT another call invite)
- Sub C4: reason = other (vague) → single soft re-open + door-close

**Special: proactive-cancel-no-reason** uses a one-message variant of Template C with no specific sub-routing — it's a polite acknowledgment with an open door, no call re-pursue.

### Step 3 — M1 architecture per context (5 min)

M1 is the highest-leverage message — get it wrong and the sequence is dead before M2. Architecture varies sharply by template.

**Template A M1** (warm reschedule, 2-4 sentences): tone warm-casual, zero guilt; assume calendar conflict, never disinterest; reference the prospect's last verbatim phrase from the DM thread (continuity); CTA = single reschedule link with 2-3 visible time blocks. Skeleton: brief greeting → "Calendar bombs happen — fresh link below" → link with time blocks → "If now isn't the window, hit me back when it is."

Template-A banned phrases (additive to `spec/BANNED-VOCABULARY.md`): "missed you" (passive-aggressive), "noticed you didn't make it" (surveillance coding), "reaching out again" (generic), "in case you forgot" (condescending), "wanted to check in" (vague).

**Template B M1** (final exit rampup, 3-5 sentences): tone direct + respectful + named gravity, no over-pursuit; reference the original trigger from `/build-call-prep` (the WHY they booked); assume the gap is closing — this is a final genuine reach; CTA = single ask with clear final-attempt framing. Skeleton: direct opener → "When we connected you mentioned {verbatim trigger}. That's still real — the gap doesn't close by itself." → "I've blocked {time block}. If timing's wrong, that's a clean signal too." → "Either way I won't keep pinging."

Template-B banned phrases: "third time's the charm" (weak humor), "just one more attempt" (desperate), "I don't want to bug you" (actually bugging them), "is everything ok?" (parental, presumptuous).

**Template C M1** (respect the reason, 2-4 sentences): tone mirrors the reason given, no agenda override; reference the prospect's stated reason verbatim if quoted; assume the prospect is being honest. CTA varies by sub-variation:
- Sub C1 (genuine conflict): extended-availability reschedule link, no time-pressure
- Sub C2 (timing-shift): archive cleanly + name the future trigger that should re-open
- Sub C3 (fit-doubt): lower-friction resource (case study, recorded mini-explainer) — NEVER another call invite
- Sub C4 (vague): single soft re-open + clean door-close

### Step 4 — Cadence per context (3 min)

Cadence drives whether the sequence converts or annoys. The defaults:

| Template | M1 timing | M2 timing | M3 timing | Total messages |
|---|---|---|---|---|
| **Template A — Warm Reschedule** | Same day, within 4 hours of no-show window end | +48 hours if no reply | +7 days if still no reply (final door-open) | 3 |
| **Template B — Final Exit Rampup** | +24 hours after no-show | +7 days if no reply (final close) | none | 2 |
| **Template C — Respect the Reason** | Within 24 hours of cancellation | none (single message, by design) | none | 1 |

Cadence rationale:
- Template A has a 3-message arc because first no-shows are statistically calendar-driven and rescheduling has highest yield within the first week
- Template B caps at 2 messages because pursuit beyond that crosses from "respectful gravity" to "desperate" — and the FSM auto-archives anyway
- Template C is single-message because the prospect already gave you a signal; pursuing past that signal is disrespectful

```yaml
sequence_cadence:
  template_class: ""                      # from Step 2
  messages:
    - message_number: 1
      timing: ""                          # specific, e.g. "within 4 hours of no-show window end"
      branch_if_reply: ""                 # what state transition the reply triggers
      branch_if_no_reply: ""              # next message OR archive
    - message_number: 2                   # if applicable
      timing: ""
      branch_if_reply: ""
      branch_if_no_reply: ""
    - message_number: 3                   # if applicable
      timing: ""
      branch_if_reply: ""
      branch_if_no_reply: "archive"       # final messages always end in archive
```

### Step 5 — Auto-archive triggers (2 min)

Per `workflows/divisions/acquisition.md`: `NoShowRecovery → archive` on 2+ no-shows. This skill respects that hard rule.

```yaml
auto_archive_check:
  prospect_no_show_count_lifetime: 0
  archive_threshold: 2                    # FSM-defined
  current_invocation_triggers_archive: false  # true only on 3rd+ no-show
  archive_action_if_triggered:
    - state_transition: "Booked → archive"
    - reason_logged: "auto-archive: 2+ no-shows per FSM"
    - re_engagement_eligible_after_days: 90
    - re_engagement_signal: ""            # named trigger that would unblock — e.g., "if they comment on a future post or DM us proactively"
```

The skill does not bypass the archive trigger. If a prospect hits 2+ no-shows and the operator wants to re-engage anyway, that's a manual override logged per the override protocol in `spec/CONTEXT-THRESHOLDS.md`.

### Step 6 — State-machine integration (2 min)

The skill emits explicit FSM state-transition recommendations. Per `INVARIANTS.md` A-13, the operator confirms — but for simple no-show flows, the operator's confirmation is one-click and usually rubber-stamps the recommendation.

State transitions emitted:

```yaml
state_transitions_recommended:
  - branch: "M1 sent, awaiting reply"
    from_state: "NoShow"
    to_state: "NoShowRecovery"
    automatic: true
  - branch: "Reply received with reschedule confirmation"
    from_state: "NoShowRecovery"
    to_state: "Booked"
    automatic: false                      # operator confirms — re-runs /build-call-prep on confirmation
  - branch: "No reply through end of cadence"
    from_state: "NoShowRecovery"
    to_state: "archive"
    automatic: true
    reason_logged: "no-reply through recovery sequence"
  - branch: "Auto-archive triggered (2+ no-shows)"
    from_state: "NoShow"
    to_state: "archive"
    automatic: true
    reason_logged: "2+ no-shows per FSM auto-archive"
```

When recovery succeeds and a reschedule is confirmed, the workspace re-runs `/build-call-prep` for the new booking — refreshing the trigger event and case-study selection in case anything has changed since the original booking.

### Step 7 — Output emission

The skill emits the sequence + reschedule link + state transition recommendation as a single bundle for operator review.

```yaml
delivery_bundle:
  - sequence_messages_drafted             # 1-3 messages depending on template
  - reschedule_link_with_availability     # if applicable per template
  - state_transition_recommendation       # one-click confirmation per A-13
  - voice_calibration_check_result        # passes | fails — must match operator/agency voice profile
  - banned_phrases_check_result           # passes | fails per Step 3
delivery_target_window: "<60 minutes from no-show event"
delivery_channel: "operator preferred — Slack DM | email | workspace UI"
```

Voice calibration pulls from `brand_voice.agency_voice` (or `brand_voice.client_voices[slug]` for client-call scope, though no-show recovery for client-call is rare and out-of-default-scope per the allowed_scopes declaration). Banned-phrase check enforces both `spec/BANNED-VOCABULARY.md` and the no-show-specific banned list from Step 3.

## Validation gate (mandatory before declaring done)

Three checks:

1. **The tone-context match test.** The selected template's tone must match the no-show context. A Template B (direct, gravity-named) sent for a first-no-show calendar conflict would feel hostile; a Template A (warm, easy reschedule) sent for a third-or-later ghost would feel desperate. Validation re-checks Step 1 → Step 2 mapping before delivery.

2. **The banned-phrase test.** Both the global banned vocabulary AND the no-show-specific banned phrases (from Step 3 per template) must clear. A "missed you" in Template A or a "third time's the charm" in Template B is hard-block.

3. **The FSM-respect test.** If `prospect_no_show_count_lifetime ≥ 2`, the skill MUST emit an auto-archive recommendation, NOT a recovery sequence. Operator override is logged. Bypassing the archive silently is not allowed.

## Output format

```yaml
---
artifact_type: no-show-recovery-sequence
prospect_slug: ""                         # anonymized
client_slug: null | "<slug>"
no_show_context: ""                       # from Step 1
template_selected: ""                     # from Step 2
sequence_message_count: 0                 # 0 (auto-archive) | 1 (template C) | 2 (template B) | 3 (template A)
auto_archive_triggered: true | false
voice_calibration_check: passed | failed
banned_phrase_check: passed | failed
fsm_respect_check: passed | failed
profiles_used: [brand_voice.agency_voice, ideal_customer_profile]
frameworks_used: [no-show-classification, sequence-template-matrix, M1-architecture-by-context]
confidence: HIGH | MEDIUM | LOW
gap_flags: []
---

## No-show context summary
{1 paragraph — what happened, what classification was applied, why this template was selected}

## Sequence messages drafted
{M1 (always), M2 (if applicable), M3 (if applicable) — formatted ready-to-send}

## Reschedule link
{calendar URL with refreshed availability — if applicable per template}

## State transition recommendation
{from Step 6 — operator one-click confirms}

## Auto-archive notice
{only if 2+ no-shows lifetime — explicit notice that recovery is bypassed per FSM}

## Validation gate result
{tone-context-match test, banned-phrase test, FSM-respect test}

## Refinement signals queued
{if pattern of no-shows from a specific lead source surfaces, route signal to /source-lead-list for list-quality refinement | if pattern of fit-doubt cancels surfaces, route to /build-application for qualification thread refinement}
```

## Failure modes and guards

- **Wrong template selected.** Template B sent to a first-no-show (calendar conflict) reads as accusatory. Template A sent to a third-no-show reads as oblivious. Guard: validation Step 1 (tone-context match test) is hard-block. Mis-classification is the most common failure mode.
- **Banned-phrase leak.** "Missed you" / "circle back" / "checking in" feels generic and lowers reply rate. Guard: Step 3 banned-phrase list + global `spec/BANNED-VOCABULARY.md` enforced at delivery.
- **Bypassing auto-archive.** Pursuing past 2 no-shows wastes operator focus on prospects who have signaled disengagement twice. Guard: FSM-respect test (validation Step 3) blocks recovery sequence on 3rd+ no-show; operator override is logged.
- **Generic reschedule link.** A calendar link with the same availability the prospect already missed feels lazy. Guard: link must include refreshed availability with multiple time-block options visible inline.
- **Voice contamination.** When run for `client-call` scope (rare per allowed_scopes), the messages must use the client's voice. Guard: scope check at Step 7; voice-calibration check gates send.
- **Pursuit-after-clear-decline.** Sub C2 (timing-shift) and Sub C4 (vague) require respecting the prospect's stated cool-down. Sending M2 against a clearly-signaled cancel = trust damage + brand confusion. Guard: Template C is single-message by design; no automatic M2 even on no-reply.
- **Late delivery.** Recovery sequence M1 sent 24 hours after the no-show window has passed has half the conversion rate of one sent within 4 hours. Guard: target-window declared; runtime adapter monitors and escalates if delivery slips.
- **Ignoring proactive-cancel reason content.** When the prospect provides a reason ("conflict came up at work" vs "actually I'm reconsidering"), the response must mirror that reason — not template through it. Guard: Step 1 reason_classification routes to specific Template C sub-variation; classification mismatch fails validation.

## Cross-references

- `agents/acquisition-head.md` — owner of NoShow → NoShowRecovery transition
- `agents/call-prep-specialist.md` — agent persona for this skill
- `workflows/divisions/acquisition.md` — FSM: `NoShow → NoShowRecovery → Booked | archive`
- `INVARIANTS.md` A-13 (operator confirms transitions), N-9 (no "final" language in artifacts), N-12 (application required — relevant for re-routing if recovery re-opens conversation)
- `spec/CONTEXT-THRESHOLDS.md` — `/no-show-recovery` threshold row
- `spec/BANNED-VOCABULARY.md` — global banned vocabulary
- `paperclip.manifest.yaml` — `call.no-show` and `meeting.cancelled` webhook triggers
- `skills/build-call-prep/SKILL.md` — re-runs on successful reschedule (refreshes trigger + case study)
- `skills/build-application/SKILL.md` (Cycle 2 — TBD) — supplies the application-status input for fit-strong vs fit-soft branching
- `skills/build-outbound-engine/SKILL.md` — supplies the DM thread history for tone-continuity
- `reference/frameworks/sales/no-show-recovery-architecture.md`
- `reference/templates/no-show-recovery-templates.md` — the three primary templates with sub-variations
- `reference/swipe-file/dm-threads/no-show-recovery/` — annotated past recovery threads

## Examples

See `examples/`:
- `example-recovery-first-no-show-warm-reschedule.md` — Template A1, calendar conflict, recovered to held call
- `example-recovery-second-no-show-final-exit.md` — Template B1, fit-strong original, one final gravity-named reach
- `example-recovery-proactive-cancel-genuine-conflict.md` — Template C1, work emergency, extended-availability reschedule
- `example-recovery-proactive-cancel-fit-doubt.md` — Template C3, prospect raised concern, routed to case-study resource
- `example-recovery-auto-archive-third-no-show.md` — FSM auto-archive, no sequence, reason logged

---

*Skill version 1.0.0 — 2026-05-03*
