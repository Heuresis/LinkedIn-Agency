# Liked-Post Sequence Architecture (Class L)

> The structural rules for the highest-intent micro-signal sequence — fast, direct, post-specific, with M1 inside the 24-hour decay window. The class with the lowest volume, the highest conversion rate, and the most commonly skipped sequence in the workspace.

## When to use (vs. other classes)

Class L is the sequence for prospects who **liked a specific post that the operator has tagged `high-intent`** within the last 7 days. High-intent posts are typically: case study posts, offer-bridge posts, named-mechanism posts, contrarian-take posts that filter for buying intent, or posts explicitly engineered to surface buyer-stage prospects.

**Use Liked-post sequence when:**
- Prospect liked a post in the operator's (or client's) `high-intent` content set in last 7 days
- The like is the only engagement signal (a like + comment routes to the L-comment compound — the highest-conversion micro-segment in the entire taxonomy)
- The like is on subject's own content, not a third-party share

**Do NOT use Liked-post sequence when:**
- Like is older than 7 days → engagement signal decayed; auto-decays to W if within 30d, or C if older
- Like is on a non-tagged post (general content) → Class W
- Prospect liked AND commented the trigger word → Class M (magnet delivery first, L flag tracked for reference)
- Prospect liked AND commented (non-trigger) → L-comment compound flag (still L sequence, but tag for the higher-conversion micro-segment)

The Class L trap: operators often **don't tag posts as `high-intent`** in `/build-content-engine`, which means the classifier doesn't surface likes on those posts as Class L. Result: the highest-conversion class quietly routes to W, compressing reply rate from 50-70% to 25-45%. Audit the high-intent post tagging quarterly.

## The class definition (entry criteria)

A prospect enters Class L when **all** of the following hold:

1. **Liked a post in last 7 days** — timestamped like in `engagement_log[prospect_id]` within the 7-day decay window
2. **The post is tagged `high-intent`** in the content engine's metadata (`content_post[id].intent_tag = high-intent`)
3. **No commented-trigger-word signal** on the same or any post in last 24h — that routes to Class M instead
4. **ICP cosine similarity ≥ 0.65** — lowest threshold across classes because the like itself is a partial fit-confirmation
5. **Operator (or client) is the post author** — likes on shared/repost content do not qualify; likes on third-party content do not qualify
6. **Connection-eligible** — DM surface exists (connection accepted OR open-DM)

If criterion 1 timestamp is older than 24h, the sequence is still valid but conversion compresses materially. M1 inside <6h is the bullseye; <24h is healthy; >24h is degraded; >72h is functionally cold.

## Sequence anatomy (M1 → M4)

Total: 2-4 messages, 3-7 days span. Sequence is the shortest in the taxonomy because the engagement signal decays fast and the prospect has earned directness — pleasantries waste the warmth.

### M1 — Compliment-with-specific (Day 0, ideally <6h post-like, hard cap <24h)

**Anatomy template:**
```
L1 — First-name + post-specific reference: "Saw you liked the post on {specific takeaway, not post title}"
L2-3 — One-sentence bridge from post takeaway to prospect's likely situation: "The thing in there about {specific mechanism / specific framework / specific insight} — {one-line operator observation}"
L4 — Direct 1-question qualifier: "Does {takeaway} map to anything you're working on right now?"
```

**Anatomy rules:**
- Reference the post by **takeaway**, not post title — "saw you liked the post on the Intent Taxonomy" reads as templated; "saw you liked the post on the cold-DM compression problem — that thing about classifying before sequencing" reads as specific
- "Thanks for the like" is banned — generic, depresses reply rate ~30%
- One question only — the prospect has earned directness, multi-question M1 reads as interrogation
- NO calendar link, NO offer, NO magnet in M1 — the post itself is the offer-bridge; the M1 is the conversation-opener

### M2 — Confirm fit + propose call (Day +2-3 if reply, Day +3 if no reply)

**Reply branch — "Yes, that maps":**
```
L1 — Confirmation acknowledgement: "Yeah, that's the pattern I see most often with {role}s in {situation}"
L2-3 — Named outcome bridge: "Worth 20 min to dig into {specific intervention from offer's transformation hypothesis}?"
L4 — Application-gated invite per N-12: "Here's how it usually starts: {application link}"
```

**No-reply branch — short value drop:**
```
L1 — Reference back: "On the {original post topic} thing"
L2-3 — One additional insight that extends the post — fully self-contained, no follow-up required
L4 — Soft re-open question: "Curious if {specific symptom from VoC patterns} is showing up for you"
```

