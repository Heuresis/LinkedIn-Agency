# Warm Sequence Architecture (Class W)

> The structural rules for the engaged-prospect DM sequence — shorter, faster, with the relationship already named in M1. Highest leverage when the operator stops treating warm signals as cold.

## When to use (vs. other classes)

Class W is the sequence for prospects who have **interacted with subject's content in the last 30 days** but do not meet the higher-intent criteria of Class L (specific high-intent post like) or Class M (lead-magnet trigger comment).

**Use Warm sequence when:**
- Prospect liked, commented, profile-viewed, or otherwise engaged with the operator's (or client's) content in last 30 days
- Engagement is on general content, not on a post tagged `high-intent` (those route to Class L)
- Engagement was not the trigger word on a CTA/magnet post (that routes to Class M)
- Prior connection exists (mutual, alumni, prior reply thread within 30d, role match in known network)

**Do NOT use Warm sequence when:**
- Engagement is older than 30 days → class auto-decays to C per `intent-taxonomy.md` decay rules
- Engagement is on a tagged high-intent post → Class L (faster, more direct)
- Engagement is a magnet trigger comment → Class M (magnet delivery first)
- No engagement record exists → Class C, even if there's a "should have" relationship that was never formalized

The warm-class trap: operators often **misclassify a cold prospect as warm** based on vague feeling ("we met at a conference 6 months ago"). If the engagement signal is older than 30d and there's no record in the engagement log, treat as C. Below-baseline reply rates on Warm almost always trace back to misclassified cold prospects being addressed as if they remember the relationship.

## The class definition (entry criteria)

A prospect enters Class W when **all** of the following hold:

1. **Timestamped engagement in last 30 days** — a like, comment, profile view, prior reply, share, or mention recorded in `engagement_log[prospect_id]`
2. **Engagement is on subject's content (not third-party)** — operator's own posts or client's own posts
3. **The engaged post is NOT tagged `high-intent`** — those route to Class L for faster cadence
4. **The engagement is NOT a magnet trigger comment** — those route to Class M
5. **ICP cosine similarity ≥ 0.70** — slightly lower than Class C threshold because engagement signal is itself a fit-confirmation
6. **Has an identifiable warm-signal anchor** — a specific reference the operator can name in M1 ("saw your comment on the {topic} post")

