# DM Intent Taxonomy

> **The 1-of-1 primitive of this workspace.** Every other LinkedIn agency runs one cold sequence at one list. This workspace runs four sequences keyed to four intent classes. The classification is the leverage.

## Why this exists

The single largest reply-rate compressor in LinkedIn DM ops is **sending the wrong sequence to the wrong intent class.** A class-C (cold) opener sent to a class-L (liked-your-post) prospect is a 70% reply-rate squandered to a 9% reply-rate. A class-L sequence sent to a class-C prospect is a 35% reply-rate-from-treating-them-like-they-know-you that backfires into spam-flagging.

The intent class IS the conversion lever. Sequence design follows.

## The four classes

| Class | Code | Definition | Volume share | Reply rate baseline | Sequence length | Decay window |
|---|---|---|---|---|---|---|
| **Cold** | C | Never interacted, sourced from list | 60-75% | 5-12% | 5-7 msgs / 14-21d | None |
| **Warm** | W | Interacted with content in last 30d | 15-25% | 25-45% | 3-5 msgs / 7-14d | 30d |
| **Liked-your-post** | L | Liked a specific high-intent post | 5-15% | 50-70% | 2-4 msgs / 3-7d | 5-7d |
| **Comment-for-lead-magnet** | M | Commented trigger on a CTA post | Variable, typically 10-30/wk | 80-95% | 3-5 msgs / 5-10d | 24h M1 deadline |

**Volume share** assumes a healthy hybrid agency with active content engine. Pure outbound agencies will skew higher on C; pure inbound agencies may have <5% C.

**Reply rate baseline** — anything below baseline by ≥30% indicates a problem in the sequence, the personalization, or the list (for C). Anything above baseline by ≥50% indicates a high-leverage pattern worth catalysing.

## Classifier logic

The `/classify-dm-intent` skill runs on every inbound DM AND on every prospect added to outreach queue. Decision tree:

```
Is the prospect a comment on a CTA post in last 24h?
├── YES → Class M
└── NO ↓
    Has the prospect liked a post tagged "high-intent" in last 7d?
    ├── YES → Class L
    └── NO ↓
        Has the prospect engaged with subject's content in last 30d (any interaction)?
        ├── YES → Class W
        └── NO → Class C
```

Edge cases:
- **Multi-class.** If a prospect liked-your-post AND commented-for-lead-magnet, route to M (highest-conversion class). Track L flag for sequence cross-references.
- **Class decay.** A class-W prospect with no further engagement for 30d auto-decays to C on next outreach attempt.
- **Class promotion.** A class-C prospect who replies and engages is promoted to W for any subsequent re-engagement.

## Per-class sequence design principles

### Class C (Cold) sequence design

**Voice.** Casual-direct. Read as written by a human, not a template. Every message must survive a "would I open this if I got it?" test from the operator.

**Personalization.** M1 personalization line is mandatory and must be specific. "Saw your post on X" with X being the actual post title. Generic "I see you're in [industry]" reads as templated and depresses reply rate by ~50%.

**Disqualification baked in.** The sequence assumes most recipients are not a fit. Soft-DQ language ("we might not be a fit, but if {specific criterion}, this matters") raises reply rate paradoxically — sounds like the operator isn't desperate.

**The "wrong?" close on M1.** Ending M1 with "wrong?" or "no need to reply if it's a miss" lifts reply rate ~2x by removing the implicit ask burden.

**Voice-note in M3.** When a class-C prospect replies to M2, M3 (transition to offer) lands with materially higher engagement when delivered as a 30-45 second voice note vs. text. Voice still has scarcity on LinkedIn DMs in 2026 (~3% of operators use it).

### Class W (Warm) sequence design

**Specific reference.** M1 must reference the specific post/comment/profile-view, not "saw you engaging with my content." Specificity is the trust currency.

**Light qualification.** Class W has earned the right to be qualified differently — they've shown signal. Don't waste it with a hard-DQ Q1. Use the engagement reference as the qualifier ("you engaged with the post about X — does X map to anything you're working on?").

