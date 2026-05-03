# Cold Sequence Architecture (Class C)

> The structural rules for the longest, hardest, lowest-baseline DM sequence in the workspace — sourced from a list, no prior interaction, every message has to earn the next message.

## When to use (vs. other classes)

Class C is the default for prospects who have **never interacted** with the operator's (or client's) account or content and are sourced from a lead list — Sales Nav saved searches, Apollo exports, ICP databases, conference attendee lists.

**Use Cold sequence when:**
- Prospect was sourced from a list, not from an engagement signal
- Zero record of any prior interaction in last 90 days
- Engagement-recovery sequences (Warm, Liked-post, Comment-magnet) don't fit because no engagement event exists
- Volume needs to scale beyond what inbound + warm channels produce (typical: 60-75% of total DM volume runs through Class C)

**Do NOT use Cold sequence when:**
- Prospect engaged with content in last 30d → Class W (Warm)
- Prospect liked a tagged high-intent post in last 7d → Class L (Liked-post)
- Prospect commented a magnet trigger in last 24h → Class M (Comment-magnet)

Sending a Class C sequence to a Class W prospect compresses what would be a 30%+ reply rate to ~10% by treating an engaged contact as a stranger. Sending Class C to Class L is the largest single conversion-rate destroyer in this workspace per `intent-taxonomy.md`.

## The class definition (entry criteria)

A prospect enters Class C when **all** of the following hold:

1. **No interaction record** — `engagement_log[prospect_id]` is empty for last 90 days (no like, comment, profile view, prior reply)
2. **Sourced from a list** — `source_field` ∈ {sales_nav, apollo, icp_database, conference_export, manual_source}
3. **ICP cosine similarity ≥ 0.75** — passes the lead-list filter from `skills/source-lead-list/SKILL.md`
4. **Has a verifiable trigger event** in last 30 days — funding, role change, hire announcement, post about the operator's pillar topic, agency-replaced signal, or other timestamped event surfaced from profile data
5. **Connection accepted** OR open-DM-eligible (premium / open-profile) — sequence cannot start without a delivery surface

If criterion 4 (trigger event) cannot be sourced, the prospect is **deferred**, not added to Class C. Cold M1 without a trigger reference reads as templated and depresses reply rate by ~50%.

## Sequence anatomy (M1 → M7)

Total: 5-7 messages, 14-21 days span, 2-4 day cadence between messages. Every message has a distinct purpose. Never two consecutive messages with the same beat.

### M1 — Trigger-event opener (Day 0)

**Anatomy template:**
```
L1 — First-name + 4-7 word context anchor (operator's actual phrase from voice profile)
L2-3 — Trigger reference: "Saw {specific event} — {one-line specific observation about it}"
L4-5 — Pain hypothesis in ICP voice-of-customer language: "Most {role}s coming off {event} run into {specific symptom from VoC patterns}"
L6 — Soft-DQ close: "Wrong?" or "no need to reply if it's a miss"
```

**Anatomy rules:**
- Trigger reference is mandatory and specific — "saw your post on X" with X being the actual post title, NOT "I see you post about X"
- NO pitch in M1 — no offer mention, no calendar link, no case study link, no magnet
- Pain hypothesis must pull verbatim from `ideal_customer_profile.voice_of_customer.pain_language_patterns` — at least one phrase exact-match
- "Wrong?" close removes ask burden; lifts reply rate ~2x vs. open-ended close

### M2 — Soft-DQ angle (Day +4)

**Anatomy template:**
```
L1 — Reference back to M1 ("circling on what I said about {trigger}")
L2-3 — New-angle pain hypothesis: "Most {role}s I work with don't have {specific symptom}. Curious if that's you?"
L4 — Light qualifier embedded in the question — no calendar link
```

**Anatomy rules:**
- New angle, not a re-statement of M1 — if M1 was a trigger-event hypothesis, M2 is a peer-comparison hypothesis
- Soft-DQ phrasing ("most don't have…") lowers defense and surfaces the engaged subset
- Still no pitch

### M3 — Value-give (Day +8)

**Anatomy template:**
```
L1 — Brief context anchor
L2-4 — One specific framework idea, insight, or free resource — fully self-contained, no follow-up required
L5 — Implicit close: "{name of resource/idea} — figured it might be useful"
```

**Anatomy rules:**
- One thing only — a single framework idea, a single short observation, OR a single free resource link. Three things = noise.
- No CTA, no question, no ask
- When the prospect has replied to M2, M3 lands materially better as a 30-45 second voice note vs. text (voice still has scarcity on LinkedIn DMs in 2026, ~3% operator usage)

