---
name: build-posting-calendar
slug: /build-posting-calendar
agent: content-strategist
department: marketing
output_format: 30-day-posting-calendar
required_profiles:
  content_strategy: 70                # recursive: agency-content or client-content (target scope)
  brand_voice: 60                     # recursive: agency-voice or client-voice
  ideal_customer_profile: 60          # recursive: agency-icp or client-icp
allowed_scopes:
  - agency-content
  - client-content
output_path:
  agency-content: outputs/{date}/calendar-agency-{period}.md
  client-content: outputs/{date}/calendar-{client_slug}-{period}.md
---

# /build-posting-calendar — 30-Day Editorial Calendar

> **Purpose.** Build the 30-day editorial calendar for a single subject (the agency operator OR a specific client). Output is a day-by-day schedule with: pillar slot, post type, hook drawn from the hook library, draft cue (3-line brief for the writer), publishing window, cross-post target, and comment-magnet target. The calendar is what `/write-linkedin-post` and `/ghostwrite-client-post` pull from each day.
>
> **Bar.** A ghostwriter handed this calendar should walk to day_17 and produce a publish-ready post in <30 minutes without re-reading the engine. The calendar is the daily operating contract — pillar discipline, hook freshness, voice scope, and conversion bridges converge into one file. Upstream: `/build-content-engine` + `/build-hook-library`. Downstream: every post-production skill consumes day-N entries.

## When to invoke

- New content engine just built (run immediately after `/build-content-engine` for the same scope)
- New client onboarded (build `client-content` calendar for the client_slug after their content engine ships)
- Monthly cadence — every 30 days, the calendar is rebuilt, never extended (extension breeds drift)
- Phase shift in `reference/playbooks/hybrid-full-stack.md` (Phase 1→2 or 2→3) — pillar mix changes, calendar rebuilds
- Engagement plateau ≥3 weeks (signal that pillar mix or hook library is stale; calendar rebuild forces a refresh)
- Offer change — content-to-conversion bridges shift, calendar must realign

## Required inputs

1. Content engine spec for the target scope (≥70% — from `/build-content-engine`)
2. Hook library for the target scope (≥30 fresh hooks — from `/build-hook-library`; if fewer, surface gap and run `/build-hook-library` first)
3. Voice profile for the target scope (≥60%)
4. ICP profile for the target scope (≥60%)
5. Subject's current phase per `reference/playbooks/hybrid-full-stack.md` (Phase 1 / 2 / 3 — for agency-content) OR the client's solution-awareness target (for client-content)
6. Subject's local timezone (for publishing-window calibration)
7. Active offer/lead-magnet inventory (for comment-magnet day assignment)
8. The 30-day window's start date

## The build sequence (7 steps)

### Step 1 — Pillar mix calibration to phase (10 min)

Pillar weighting is not static — it shifts with the operator's pipeline phase (or the client's solution-awareness target). Calibrate before allocating slots.

