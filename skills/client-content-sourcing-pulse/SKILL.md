---
name: client-content-sourcing-pulse
slug: /client-content-sourcing-pulse
agent: account-manager
department: fulfillment
output_format: content-sourcing-pulse-report
required_profiles:
  client.slug: declared
allowed_scopes:
  - single-client
output_path:
  single-client: outputs/{date}/sourcing-pulse/{client_slug}-{week}.md
---

# /client-content-sourcing-pulse — Weekly Content Sourcing Audit

> **Purpose.** Measure whether the client is providing enough raw input (voice notes, story prompts, case-study moments, opinion-takes) for the ghostwriter to produce non-templated posts. Output is a 1-page sourcing-health brief + 3-5 specific prompts for the AM to send to the client this week.
>
> **Bar.** A ghostwriter without raw client input reverts to runtime defaults — and runtime defaults churn. Sourcing pulse runs weekly so the AM never asks the client "send me content ideas" generically — they ask specific prompts that backfill the next week's calendar gaps.

## When to invoke

- **Cron — every Wednesday 10:00 (weekly per active client).** Per `paperclip.manifest.yaml`. Wednesday timing gives the AM 2 days to source before the next week's calendar build.
- After any pillar runs out of raw material (surfaces from `/build-posting-calendar` Step 3 hook-library check)
- Before monthly client report (sourcing-cadence trend is a report indicator)
- Manually when client engagement drops (sourcing volume often leads disengagement signal)

## Required inputs

1. `client_slug` declared
2. Last 7 days of client-provided raw input across all channels: voice notes (Loom/Voxer/Slack), text drops (Slack/email), screenshots, transcripts, peer-conversation summaries
3. Next 7 days of calendar slots from `/build-posting-calendar` (so we know what raw material is needed)
4. Client's content_strategy subprofile — pillar mix + post types
5. Last 30 days of client engagement metrics on their own posts (which posts client commented on, replied to, reshared) — these are sourcing signals too

## The audit sequence

### Step 1 — Sourcing inventory (last 7 days)

Pull every input the client provided in last 7 days. Categorize:

```yaml
sourcing_inventory:
  voice_notes: { count: N, total_minutes: M, source: [loom/voxer/slack/etc] }
  text_drops: { count: N, total_words: M }
  screenshots: { count: N, types: [post-of-someone-elses, dashboard, conversation] }
  transcripts: { count: N, source: [recorded-call, podcast-appearance, webinar] }
  peer_conversations: { count: N, summary_provided: bool }
  total_raw_input_score: 0-100   # weighted aggregation
```

### Step 2 — Per-pillar coverage check

For each of the 5 content pillars, check if client has enough raw input for next 7 days of calendar slots in that pillar:

```yaml
pillar_coverage:
  authority:
    next_7d_slots: N
    raw_input_available: M items
    status: covered | thin | gap
  proof:
    next_7d_slots: N
    raw_input_available: M items
    status: covered | thin | gap
  personality:
    next_7d_slots: N
    raw_input_available: M items
    status: covered | thin | gap
  offer-bridge:
    next_7d_slots: N
    raw_input_available: M items
    status: covered | thin | gap
  pier:
    next_7d_slots: N
    raw_input_available: M items
    status: covered | thin | gap
```