### M4 — Case study + soft offer intro (Day +13)

**Anatomy template:**
```
L1 — Bridge from value-give: "On the {M3 topic} — wanted to share one quick example"
L2-4 — Anonymized case study reference: "{role} we worked with had {specific symptom from M2} — {specific intervention} → {specific outcome with cited number}"
L5 — Soft frame: "If {symptom} is true for you, this is the kind of work we do" — link to ONE case study page (not the offer page)
```

**Anatomy rules:**
- Case study uses anonymized methodology descriptor per `INVARIANTS.md` A-11 — "the SaaS-procurement client," not the actual name
- Numbers cited must be in `case_studies.agency_case_studies[*]` with `proof_assets` per N-3 — never invented
- Link to case study page, NOT to offer page or booking link — the offer page is a different conversion event

### M5 — Peer-comparison + call invite (Day +18)

**Anatomy template:**
```
L1 — Peer-comparison frame: "The {role}s I see actually move on this fall into one of two camps — {camp A description} or {camp B description}"
L2-3 — Position the prospect: "If you're closer to camp B, the next move is usually a 20-min look at {specific named outcome}"
L4 — Application-gated invite: "Want to talk? Here's how it usually starts: {application link, NOT calendar link per N-12}"
```

**Anatomy rules:**
- Calendar link MUST be application-gated per `INVARIANTS.md` N-12 — never raw Calendly
- Named outcome = specific to the target offer's transformation hypothesis (per `retainer-architecture.md` Rule 1), not generic
- Time-boxed (20 min) — open-ended invites depress acceptance

### M6 — Bow-out with door-open (Day +22, optional)

**Anatomy template:**
```
L1 — Permission release: "I'll stop pinging — appreciate you letting me show up in the inbox"
L2-3 — Door-open trigger: "If {specific trigger event} ever happens, here's the reply that gets you to the front of the line: '{specific code phrase}'"
L4 — Sign-off in operator voice
```