**Anatomy rules:**
- Reply branch is the conversion path — Class L's 50-70% reply rate means M2-conversion-on-reply is where most calls book
- No-reply branch should not be a re-pitch — it's a value extension that gives the prospect a second opening
- Application-gated calendar per N-12 — calls without application gate burn the qualified prospect

### M3 — Bow-out + door-open (Day +5-7)

**Anatomy template:**
```
L1 — Permission release: "Won't keep nudging — appreciate you engaging with the {original post topic}"
L2-3 — Specific door-open: "If {specific trigger event likely for the prospect's role/stage} ever surfaces, here's the reply that gets you to the front: '{specific code phrase}'"
L4 — Sign-off in operator voice
```

**Anatomy rules:**
- Bow-out closes the thread cleanly without burning the engagement signal — the like remains in the engagement log for any future re-targeting
- Specific trigger and specific code phrase — generic "feel free to DM" is banned
- Fires only if no reply at M2+3-4d; do not send if M2 produced a reply

### M4 — Conditional reactivation (Day +7+, fires only on fresh engagement signal)

**Anatomy template:**
```
L1 — New angle: "Saw you liked the {new high-intent post topic} — different angle than last week"
L2-3 — Fresh take tied to the new post + the prospect's situation
L4 — Soft re-open question
```

**Anatomy rules:**
- Only fires if prospect engages with another `high-intent` post within 14 days of original sequence end
- Never repeat M1 framing — the prospect saw it
- Class L has no scheduled reactivation default (per `intent-taxonomy.md` decay table) — M4 is event-triggered, not cadence-triggered
- If still no reply at M4+5d, archive permanently per `acquisition.md` NoReply → [*]

## Per-message timing + spacing

| Message | Day | Spacing rationale |
|---|---|---|
| M1 | 0 (target <6h post-like, hard cap <24h) | Engagement signal decays in 5-7 days; M1 in <24h is the conversion bullseye |
| M2 (reply branch) | +2-3 | Tight cadence preserves the heat of the original signal |
| M2 (no-reply branch) | +3 | Same cadence — value drop must arrive before the engagement memory fades |
| M3 | +5-7 | Bow-out fast; Class L is a sprint, not a marathon |
| M4 | event-triggered (+7-14 if fresh signal) | Conditional only; never cadence-driven |

**Cadence rules:**
- M1 timing is THE conversion lever — operators who delay M1 to "next business day" lose 40%+ of the conversion potential
- Never compress to less than 24h between messages (even on a hot signal)
- Hard cap sequence at day 7 (or day 14 if M4 fires on fresh signal) — Class L decays fast and dragging it past 7 days reads as desperation
- Workspace must surface the like signal in real-time (or near-real-time) — manual review delays kill the class

## Reply branching

**Universal rule per `write-dm-sequence/SKILL.md` Step 4:** Every reply pauses the sequence and re-routes through `/classify-dm-intent`. Pre-written sequence is for *no-reply* progression only.