Status definitions:
- `covered`: ≥2 raw inputs per slot
- `thin`: 1 raw input per slot (post can ship but quality at-risk)
- `gap`: 0 raw inputs per slot (post will revert to template default OR can't ship)

### Step 3 — Sourcing gap detection

For each `gap` pillar:
- Surface as priority-1 in the brief
- AM action: use prompts from Step 5 to source raw material this week
- If gap persists 2+ weeks, escalate (could be client disengagement OR pillar mismatch)

For each `thin` pillar:
- Surface as priority-2
- AM action: optional sourcing OR fall back to last-month material with diminishing-returns warning

### Step 4 — Sourcing-cadence trend

Rolling 4-week input volume per client. Decline pattern is a leading indicator of engagement risk:

```yaml
cadence_trend:
  week_minus_4: total_raw_input_score
  week_minus_3: ...
  week_minus_2: ...
  week_minus_1: ...
  current_week: ...
  trend: stable | declining | growing
  declining_weeks_in_row: 0-N
```

If `declining_weeks_in_row ≥ 3`: surface as retention risk to operator.

### Step 5 — Suggested prompts for client

3-5 specific prompts the AM sends the client this week to backfill the gap pillars. Prompt design rules:
- Specific, not generic ("send me content ideas" → BAD)
- Anchored to a recent client moment ("you mentioned X on Monday's call — can you record a 2-min voice note on it?" → GOOD)
- Single-channel, single-format (one voice note OR one text drop, not both)
- Actionable in <5 minutes for the client
- Aligned to the gap pillar (so the input directly fills next week's calendar)

Example prompts (anonymized templates):

| Pillar gap | Prompt template |
|---|---|
| Authority | "You closed [specific deal type] last [period] using [mechanism]. Can you record a 90-sec voice note on the framework?" |
| Proof | "[Client of yours] hit [outcome] last week — can you send me 3-5 sentences on the before/after for a case study post?" |
| Personality | "You said [opinion] in our last call — can you elaborate in a 60-sec voice note? I want to capture the conviction in your voice." |
| Offer-bridge | "[Inbound DM you got this week] — can you send me a screenshot + 1 sentence on what you'd say in person?" |
| Pier | "You disagreed with [framework] yesterday — what's your one-sentence reframe?" |

### Step 6 — Output emission

The Wednesday brief, sent to AM:

```markdown
# Content Sourcing Pulse — {Client Slug} — Week of {date}

**Total raw input score (last 7d):** {0-100}
**Sourcing cadence trend:** stable / declining / growing — {context}

## Pillar coverage for next 7 days

| Pillar | Slots | Raw inputs available | Status |
|---|---|---|---|
| Authority | {N} | {M} | covered / thin / gap |
| Proof | {N} | {M} | covered / thin / gap |
| Personality | {N} | {M} | covered / thin / gap |
| Offer-bridge | {N} | {M} | covered / thin / gap |
| Pier | {N} | {M} | covered / thin / gap |

## Action: prompts to send client this week

1. **{Pillar gap}:** {prompt 1}
2. **{Pillar gap}:** {prompt 2}
3. **{Pillar gap}:** {prompt 3}

## Watchlist

- {if cadence declining 3+ weeks: surface to operator as retention risk}
- {if any pillar gap 2+ weeks: pillar mix may need rebalancing}
```

## Verification checklist

1. All 5 pillars audited (none missed)
2. Raw input categorized correctly (voice notes vs. text drops vs. screenshots — different ghostwriter handling)
3. Coverage status applied per pillar with explicit slot/input counts
4. Cadence trend computed with rolling 4-week window
5. Suggested prompts are SPECIFIC (anchored to a client moment), not generic
6. Prompts limited to 3-5 — more dilutes AM execution
7. Brief delivered Wednesday 12:00 local
8. Retention-risk flag raised if cadence declining 3+ weeks

## Common failure modes

| Failure | Likely cause | Fix |
|---|---|---|
| Brief shows "covered" but ghostwriter still produces template-default posts | Raw input volume high but quality thin (e.g., 30 voice notes but all "I don't know what to say") | Tighten input-quality scoring; AM coaches client on specific prompt structure |
| Prompts ignored by client week after week | Channel mismatch OR prompts too generic OR timing wrong | Switch channel; rewrite prompts with hyper-specific anchors; adjust send-time |
| Cadence growing but engagement on shipped posts dropping | Sourcing volume up but pillar mismatch — wrong pillars getting raw material | Audit pillar mix vs. ICP awareness; rebalance via /build-content-engine |
| Authority pillar always in `gap` | Client's expertise extraction is thin; profile doesn't capture their domain mastery | Re-run /extract-founder-voice with focus on Authority pillar; expand story bank |
| Pier pillar always in `gap` | Client uncomfortable with provocation/contrarian takes | Acceptable — drop Pier weight in pillar mix; reallocate to Authority + Personality |
| Brief surfaces 3+ gap pillars simultaneously | Client disengaged this week | Surface to operator immediately; could be retention risk OR client out-of-office (check before escalating) |

## Cross-skill routing

- **Upstream:** `/onboard-client` Step 5 establishes content engine that this pulse measures against; `/build-content-engine` defines pillar mix
- **Sibling:** `/post-approval-tracker` (revision patterns inform sourcing gaps); `/voice-drift-detector` (drift sometimes traces to sourcing thinness — ghostwriter compensates with template defaults)
- **Downstream:** `/build-posting-calendar` (sourcing inventory feeds calendar build); `/build-hook-library` (raw input is hook fuel); `/build-client-report` (sourcing trend is report section)
- **On retention risk:** `agents/agency-director.md` notified for operator-led check-in

## Cross-references

- `agents/account-manager.md` (Cycle 2 — TBD)
- `agents/fulfillment-head.md`
- `INVARIANTS.md` A-9 (every action leaves a receipt)
- `ENCODING.md` — `content_strategy.client_content[{client_slug}]` schema
- `skills/build-content-engine/SKILL.md`, `skills/build-posting-calendar/SKILL.md`, `skills/build-hook-library/SKILL.md`
- `skills/post-approval-tracker/SKILL.md`, `skills/voice-drift-detector/SKILL.md`, `skills/build-client-report/SKILL.md`
- `paperclip.manifest.yaml` — Wednesday 10:00 cron

---

*Skill version 1.0.0 — 2026-05-03*
