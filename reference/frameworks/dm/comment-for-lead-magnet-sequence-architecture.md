# Comment-for-Lead-Magnet Sequence Architecture (Class M)

> The structural rules for the self-selected, contract-driven, magnet-anchored DM sequence — magnet first in M1, qualify second, never invert. The class with the highest reply rate baseline and the simplest failure mode (delivery latency).

## When to use (vs. other classes)

Class M is the sequence for prospects who **commented a specific trigger keyword** on a content CTA post engineered for magnet delivery (e.g., "comment GUIDE for the framework," "drop INFO for the playbook," "type SYSTEM for the breakdown"). The comment IS the contract: prospect performs the trigger action, operator delivers the magnet.

**Use Comment-magnet sequence when:**
- Prospect commented a defined trigger keyword on an operator's (or client's) magnet-engineered CTA post within last 24h
- The trigger keyword matches the post's published trigger spec (case-insensitive match acceptable; substring/typo tolerance defined per skill)
- Magnet asset is ready to deliver (link is live, file is accessible, gate is configured)

**Do NOT use Comment-magnet sequence when:**
- Comment is generic (no trigger keyword) on a magnet-engineered post → Class W (engaged but not magnet-claiming)
- Comment is on a non-magnet post → Class W
- Trigger keyword arrives >24h after post publication → still Class M but flag for delivery-system audit (latency surfaced)
- Prospect liked AND commented trigger → Class M takes priority (magnet honor first); track L flag for cross-references

The Class M trap: the sequence is structurally the simplest, but the failure mode (delivery latency) is the most catastrophic. A magnet that arrives 6+ hours after the comment breaks trust. The contract was "comment X to get Y" — delay is contract violation. Reply rate compresses from 80-95% to 30-50% when M1 latency exceeds 1 hour.

## The class definition (entry criteria)

A prospect enters Class M when **all** of the following hold:

1. **Trigger comment in last 24h** — timestamped comment in `engagement_log[prospect_id]` matching the post's `magnet_trigger_keyword`
2. **The post is tagged `magnet-cta` in the content engine** — `content_post[id].cta_type = magnet-cta` with `trigger_keyword` and `magnet_asset_path` fields populated
3. **Magnet asset is ready to deliver** — `magnet_asset_path` resolves, link is live, gating mechanism (ManyChat, automated DM, manual VA queue) is operational
4. **DM surface exists** — connection accepted OR open-DM-eligible (premium / open-profile)
5. **No prior Class M trigger from this prospect on the same magnet in last 30 days** — duplicates re-route to manual review (likely automation issue)

ICP cosine similarity is **not** a gating criterion for Class M — the prospect self-selected by performing the contract action. Operator may still tag the post-magnet conversion path differently for non-ICP prospects (magnet still delivers, but qualification-to-call thread skips). Per `intent-taxonomy.md`, M1 is delivery-only and runs regardless of fit.

## Sequence anatomy (M1 → M5)

Total: 3-5 messages, 5-10 days span. Sequence is structurally inverted from the others — value (magnet) comes first, qualification comes after consumption is verified.

### M1 — Magnet delivery (Day 0, target <1 hour post-comment, hard cap <4 hours)

**Anatomy template:**
```
L1 — Acknowledge comment + name magnet: "Thanks for commenting {trigger keyword} — here's the {magnet name}"
L2 — The magnet itself: link / file / direct asset access
L3 — One-sentence frame line on how to use it: "The part on {specific page / specific section} is where most {role}s find the wedge"
```

**Anatomy rules:**
- That is the **entire** M1. No qualifier. No pitch. No "before I send this, can I ask…" — that breaks the contract and trust dies
- Magnet delivery in <1 hour is the conversion bullseye; <4 hours is healthy; >4 hours is degraded; >24 hours is contract violation
- Manual delivery breaks the chain at scale — ManyChat or equivalent automation handles M1 send for any volume above 5/week
- Frame line is critical — without it, magnet consumption rate drops ~25%; the frame primes the prospect to find the value

### M2 — Consumption check (Day +1-2)

**Anatomy template:**
```
L1 — Light follow-up reference: "Quick check on the {magnet name}"
L2-3 — Consumption-check question: "Did the {magnet} land okay? Anything stick out?"
L4 — Soft invitation to reply (no calendar, no offer)
```