If criterion 6 fails (engagement happened but operator can't name what it was), defer for 24h while engagement log is reconciled, then process — never write a Warm M1 with a vague "saw you engaging with my content" reference. That phrase pattern-matches a templated bot and depresses reply rate ~40%.

## Sequence anatomy (M1 → M5)

Total: 3-5 messages, 7-14 days span, 3-4 day cadence. Sequence is structurally shorter than Cold because the relationship is established and the decay window is 30 days — dragging it to day 21 misses the conversion window.

### M1 — Name-the-relationship opener (Day 0)

**Anatomy template:**
```
L1 — First-name + warm-signal acknowledgement: "Saw your {comment / like / share} on {specific post by topic}"
L2-3 — Specificity payload: "The thing you said about {specific element of their engagement OR specific takeaway from the post} — {one-line operator reaction}"
L4-5 — Light qualifier tied to topic: "The thing in there about {specific takeaway} — does that map to anything you're running into?"
L6 — Soft sign-off (no calendar link, no offer, no magnet)
```

**Anatomy rules:**
- Specificity is the trust currency — name the post by topic, name the comment by what it said, name the profile view by date if recent
- "Saw you engaging with my content" = banned (templated and depresses reply rate ~40%)
- The qualifier reuses the engagement context — it's not a separate question, it's a question about the thing they already engaged with
- NO pitch in M1 — the relationship is established, but the offer isn't earned yet

### M2 — Brief value-give tied to engagement topic (Day +3-4)

**Anatomy template:**
```
L1 — Bridge from M1: "On the {topic from M1} thing"
L2-3 — One specific insight extending the post they engaged with — fully self-contained, no follow-up required
L4 — Implicit close: "{the insight} — figured it might extend the original thread"
```

**Anatomy rules:**
- Value-give specifically extends the engagement topic — generic insight breaks the thread
- One specific insight only — never two
- No CTA, no question — value lands on its own
- When the prospect has replied to M1, M2 can compress to a one-line value extension + the soft call invite (effectively merging M2 + M3 in classes that have already shown buy-signal)

### M3 — Soft call invite (Day +7)

**Anatomy template:**
```
L1 — Frame: "Worth 20 min to dig into {specific topic from engagement}?"
L2-3 — Named outcome: "Where I'd start: {specific framework / specific lens / specific outcome from offer's transformation hypothesis}"
L4 — Application-gated invite per `INVARIANTS.md` N-12: "Here's how it usually starts: {application link, NOT calendar link}"
```

**Anatomy rules:**
- Named outcome ties back to the offer's transformation hypothesis (per `retainer-architecture.md` Rule 1) — generic "let's chat" is banned
- Time-boxed (20 min) — never open-ended
- Application-gated calendar per N-12 — calls without application are vibe calls; close rate collapses
- Soft frame ("worth 20 min?") gives the prospect the off-ramp without making them say no

### M4 — Door-open bow-out (Day +12, optional)

**Anatomy template:**
```
L1 — Permission release: "Won't keep nudging — appreciate the engagement on the {original post topic}"
L2-3 — Door-open: "If {specific trigger event} ever surfaces, here's the reply that gets you to the front: '{specific code phrase}'"
L4 — Sign-off in operator voice
```

**Anatomy rules:**
- Specific trigger and specific code phrase — generic "feel free to DM" is banned
- Closes the thread cleanly without burning the relationship — the prospect remains a Warm-eligible re-target for the next 30d
- Fires only if no reply at M3+5d; do not send if M3 produced a reply

### M5 — Topic-extension (Day +14, conditional)

**Anatomy template:**
```
L1 — New angle on the original topic: "On the {topic} thing — saw {fresh content / event / new insight}"
L2-3 — Fresh take that extends but doesn't repeat the M2 value-give
L4 — Soft re-open question
```

**Anatomy rules:**
- Only fires if a fresh content event surfaces (operator publishes new post on same topic, prospect engages with related content, industry event happens) — not on cadence alone
- Never repeat M1 or M2 framing — the prospect saw it
- If still no reply at M5+5d, archive per `acquisition.md` NoReply → [*]; class auto-decays to C for any future re-engagement

## Per-message timing + spacing

| Message | Day | Spacing rationale |
|---|---|---|
| M1 | 0 | Send within 48h of the engagement signal — warmth decays fast |
| M2 | +3-4 | Long enough to not crowd, short enough that the engagement context is still fresh |
| M3 | +7 | Call invite at the 1-week mark — Warm's compression target |
| M4 | +12 | Bow-out 5 days after invite gives reply window without dragging |
| M5 | +14 | Conditional re-engagement only on fresh content/event |

**Cadence rules:**
- Faster than Cold (7-14 day span vs. 14-21) — Warm decays in 30 days; sequence cannot consume the entire window
- Never compress to less than 3 days between messages (reads as desperation, even with engagement context)
- M3 is the conversion event — never push it past day 7 unless the prospect explicitly asks for time

## Reply branching

**Universal rule per `write-dm-sequence/SKILL.md` Step 4:** Every reply pauses the sequence and re-routes through `/classify-dm-intent`. Pre-written sequence is for *no-reply* progression only.

| Reply pattern | Routes to | Rationale |
|---|---|---|
| Affirmative on M1 ("yeah, I've been thinking about that") | Qualifying state — Q1 + Q2 (compressed thread per Class W's earned trust) | Compressed qualification; do not run full Cold-style 3-question thread |
| Question reply on M1 ("what do you mean by X?") | Qualifying state — answer + bridge to Q2 | High-curiosity signal — answer the question, then qualify on the back-half |
| Affirmative on M2 value-give ("this is helpful") | Qualifying state — propose call as Q-thread close | Conversion signal — go to call invite faster than the default M3 day |
| Affirmative on M3 invite | ApplicationGate — application form sent | Standard application route per N-12 |
| Soft objection on M3 ("not yet — Q3 maybe") | NoReply + 90-day timeline-flag | Defer per stated timeline; auto-route to reactivation queue |
| Hard objection ("not interested") | NoReply + 6-month CRM flag | Respect the no |
| Hard-DQ trigger reply ("we don't have budget right now") | OperatorReview per A-13 | Founder-led DQ; never auto-reject |
| Wrong-person redirect ("ping my CMO instead") | NewProspect for the named contact + bow-out current | New entry, fresh classification |
| Re-engagement on M5 (fresh signal) | Qualifying state — extension Q1 | Promotion: Warm has demonstrated pull-back signal |

## Conversion benchmarks

| Metric | Healthy range | Below range diagnosis |
|---|---|---|
| M1 reply rate (3d) | 25-45% | M1 not naming warm signal specifically enough OR engagement was misclassified (probably actually cold) |
| M1-M3 cumulative reply rate | 35-55% | M2 value-give too generic OR M3 invite lacks named outcome |
| Qualified reply rate (post-classification) | 50-65% of replies | Qualification thread too long (Warm earned compression — running Cold-style 3-Q thread depresses) |
| Qualified → ApplicationGate conversion | 55-75% | Application gate too friction-heavy OR Warm trust not respected (asking for too much info) |
| Application → Booked conversion | 60-80% | Application triage misclassifying OR calendar friction |
| Booked → Held (show rate) | 80-92% | Higher than Cold baseline because the relationship is established; below 80% = qualification thread leaky |
| Held → Closed | 35-55% (depends on offer) | Call structure or offer-clarity issue |

**Rule of thumb:** if M1 reply rate is below 25%, the warm signal is misclassified — the prospect is probably cold and was incorrectly classified as warm. Audit `engagement_log[prospect_id]` for the actual signal; verify timestamp is within 30 days; verify M1 names the specific signal. If all check out and reply rate is still below 25%, the M1 specificity payload is too generic.

## Failure modes

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| Reply rate at 12% | Hits below 25% Warm baseline | Misclassified — prospect is actually cold | Re-audit `engagement_log` filter; tighten 30d window; verify signal timestamp |
| Reply rate at 18% with verified warm signal | Engagement real but reply rate low | M1 reference is vague ("saw you engaging with my content") | Re-write M1 to name the specific post by topic + the specific comment/like context |
| High M1 reply rate but low qualified rate | Replies are conversational with no buying signal | M2-M3 not actually qualifying — running social pleasantries | Embed Q1 in M2's value-give close ("does X map to anything you're running on right now?") |
| M3 invite acceptance below 30% | Calls don't book despite engaged thread | M3 invite is generic ("worth a chat?") without named outcome | Re-write M3 to tie outcome to offer's transformation hypothesis (per Rule 1) |
| Calls book but no-show >25% | Calendar leaky — Warm should be tighter than Cold's 75-90% | M3 routed direct to Calendly — N-12 violation | Application-gate the calendar; respect N-12 even on Warm |
| Wrong-vertical replies | Replies arrive from non-ICP roles | ICP cosine threshold dropped too low (engagement assumed to be fit) | Raise threshold back to ≥0.70; engagement signal alone is not a fit signal |
| Sequence drags past 14 days | Operator extends sequence "to give them more time" | Warm decay window violated — class auto-decays at 30d | Hard-cap sequence at M5/day 14; reactivation only on fresh signal |
| Pitch arrives in M1 ("loved your post — want to chat?") | M1 invitation lands as templated | Earned trust burned by premature ask | Strip pitch from M1; M3 carries the call invite, never earlier |

## Cross-references

- `reference/frameworks/dm/intent-taxonomy.md` — parent framework defining all four classes
- `reference/frameworks/dm/cold-sequence-architecture.md` — the sister sequence for unsourced prospects (and the auto-decay destination if Warm window expires)
- `reference/frameworks/dm/liked-post-sequence-architecture.md` — the higher-intent class for tagged-post engagement
- `reference/frameworks/dm/qualification-thread-design.md` — Warm-specific compressed Q-thread (2 questions, not 3)
- `reference/frameworks/dm/booking-gate-construction.md` — the application-gated calendar route per N-12
- `reference/swipe-file/dm-threads/warm/` — annotated example threads per M-position
- `skills/build-outbound-engine/SKILL.md` — the engine spec that schedules this sequence
- `skills/write-dm-sequence/SKILL.md` — the per-message draft writer
- `skills/classify-dm-intent/SKILL.md` — runtime classifier that routes replies and detects class promotion/decay
- `workflows/divisions/acquisition.md` — the FSM (WarmSequence → InThread → Replied / NoReply states)
- `INVARIANTS.md` N-4 (banned vocab — "saw you engaging" is on the list), N-5 (intent classification first), N-12 (no calendar without application), A-13 (operator-in-loop on hard DQs)

---

*Framework version 1.0.0 — 2026-05-03*