**Anatomy rules:**
- Specific code phrase ("DM 'WAIT' if…") raises re-engagement rate vs. generic "feel free to DM"
- Specific trigger = something the prospect is likely to encounter (next funding, next agency-replacement decision, next quarter's planning) — generic triggers are forgotten

### M7 — Reactivation (Day +60, conditional)

**Anatomy template:**
```
L1 — New angle: "Saw {new trigger event in last 30d} — different angle than last time"
L2-3 — Fresh pain hypothesis tied to the new event
L4 — Soft re-open question
```

**Anatomy rules:**
- Only fires if a fresh engagement signal surfaced in the 60d window (post like, profile view, content engagement)
- Never resend the original M1 angle — the prospect saw it
- If still no reply at M7+5d, archive permanently per `acquisition.md` NoReply → [*]

## Per-message timing + spacing

| Message | Day | Spacing rationale |
|---|---|---|
| M1 | 0 | Send within 24h of connection accept while warmth is highest |
| M2 | +4 | Long enough to not stalk, short enough that M1 trigger reference is still fresh |
| M3 | +8 | Value-give breathes; doesn't feel like a sales push |
| M4 | +13 | Case study lands after value has been established |
| M5 | +18 | Call invite comes only after 4 prior messages of context — never before |
| M6 | +22 | Bow-out 4 days after invite gives the prospect a window to reply on their own |
| M7 | +60 | Reactivation only on fresh signal; otherwise archive |

**Cadence rules:**
- Never send two messages within 48 hours (reads as desperation)
- Never let gap exceed 7 days between messages M1-M5 (sequence loses thread)
- M6 → M7 is the only multi-week gap and requires a fresh engagement signal to fire

## Reply branching

**Universal rule per `write-dm-sequence/SKILL.md` Step 4:** Every reply pauses the sequence and re-routes through `/classify-dm-intent`. The pre-written sequence is for *no-reply* progression only.

| Reply pattern | Routes to | Rationale |
|---|---|---|
| Affirmative on M1 trigger ("yeah that's me") | Qualifying state — soft qualifier Q1 | Engaged signal — promote thread to qualification thread |
| Affirmative + pain confirmation on M2 | Qualifying state — Q2 + Q3 | High-intent — accelerate to budget/decision qualifier |
| Question reply on M3 (value-give engagement) | Qualifying state — context-tied Q1 | Curiosity signal — re-route through classifier; class often promotes C → W |
| Affirmative on M4 case study | ApplicationGate — skip remaining messages | Direct buying signal — gate behind application per N-12 |
| Affirmative on M5 invite | ApplicationGate — application form sent | Standard application route |
| Hard objection ("not interested" / "stop") | NoReply + flag in CRM | Respect the no; tag for 6-month re-engagement review |
| Hard-DQ trigger reply ("we don't have budget") | OperatorReview per A-13 | Founder-led DQ; never auto-reject |
| Soft objection ("maybe in Q2") | NoReply + 90-day reactivation flag | Defer per timeline; flag for the date |
| Wrong-person redirect ("ping my COO instead") | NewProspect for the named contact + bow-out current | New prospect entry, fresh classification |
| Profile-view ping (no reply text) | Continue sequence + flag W-promotion candidate | Soft engagement signal — does not pause sequence |

## Conversion benchmarks

| Metric | Healthy range | Below range diagnosis |
|---|---|---|
| M1 reply rate (7d) | 8-15% | M1 personalization templated OR ICP-list mismatch OR trigger reference generic |
| M1-M3 cumulative reply rate | 12-20% | M2 angle too similar to M1 OR M3 value-give too generic |
| M1-M5 cumulative reply rate | 15-25% | Sequence skipped a beat OR pitch arrived too early (M3-M4 instead of M5) |
| Qualified reply rate (post-classification) | 35-50% of replies | Qualification thread weak — re-run `/build-qualification-thread` |
| Qualified → ApplicationGate conversion | 40-60% | Application gate too friction-heavy OR too soft |
| Application → Booked conversion | 50-70% | Application triage misclassifying OR calendar friction |
| Booked → Held (show rate) | 75-90% | Qualification thread too leaky — calendar gate not validating intent |
| Held → Closed | 30-50% (depends on offer) | Call structure or offer-clarity issue, not sequence issue |

**Rule of thumb:** if M1 reply rate is below 8%, the issue is M1 personalization, ICP-list match, or trigger reference. Do not rewrite M2-M5 first — fix the funnel-entry message.

## Failure modes

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| Reply rate at 4% | Hits below 8% baseline | M1 personalization templated — variable substitution into a generic frame | Force trigger-event slot to be a specific timestamped event from prospect's last 30d |
| Reply rate at 6% with sharp M1 | M1 specific but still low | ICP-list cosine similarity below 0.75 — wrong list | Re-run `/source-lead-list` with sharper firmographic + behavioral filters |
| Replies arrive but no qualified replies | Replies are "thanks but no thanks" | M2 soft-DQ angle is too soft (not actually qualifying) | Sharpen M2 to surface the engaged subset, not just collect polite responses |
| M5 invite acceptance below 20% | Calls don't book despite engagement | Pitch arrives without value-stack context (M3-M4 underweight) | Re-write M3 with denser specific insight; re-write M4 with stronger case-study |
| Spam-flag warnings from LinkedIn | Account hits velocity threshold | Same M1 to too many recipients in same day | Cap M1 sends at 15-20/day per account; refresh M1 templates monthly |
| Calls book but no-show >40% | Calendar leaky | M5 routed direct to Calendly — N-12 violation | Re-route M5 through application form per N-12; gate calendar |
| Replies from non-ICP roles | Wrong-fit responses dilute pipeline | List filter is too broad on title or seniority | Tighten list filter; raise ICP cosine threshold to ≥0.80 |

## Cross-references

- `reference/frameworks/dm/intent-taxonomy.md` — parent framework defining all four classes
- `reference/frameworks/dm/warm-sequence-architecture.md` — the sister sequence for engaged prospects
- `reference/frameworks/dm/qualification-thread-design.md` — what runs when M1-M5 produces a qualified reply
- `reference/frameworks/dm/booking-gate-construction.md` — the application-gated calendar route per N-12
- `reference/swipe-file/dm-threads/cold/` — annotated example threads per M-position
- `skills/build-outbound-engine/SKILL.md` — the engine spec that schedules this sequence
- `skills/write-dm-sequence/SKILL.md` — the per-message draft writer
- `skills/source-lead-list/SKILL.md` — list-build protocol that feeds Class C
- `skills/classify-dm-intent/SKILL.md` — runtime classifier that routes replies
- `workflows/divisions/acquisition.md` — the FSM (ColdSequence → InThread → Replied / NoReply states)
- `INVARIANTS.md` N-3 (no invented results), N-4 (banned vocab), N-5 (intent classification first), N-12 (no calendar without application), A-11 (anonymized methodology descriptors), A-13 (operator-in-loop on hard DQs)

---

*Framework version 1.0.0 — 2026-05-03*