**Compressed timeline.** Class W decays in 30 days. Don't drag the sequence to day 21. 7-14 days max.

### Class L (Liked-your-post) sequence design

**Move fast.** Engagement signal decays in 5-7 days. M1 in <24 hours of the like, ideally <6.

**Direct reference to the post + the takeaway.** Not "saw you liked my post" — "saw you liked the post about {specific takeaway}. The thing in there about {specific mechanism} — does that map to anything you're working on?"

**Skip pleasantries.** This class has earned directness. 1-question qualifier in M1, call invite in M2 if reply.

**The like-comment compound.** If the prospect liked AND commented, the L-comment compound is the highest-conversion micro-segment in the entire taxonomy (often 80%+ reply rate). Tag separately.

### Class M (Comment-for-lead-magnet) sequence design

**Deliver the magnet first.** M1 = the magnet, nothing else. No "before I send this, can I ask…" — that's where trust dies. Deliver in <1 hour of comment via ManyChat or equivalent. Manual delivery breaks the chain (delays kill conversion).

**Consumption check in M2.** "Did the {magnet} land okay?" — surfaces the engaged subset (responders convert at 60%+) and surfaces friction for the non-responders (link broken, magnet didn't deliver, etc.).

**Qualification AFTER consumption.** Inverting (qualify first, deliver second) destroys trust. The contract was "comment X to get Y" — honor it before asking for anything.

**Magnet-to-call bridge in M4.** The bridge lands when the magnet's promised outcome is partially delivered and the call completes the picture. "The {magnet} shows you the framework. The 20-min call shows you which parts apply to your specific {situation} — want one?"

## Cross-class principles

**Voice consistency.** All four sequences carry the SAME operator (or client) voice. The intent class shifts cadence and depth, not voice character. A class-C M1 in agency-default voice + a class-L M1 in casual-warm voice = brand confusion.

**Tracking.** Every sequence message is tagged with class + sequence position + outcome. Workspace logs to `outputs/{date}/dm/` for analysis.

**Refresh cadence.** LinkedIn detects template repetition across accounts. Refresh M1-M3 templates quarterly. M4-M7 can hold longer (less LinkedIn-visible).

**No automation tools that auto-send.** Manual or assisted sends only. LinkedIn shadow-bans accounts with high-volume automated sends. The workspace produces the sequences; humans (or VAs) send them.

## Failure modes (catalogued)

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| Reply rate at 4% on cold | Hits below baseline | M1 personalization templated | Force human-written or workspace-generated personalization with verified profile-data input |
| Class M not delivering at 1h | Magnet arrives 6+ hrs late | Manual delivery | ManyChat or equivalent automation for M1 only |
| Class L sequence missed | High-engagement posts produce no DM volume | Workspace not tagging high-intent posts | Mark posts as "high-intent" in `/build-content-engine` so classifier picks up likes |
| Calls book but no-show >40% | Booking gate leaky | No qualification thread before calendar | Re-run `/build-qualification-thread` and gate calendar behind it |
| Cold outreach causes spam flags | LinkedIn warns the account | Same M1 to too many recipients | Refresh M1 templates monthly, vary personalization variables |

## Cross-references

- `skills/build-outbound-engine/SKILL.md` — the engine spec that consumes this taxonomy
- `skills/classify-dm-intent/SKILL.md` — the runtime classifier
- `skills/write-dm-sequence/SKILL.md` — per-class sequence writer
- `reference/frameworks/dm/cold-sequence-architecture.md`
- `reference/frameworks/dm/warm-sequence-architecture.md`
- `reference/frameworks/dm/liked-post-sequence-architecture.md`
- `reference/frameworks/dm/comment-for-lead-magnet-sequence-architecture.md`
- `reference/frameworks/dm/qualification-thread-design.md`
- `reference/frameworks/dm/booking-gate-construction.md`
- `reference/swipe-file/dm-threads/` — annotated examples per class
- `INVARIANTS.md` N-5 — never run outbound DMs without intent classification

---

*Framework version 1.0.0 — 2026-05-03*