**Anatomy rules:**
- Consumption check timing: 24-48h post-delivery — long enough that the prospect has had time to engage, short enough that the magnet is still top-of-mind
- "Anything stick out?" is an open prompt — surfaces the engaged subset (responders convert at 60%+) AND surfaces friction for non-responders (link broken, magnet didn't deliver, etc.)
- NO pitch in M2 — the qualification thread does not start until consumption is confirmed
- If M2 produces no reply, M3 splits to a value-extension branch instead of a qualification-bridge

### M3 — Qualification bridge (Day +4)

**Reply branch — consumption confirmed:**
```
L1 — Reference back to their consumption signal: "Glad {specific aspect they mentioned} landed"
L2-3 — Qualifying question Q1: "What part of {magnet topic} are you working on right now?" — surfaces buying-stage and trigger
L4 — No calendar link yet; let the qualifier answer first
```

**No-reply branch — value extension:**
```
L1 — Brief context anchor
L2-3 — One additional insight that extends the magnet — fully self-contained, no follow-up required
L4 — Soft re-open: "On the {magnet's named mechanism} thing — figured this might extend it"
```

**Anatomy rules:**
- Qualification only after consumption confirmation — inverting (qualify first, deliver second) destroys trust per `intent-taxonomy.md`
- Q1 is open-ended — it surfaces the prospect's current situation in their own words, which feeds Q2 in the next message or the call-prep doc
- Value extension on no-reply gives the prospect a second opening; never re-pitch in the no-reply branch

### M4 — Call invite tied to magnet topic (Day +7)

**Anatomy template:**
```
L1 — Bridge from qualification or magnet: "On the {Q1 reply OR magnet topic} thing"
L2-3 — Magnet-to-call bridge: "The {magnet name} shows you the framework. The 20-min call shows which parts apply to your specific {situation from Q1}"
L4 — Application-gated invite per N-12: "Want one? Here's how it usually starts: {application link, NOT calendar link}"
```

**Anatomy rules:**
- Magnet-to-call bridge is the load-bearing message — the magnet's promised outcome is partially delivered; the call completes the picture
- Named outcome ties to the offer's transformation hypothesis (per `retainer-architecture.md` Rule 1) — generic "let's chat" is banned
- Application-gated calendar per N-12 — calls without application gate burn the qualified prospect
- Time-boxed (20 min) — open-ended invites depress acceptance

### M5 — Door-open bow-out (Day +10)

**Anatomy template:**
```
L1 — Permission release: "Won't keep nudging — appreciate you grabbing the {magnet name}"
L2-3 — Specific door-open: "DM '{specific code phrase}' if you want me to circle back when {specific trigger event}"
L4 — Sign-off in operator voice
```

**Anatomy rules:**
- Specific code phrase + specific trigger — generic "feel free to DM" is banned
- Closes the thread cleanly without burning the magnet relationship — the magnet remains a reference asset for any future re-targeting
- Fires only if no reply at M4+3d; do not send if M4 produced a reply

## Per-message timing + spacing

| Message | Day | Spacing rationale |
|---|---|---|
| M1 | 0 (target <1h post-comment, hard cap <4h) | Contract honor — every hour of latency erodes trust geometrically |
| M2 | +1-2 | Consumption-check window — long enough to consume, short enough to remember |
| M3 | +4 | Qualification bridge after consumption is verified or value extension if no signal |
| M4 | +7 | Call invite anchored to magnet's promised outcome |
| M5 | +10 | Door-open bow-out; class has no scheduled reactivation default |

**Cadence rules:**
- M1 latency is THE conversion lever — operators who hand-deliver M1 for "personal touch" lose 40%+ of conversion at scale
- Never compress M2 to less than 24h post-delivery (prospect needs time to consume)
- Hard cap sequence at day 10 — class M decays once the magnet is no longer top-of-mind
- No reactivation default — the magnet was the conversion event; reactivation only triggers on fresh magnet trigger or fresh high-intent engagement

## Reply branching

**Universal rule per `write-dm-sequence/SKILL.md` Step 4:** Every reply pauses the sequence and re-routes through `/classify-dm-intent`. Pre-written sequence is for *no-reply* progression only.

| Reply pattern | Routes to | Rationale |
|---|---|---|
| Reply on M1 ("got it, thanks!") | Continue sequence — no re-route needed | Acknowledgement; sequence proceeds to M2 on cadence |
| Detailed engagement reply on M1 ("this is great, the part about X is exactly what I needed") | Promote to Qualifying state — accelerate to Q1 in next send | High-intent signal in delivery message; compress sequence |
| Affirmative on M2 consumption check ("yeah, the {section} landed") | Qualifying state — Q1 + bridge to call invite | Confirmed consumption + engagement signal — proceed to qualification |
| Friction on M2 ("link didn't work" / "didn't get it") | Operator-flag — re-deliver M1 + audit delivery automation | Critical signal: delivery system failed; never proceed to qualification |
| "Not what I expected" on M2 | NoReply + flag for magnet-quality audit | Magnet-fit issue; do not re-pitch; surface for content engine audit |
| Affirmative on M3 Q1 ("I'm working on {specific situation}") | Qualifying state — Q2 (budget/decision) | Standard qualification thread |
| Affirmative on M4 invite | ApplicationGate — application form sent | Standard application route per N-12 |
| Hard objection ("not interested" / "stop") | NoReply + 6-month CRM flag | Respect the no |
| Hard-DQ trigger reply ("we don't have budget") | OperatorReview per A-13 | Founder-led DQ |
| Comment trigger word arrives during sequence (different magnet) | Pause Class M, route to new Class M for the new magnet | Each magnet contract is honored independently |
| Wrong-person redirect ("ping my COO instead") | NewProspect for the named contact + bow-out current; magnet already delivered | New entry, fresh classification |

## Conversion benchmarks

| Metric | Healthy range | Below range diagnosis |
|---|---|---|
| M1 delivery time | <1 hour (target), <4 hours (acceptable) | Manual delivery at volume — automate via ManyChat or equivalent |
| M1 reply rate (acknowledgement of receipt) | 60-80% | M1 missing frame line OR delivery latency >4h breaking contract |
| M2 consumption-check reply rate | 30-50% | Magnet weak (no consumption signal) OR audience not magnet-fit OR M1 frame line absent |
| M2 detailed-engagement reply rate (responders) | 15-25% of M1 recipients | Magnet quality high → these convert at 60%+ to call |
| M3 Q1 reply rate (post-consumption) | 55-75% | Q1 too generic — should reference the magnet's specific topic |
| M4 invite acceptance | 25-40% of qualified replies | Magnet-to-call bridge weak — call value not differentiated from magnet value |
| Application → Booked conversion | 65-85% | Application triage misclassifying — Class M apps should fast-track |
| Booked → Held (show rate) | 80-92% | Calendar leak; verify application gate is enforced per N-12 |
| Held → Closed | 35-55% (depends on offer) | Call structure or offer-clarity issue |
| Overall magnet-to-close conversion | 3-8% of M1 recipients | Below 3% = magnet is not converting; audit magnet quality, audience match, or call structure |

**Rule of thumb:** if M1 delivery time >1 hour, fix delivery automation before any other diagnosis. If delivery is fast and M2 consumption-check reply rate is below 30%, the magnet is weak (or the audience is not magnet-fit). Audit the magnet asset and the audience-magnet match before re-writing M2-M5.

## Failure modes

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| M1 delivery 6+ hours late | Magnet arrives after the contract trust window | Manual delivery at volume | Implement ManyChat or equivalent automation; M1 is a no-touch delivery |
| M2 consumption-check reply rate at 15% | Below 30% baseline | Magnet weak — no consumption signal | Audit magnet asset (length, density, immediate value); test with operator's network for consumption rate |
| M1 reply rate at 45% | Below 60% acknowledgement baseline | M1 missing frame line OR magnet link broken | Add frame line ("the part on page X is where most operators find the wedge"); audit link health |
| Consumption-check rate high but call-invite acceptance low | M2 strong, M4 weak | Magnet-to-call bridge generic ("worth a chat?") | Re-write M4 to differentiate magnet value (framework) from call value (situation-specific application) |
| Qualification-before-delivery in M1 | "Before I send this, can I ask quick context?" pattern | Operator over-eager; thinks pre-qualification will improve fit | Strip M1 to delivery-only; qualification starts in M3 only after consumption is verified |
| Call no-show >25% | Show rate below 80% baseline | Application gate not enforced — N-12 violation | Application-gate the calendar; even Class M (highest-intent) respects N-12 |
| Magnet trigger comments arrive but no Class M sequence fires | Classifier doesn't detect magnet trigger | Post not tagged `magnet-cta` in content engine OR keyword spec missing | Audit content engine post tags; verify `trigger_keyword` field populated for every magnet-CTA post |
| Same prospect comments trigger word twice | Duplicate Class M sequence triggered | Auto-deduplication missing in classifier | Add 30-day duplicate guard per prospect-magnet pair; surface dupes for manual review |
| Magnet delivered but the link is broken | Prospects flag broken link in M2 | Magnet asset moved or expired | Audit `magnet_asset_path` resolution monthly; alert on broken links |

## Cross-references

- `reference/frameworks/dm/intent-taxonomy.md` — parent framework defining all four classes; defines Class M magnet-first contract
- `reference/frameworks/dm/cold-sequence-architecture.md` — the structural opposite (no contract, no magnet, full-cold cadence)
- `reference/frameworks/dm/warm-sequence-architecture.md` — where non-trigger comments on magnet-engineered posts route
- `reference/frameworks/dm/liked-post-sequence-architecture.md` — Class M takes priority over L if both signals fire
- `reference/frameworks/dm/qualification-thread-design.md` — Class M's post-consumption qualification thread
- `reference/frameworks/dm/booking-gate-construction.md` — application-gated calendar route per N-12
- `reference/frameworks/content/hook-anatomy.md` — magnet-CTA post engineering feeds Class M volume
- `reference/swipe-file/dm-threads/comment-magnet/` — annotated example threads per M-position
- `skills/build-outbound-engine/SKILL.md` — engine spec that schedules this sequence
- `skills/build-content-engine/SKILL.md` — content engine that tags `magnet-cta` posts and defines `trigger_keyword`
- `skills/write-dm-sequence/SKILL.md` — per-message draft writer
- `skills/classify-dm-intent/SKILL.md` — runtime classifier; must detect trigger comment in <1h
- `workflows/divisions/acquisition.md` — the FSM (CommentMagnetSequence → MagnetDelivered → InThread → Replied / NoReply states)
- `INVARIANTS.md` N-3 (no invented results in magnet content), N-4 (banned vocab), N-5 (intent classification first), N-12 (no calendar without application), A-13 (operator-in-loop on hard DQs)

---

*Framework version 1.0.0 — 2026-05-03*