**For agency-content (operator's own LinkedIn), mix shifts by phase per `reference/playbooks/hybrid-full-stack.md`:**

| Phase | Months | Authority | Personality | Proof | Offer-bridge | Pier |
|---|---|---|---|---|---|---|
| **Phase 1** | 1-4 | 30% | 30% | 25% | 5% | 10% |
| **Phase 2** | 5-12 | 35% | 25% | 25% | 10% | 5% |
| **Phase 3** | 13+ | 30% | 20% | 30% | 15% | 5% |

**Phase logic.** Phase 1 leans Authority + Personality (no audience yet — content is seeded for future readers; Offer-bridge is wasted because there's no inbound flow). Phase 2 balances Authority + Proof + Offer-bridge growing as inbound starts converting. Phase 3 leans Proof + Offer-bridge dominant (proof pool funds program tier, Offer-bridge converts both retainer + program), Personality maintained at lower volume to keep brand soul.

**For client-content, mix calibrates to client's ICP solution-awareness state:**

| Client ICP awareness | Authority | Personality | Proof | Offer-bridge | Pier |
|---|---|---|---|---|---|
| **Problem-aware** (build category) | 25% | 30% | 15% | 10% | 20% |
| **Solution-aware** (default majority) | 35% | 20% | 30% | 10% | 5% |
| **Most-aware / Product-aware** | 25% | 15% | 25% | 30% | 5% |

**Output of Step 1:** target % per pillar for the 30 days, plus the absolute slot count per pillar (e.g., 30 days × 35% Authority = 10-11 Authority slots). Round so the slot counts sum to exactly the days in the cadence (5x/week × 4.3 weeks ≈ 22 posts, 7x/week = 30 posts, etc., per cadence in `content_strategy.{scope}.posting_cadence.frequency`).

### Step 2 — Post-type allocation per pillar (10 min)

Each pillar maps to 2-4 post types from the engine spec. Allocate post types within each pillar's slot count to preserve format variety.

| Pillar | Eligible post types | Slot rule |
|---|---|---|
| **Authority** | Contrarian-reframe, Named-mechanism, List-promise, Authority post | No same post type twice in a 7-day window |
| **Proof** | Before-after, Case-study post, Receipt-screenshot post, Failure-lesson | Mix narrative (case-study) with metric-led (receipt) |
| **Personality** | Personal-to-business, Day-in-life, Failure-lesson, Loop-open story | At least 1 Day-in-life per 14 days for behind-the-scenes signal |
| **Offer-bridge** | Hook-loop-CTA, Thought-proof-CTA, Comment-magnet post | Always pair Offer-bridge with a comment-trigger CTA per Step 6 |
| **Pier** | Pier post, Contrarian-reframe (provocation variant), Pattern-break | Pier slots cluster Tue/Thu (highest engagement windows) |

For each day's slot, assign exactly one post type. The calendar emits both `pillar` and `post_type` per day.

**Variety rule.** Across any rolling 7-day window, no single post type appears more than twice. Across any 14-day window, no single post type appears more than 3 times. Repetition kills hook freshness AND triggers algorithmic skim.

### Step 3 — Hook library pull (15 min)

For each day, select exactly one hook from the subject's hook library that matches:

1. **Pillar fit** — hook formula must match the pillar (e.g., contrarian-reframe formula → Authority pillar; specific-stakes → Personality + Proof; named-mechanism → Authority + Proof)
2. **Post type fit** — the hook must lead naturally into the assigned post type's structure (per `reference/frameworks/content/hook-anatomy.md`)
3. **Voice fit** — the hook must score ≥4 on the voice-match axis of the hook scoring rubric (per `/score-hook`)
4. **Freshness** — no hook reused within the 30-day window. No hook formula used 3 days in a row.

**Hook library sufficiency check.** The hook library must contain ≥30 fresh hooks (one per day) for a 30-day calendar at 30 posts. For 5x/week cadence, ≥22 fresh hooks. If the library is short:

```
HOOK_LIBRARY_GAP — required: 30, available: 18
Action: halt calendar build, run /build-hook-library for {scope} to top up to ≥30, then resume.
```

**Pull priority order:** (1) proven hooks from the subject's past top-decile posts, (2) hook formulas from `reference/frameworks/content/hook-anatomy.md` adapted to the subject's voice, (3) swipe-file hooks from `reference/swipe-file/hooks/` re-skinned to the subject's voice. Always tag the hook's source in the calendar entry's metadata so the writer downstream knows whether to render verbatim or adapt.

### Step 4 — Draft cue generation per day (10 min)

The draft cue is a 3-line brief — the smallest unit of input that lets `/write-linkedin-post` or `/ghostwrite-client-post` produce a post without re-deriving strategy. The actual full post is generated by those skills; the calendar gives them the brief.

**Draft cue structure (3 beats):**

1. **Opening beat — the hook.** The exact hook from Step 3. Verbatim. (The writer may smooth ±1 word for voice; structural change is forbidden without re-running `/score-hook`.)
2. **Middle beat — the proof / specific / mechanism.** One sentence naming the concrete element the body must deliver. Examples: "Receipt: client's screenshot showing 14 calls in week 6 vs. 3 in week 1." / "Mechanism: the Recursive ICP Method, specifically the per-client subprofile loader." / "Story specific: the Friday-night DM that closed at 11 PM."
3. **Closing beat — the CTA or loop close.** What the post asks the reader to do (or the loop the post resolves). Examples: "Soft CTA: 'Comment 'PLAYBOOK' for the 12-page doc on this.'" / "Loop close: pay off the 'here's what happened next' tension with the specific outcome." / "Hard CTA: 'I have 3 spots open — reply 'INFO' for details.'"

**Cue length:** the entire 3-line cue must fit in 60 words total. Longer cues mean the writer is re-doing strategy, which means the calendar is doing too much.

**Draft cue example (one calendar day):**

```yaml
day_07:
  opening: "Most B2B founders post about what their product does. The ones who get inbound calls post about what their buyer is afraid of."
  middle: "Mechanism: the fear-mapping protocol — three fears the ICP holds about hiring an agency, sourced from the last 12 discovery calls."
  closing: "Soft CTA: 'Comment 'FEAR' for the 6-fear ICP map I use with clients.'"
```

### Step 5 — Publishing window assignment (5 min)

Each post lands in one of three windows, calibrated to the post's pillar and the subject's audience timezone distribution. Default windows (subject-local time):

| Window | Hours | Pillar fit | Why |
|---|---|---|---|
| **Morning** | 07:00-09:00 | Authority, Proof | B2B audience checks LinkedIn during commute / coffee window; Authority + Proof posts get highest dwell-time when readers are alert |
| **Midday** | 11:00-13:00 | Personality, Pier | Lunch-break scroll behavior favors lighter narrative + provocation; Personality posts harvest emotional engagement when reader's defenses are lower |
| **Evening** | 17:00-19:00 | Offer-bridge | End-of-day decision window — readers who engaged with morning Authority posts return to act; Offer-bridge CTAs convert at higher rate in this window |

**Calibration override.** For the first 30 days of any new subject (agency-operator OR new client), test windows as defaults. After day 30, compute per-window engagement rate from the prior 30 days' data and rebalance. Per-client overrides:

- Client's ICP geographically clustered outside subject's local TZ → shift windows to the ICP's commute hours (e.g., NYC operator with a London-clustered ICP → publish 02:00-04:00 NYC time = 07:00-09:00 London)
- Client's ICP industry signal (e.g., engineers vs. founders vs. enterprise execs) → shift per `reference/platforms/linkedin/algorithm-notes.md` engagement-window data
- Operator's content engine spec declares non-default time blocks → respect the engine spec, don't override here

**Avoid.** Mon AM (low attention — readers triaging the week), Fri PM (low attention — readers checking out), all weekends (low B2B engagement), holiday weeks unless explicitly opted-in for the agency's own ironic-counter-cycle play.

### Step 6 — Cross-post + comment-magnet plan (10 min)

Two intentional flags get assigned per day. Neither is accidental — both are designed.

**Cross-post flag (`repurpose_targets`).** 30-50% of calendar days carry a 30-second X repurpose that fires via `/repurpose-to-x` 1-3 days after the LinkedIn post lands. Selection rule: the post idea must compress to ≤280 chars without losing the hook's bite. Authority posts and Proof posts repurpose well; long Personality narratives don't. For agency-content, target 40% repurpose density (12 of 30 days). For client-content, target the client's contractual repurpose scope (typically 30%).

**Comment-magnet flag (`comment_magnet_target`).** 10-20% of calendar days are explicitly designed as comment-magnet posts — the comment triggers a DM with a magnet (per `/run-client-dm-ops` and `reference/frameworks/dm/intent-taxonomy.md` Class M — Comment-for-lead-magnet). For each magnet day, declare:

- `magnet_asset` — the lead-magnet file or playbook delivered (must already exist or be queued in same sprint)
- `trigger_word` — the specific word the reader comments (e.g., "PLAYBOOK", "MAP", "TAXONOMY"). Must be specific, unambiguous, and not a generic word likely to appear in unrelated comments
- `dm_sequence_slug` — the DM follow-up sequence that fires after the magnet delivery (per `/build-outbound-engine` Class M sequence)

Magnet days cluster on Tue/Wed/Thu mornings (highest comment volume → highest DM funnel feed). Never run two consecutive magnet days (saturates the DM channel and dilutes magnet perceived value).

### Step 7 — Calendar emission (10 min)

Emit the full 30-day calendar as a YAML block, plus a markdown summary table, plus a week-by-week thematic narrative.

**YAML block structure:**

```yaml
calendar:
  scope: agency-content | client-content
  client_slug: null | "<slug>"
  start_date: YYYY-MM-DD
  end_date: YYYY-MM-DD
  cadence: 5x-week | 7x-week | 3x-week
  total_posts: <integer>
  pillar_mix_target:
    authority: <pct>
    personality: <pct>
    proof: <pct>
    offer_bridge: <pct>
    pier: <pct>
  pillar_mix_actual:                  # computed from day allocations; must match target ±5%
    authority: <pct>
    personality: <pct>
    proof: <pct>
    offer_bridge: <pct>
    pier: <pct>
  days:
    - day: 1
      date: YYYY-MM-DD
      pillar: authority
      post_type: contrarian-reframe
      hook: { text: "...", formula: contrarian-reframe, source: proven | formula-adapted | swipe-file, last_used: null | YYYY-MM-DD }
      draft_cue: { opening: "", middle: "", closing: "" }
      publish_window: morning | midday | evening
      publish_time_local: HH:MM
      repurpose_targets: []           # ["x"] | ["x", "ig"] | []
      comment_magnet_target: null     # null | { magnet_asset, trigger_word, dm_sequence_slug }
      status: queued | drafted | scheduled | published | rejected
    - day: 2
      ...
    - day: 30
      ...
```

**Markdown summary table:** one row per day, columns = day | date | pillar | post_type | hook (truncated 60 chars) | window | repurpose | magnet.

**Week-by-week thematic narrative:** 4 short paragraphs (week 1 / week 2 / week 3 / week 4) explaining the editorial arc — what theme the week leans into, what the operator (or client) is trying to compound, what the reader should feel by the end of the week. The narrative gives the writer downstream the *why* behind the day-by-day allocation.

## Validation gate (mandatory before declaring done)

Seven checks, all must pass:

1. **Pillar mix sums to 100% and matches phase target ±5%.** If actual drifts >5% on any pillar, rebalance Step 1's slot allocation.
2. **No hook reused.** Every hook in the 30-day calendar is unique. If duplicated, replace one with a fresh pull from Step 3.
3. **Hook library has ≥30 fresh hooks (or gap surfaced).** Library sufficiency check from Step 3 has either passed or the gap-and-halt routine has fired.
4. **Every day has a complete draft cue.** No day is missing the opening / middle / closing beats. No cue exceeds 60 words.
5. **Repurpose + magnet days are intentional, not accidental.** Repurpose density 30-50%. Magnet density 10-20%. No two consecutive magnet days.
6. **For client-content: client's voice + ICP loaded recursively, not agency-level.** Calendar metadata declares `scope: client-content` + `client_slug: <slug>`; cues hydrate from `brand_voice.client_voices[slug]` and `ideal_customer_profile.client_icps[slug]` — verified by checking cue language against the client subprofile's `phrases_to_avoid`.
7. **Calendar dates align with publishing-window timezone.** All `publish_time_local` entries respect the subject's TZ; weekend + holiday exclusions honored.

If any check fails, halt and re-run the offending step. Per `INVARIANTS.md` A-5, downstream posting cannot begin from an ungated calendar.

## Output format

```yaml
---
artifact_type: 30-day-posting-calendar
scope: agency-content | client-content
client_slug: null | "<slug>"
phase: phase-1 | phase-2 | phase-3 | n/a-client
start_date: YYYY-MM-DD
end_date: YYYY-MM-DD
profile_completeness:
  content_strategy: <pct>
  brand_voice: <pct>
  ideal_customer_profile: <pct>
validation_gate: passed | failed | not-yet-run
profiles_used: [content_strategy, brand_voice, ideal_customer_profile, product_strategy, case_studies]
frameworks_used: [hook-anatomy, virality-engine, intent-taxonomy, hybrid-full-stack-playbook]
confidence: HIGH | MEDIUM | LOW
---

## Calendar — 30 days

{the YAML calendar block from Step 7}

## Pillar mix — target vs. actual

{table showing target % vs. actual % per pillar with delta}

## Daily summary table

{markdown table — day | date | pillar | post_type | hook | window | repurpose | magnet}

## Week-by-week thematic narrative

{4 paragraphs — week 1 theme / week 2 theme / week 3 theme / week 4 theme}

## Validation gate result

{7 checks with pass/fail; rebalance notes if any failed and was re-run}

## Hook library status

{count of fresh hooks remaining post-pull; flag if next build needs a top-up}

## Magnet + repurpose schedule

{3-6 magnet days with magnet_asset + trigger_word + dm_sequence_slug; 9-15 repurpose days with target platform + timing}

## Next actions

{queue first 5 days into /write-linkedin-post or /ghostwrite-client-post; schedule the comment-magnet DM sequences; set the 30-day rebuild trigger}
```

## Failure modes and guards

| Symptom | Likely cause | Fix |
|---|---|---|
| Calendar feels repetitive by week 3 | Pillar mix too narrow OR hook library too small | Re-run Step 1 with wider pillar spread; OR run `/build-hook-library` to top up to ≥40 hooks before next build |
| Engagement plateau by week 4 | Hooks ran out of novelty OR pillar shift not absorbed | Audit which post types are decaying; mid-month replace remaining slots with fresher hook formulas; queue a phase-shift check |
| Client voice drift after week 2 | Wrong voice scope loaded — agency voice contaminating client calendar | Verify `scope: client-content` and rerun `/build-content-engine` for `client_voice` if drift is structural; per `INVARIANTS.md` N-1 + N-6 |
| Calendar slot empty (no post that day) | Hook library gap mid-build OR draft cue could not resolve | Run `/build-hook-library` to fill, then regenerate the affected day's cue; never ship a calendar with empty slots |
| Magnet days fire but DMs don't follow | DM sequence slug missing OR runtime trigger not registered | Cross-check Step 6 outputs against `/run-client-dm-ops` and the runtime adapter — magnet without DM sequence = leak |
| Cross-post flagged but X repurpose never ships | `/repurpose-to-x` not queued at calendar emission time | Calendar emission must auto-queue repurpose tasks; verify the runtime trigger is firing on day-of-publish |
| Pillar mix drifts week 3-4 | Operator overrode Tuesday/Thursday slots ad-hoc and the override killed the math | Lock the calendar — overrides must come back to this skill, not edit the YAML in place |

## Cross-skill routing

**Upstream dependencies (must complete first):**

- `/build-content-engine` — produces the pillar definitions, post-type library, and posting cadence this skill consumes
- `/build-hook-library` — produces the hook pool this skill pulls from; if gap, this skill halts and routes back

**Downstream consumers (read this calendar):**

- `/write-linkedin-post` — pulls day-N entries for agency-content posts
- `/ghostwrite-client-post` — pulls day-N entries for client-content posts
- `/repurpose-to-x` — fires on calendar days flagged with `repurpose_targets: ["x"]`
- `/score-hook` — re-validates hooks at write-time if the cue cites a hook that's been edited

**Sibling coordination:**

- `/run-client-dm-ops` — receives the magnet days from Step 6; the DM sequences fire when comments arrive
- `/voice-drift-detector` — runs weekly across the 30-day window's published posts to catch drift early enough to recalibrate before next calendar build

## Cross-references

- `skills/build-content-engine/SKILL.md` — upstream; engine consumed at Steps 1-2
- `skills/build-hook-library/SKILL.md` — upstream; pool consumed at Step 3
- `skills/write-linkedin-post/SKILL.md` + `skills/ghostwrite-client-post/SKILL.md` — downstream consumers
- `skills/run-client-dm-ops/SKILL.md` — sibling for magnet day routing
- `agents/marketing-head.md` — owns calendar gating + pillar mix shifts
- `reference/frameworks/content/hook-anatomy.md` — hook taxonomy + scoring rubric
- `reference/frameworks/content/virality-engine.md` — first-12-impression bet, dwell-time, comment-trigger mechanics
- `reference/frameworks/content/pillar-architecture.md` — pillar definitions
- `reference/frameworks/dm/intent-taxonomy.md` — Class M routing for magnet days
- `reference/playbooks/hybrid-full-stack.md` — phase-aware pillar mix shifts (Step 1 source of truth)
- `reference/platforms/linkedin/algorithm-notes.md` — quarterly window + cadence updates
- `INVARIANTS.md` N-1, N-6, A-5, A-6 — voice scope rules + dependency chain

## Examples

See `examples/`:

- `example-agency-calendar-phase-1.md` — operator in months 1-4 (Authority + Personality lean)
- `example-agency-calendar-phase-2.md` — operator in months 5-12 (balanced + Offer-bridge growing)
- `example-client-calendar-saas-founder.md` — Solution-aware B2B SaaS founder client (Authority + Proof lean)
- `example-client-calendar-services-founder.md` — Problem-aware B2B services founder client (Personality + Pier lean)

---

*Skill version 1.0.0 — 2026-05-03*