| Reply pattern | Routes to | Rationale |
|---|---|---|
| Affirmative on M1 ("yeah that's exactly where I am") | Qualifying state — single-Q thread (Class L's earned trust = compress to 1 qualifier max) | Highest-intent signal in the taxonomy; do not run multi-question Q-thread |
| Question reply on M1 ("can you say more about X?") | Qualifying state — answer + propose call | High-curiosity signal; answer briefly, then propose 20-min call |
| Affirmative + buying signal on M1 ("how do you usually engage on this?") | ApplicationGate — skip Q-thread entirely | Direct buying-intent signal; gate behind application per N-12 |
| Affirmative on M2 invite | ApplicationGate — application form sent | Standard application route |
| Soft objection on M2 ("not yet") | NoReply + 60-day reactivation flag | Defer; reactivate on fresh high-intent post engagement |
| Hard objection ("not interested") | NoReply + 6-month CRM flag | Respect the no |
| Hard-DQ trigger reply ("we don't have budget") | OperatorReview per A-13 | Founder-led DQ |
| L+comment compound (the like came with a comment, even non-trigger) | Tag as L+comment compound; run L sequence with M1 referencing both | Highest-conversion micro-segment in the taxonomy (often 80%+ reply rate) |
| Comment trigger word arrives mid-sequence | Pause Class L, route to Class M magnet delivery | M takes priority — honor the magnet contract |

## Conversion benchmarks

| Metric | Healthy range | Below range diagnosis |
|---|---|---|
| M1 reply rate (3d) | 50-70% | M1 generic ("thanks for the like") OR M1 timing slow (>24h post-engagement loses momentum) |
| L+comment compound reply rate | 75-90% | M1 not naming both the like and the comment specifically |
| M1-M2 cumulative reply rate | 60-80% | M2 no-reply value drop too generic OR M2 reply branch lacks named outcome |
| Qualified reply rate (post-classification) | 70-85% of replies | Q-thread too long (Class L earned compression — single-question max) |
| Qualified → ApplicationGate conversion | 65-80% | Application gate friction-heavy OR Class L trust not respected (asking for too much info) |
| Application → Booked conversion | 70-85% | Application triage misclassifying — Class L apps should fast-track |
| Booked → Held (show rate) | 85-95% | Highest in the taxonomy because intent is highest; below 85% = qualification was leaky |
| Held → Closed | 40-60% (depends on offer) | Call structure or offer-clarity issue, not sequence issue |

**Rule of thumb:** if M1 reply rate is below 50%, the issue is one of three things — (1) M1 is generic ("thanks for the like" or post title without takeaway), (2) M1 timing is slow (M1 sent 36h+ post-like), or (3) the post wasn't actually high-intent (the tagging is wrong, the prospects who like it are not buying-stage). Check in that order.

## Failure modes

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| Reply rate at 25% | Below 50% L baseline | M1 generic ("thanks for the like") | Re-write M1 to reference post by takeaway + bridge to prospect's situation |
| Reply rate at 35% with sharp M1 | M1 specific but still below baseline | M1 timing slow — sent 24h+ post-like | Move classifier and send pipeline to <6h target; cap delays at 24h |
| Sequence never fires for liked posts | Likes on operator's content don't surface as Class L | Posts not tagged `high-intent` in content engine | Audit content engine tagging; mark case-study posts, offer-bridge posts, named-mechanism posts |
| L+comment compound below 75% | The highest-leverage micro-segment underperforms | M1 not referencing both the like AND the comment | Write M1 to acknowledge both signals: "saw the like AND the comment on X" |
| Qualified-but-no-call-book | Replies are positive but calls don't close | Q-thread too long — Class L earned single-Q compression | Compress Q-thread to one question; route to ApplicationGate immediately on positive |
| Calls book but no-show >15% | Show rate below 85% L baseline | Calendar not application-gated — N-12 violation | Application-gate the calendar; even Class L respects N-12 |
| Pitch arrives in M1 | M1 includes calendar link or offer mention | Operator over-eager with high-intent prospect | Strip M1 to compliment-with-specific + 1 question; M2 carries the invite |
| Class L sequence missed entirely | High-engagement posts produce no Class L volume | Workspace not tagging or not surfacing likes in real-time | Re-run `/build-content-engine` with `intent_tag` field; audit classifier latency |

## Cross-references

- `reference/frameworks/dm/intent-taxonomy.md` — parent framework defining all four classes; defines L+comment compound as highest-conversion micro-segment
- `reference/frameworks/dm/warm-sequence-architecture.md` — the auto-decay destination if L window expires (within 30d)
- `reference/frameworks/dm/cold-sequence-architecture.md` — the auto-decay destination if W window also expires
- `reference/frameworks/dm/comment-for-lead-magnet-sequence-architecture.md` — Class M takes priority if a magnet trigger arrives mid-L-sequence
- `reference/frameworks/dm/qualification-thread-design.md` — Class L's compressed single-Q thread
- `reference/frameworks/dm/booking-gate-construction.md` — application-gated calendar route per N-12
- `reference/frameworks/content/hook-anatomy.md` — `high-intent` post tagging rules feed Class L volume
- `reference/swipe-file/dm-threads/liked-post/` — annotated example threads per M-position
- `skills/build-outbound-engine/SKILL.md` — engine spec that schedules this sequence
- `skills/build-content-engine/SKILL.md` — content engine that tags `high-intent` posts feeding Class L
- `skills/write-dm-sequence/SKILL.md` — per-message draft writer
- `skills/classify-dm-intent/SKILL.md` — runtime classifier; must run in <6h target
- `workflows/divisions/acquisition.md` — the FSM (LikedPostSequence → InThread → Replied / NoReply states)
- `INVARIANTS.md` N-4 (banned vocab — "thanks for the like" is on the list), N-5 (intent classification first), N-12 (no calendar without application), A-13 (operator-in-loop on hard DQs)

---

*Framework version 1.0.0 — 2026-05-03*
