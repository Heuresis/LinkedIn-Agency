---
name: build-attribution-trace
slug: /build-attribution-trace
agent: client-reporter
department: fulfillment
output_format: attribution-trace-document
required_profiles:
  client.metric_source_data: available
allowed_scopes:
  - single-client
  - agency-self
output_path:
  single-client: outputs/{date}/attribution-traces/{client_slug}-{period}.md
  agency-self: outputs/{date}/attribution-traces/agency-{period}.md
---

# /build-attribution-trace — Lead-to-Revenue Attribution Trace

> **Purpose.** Build the cleanest attribution trace possible from available LinkedIn + CRM signals. Multi-touch, dark-social-aware, confidence-scored. Used in monthly client reports, used in `/pipeline-pulse` to identify channel weight, used to defend the engagement against "what's actually working" pushback.
>
> **Bar.** LinkedIn attribution is structurally messy (multi-touch, dark social, content-influenced-but-not-clicked). The trace must be honest about uncertainty — confidence-scored, not bulldozed into a single number that overstates clarity.

## When to invoke

- Monthly per active client (called by `/build-client-report`)
- Quarterly for the agency itself (called by `/pipeline-pulse` Layer 2 channel attribution)
- Ad-hoc when operator or client asks "where did this lead come from"
- Before renewal conversations (3-month rolling trace)

## Required inputs

1. `scope` declared (single-client OR agency-self)
2. Source data inventory — at minimum:
   - LinkedIn analytics (post impressions, engagements, profile clicks, follower growth)
   - CRM data (every contact in the funnel for the period — source field, touchpoint history)
   - Calendar data (booked calls + held calls + outcomes)
   - DM thread metadata (per `/classify-dm-intent` outputs)
3. Optional: Shield/Taplio analytics export, UTM-tagged link tracking, CMS dark-social signals
4. Period (default: previous calendar month; quarterly for renewal/pipeline-pulse use)
5. List of closed deals in the period (the events being attributed)

## The trace sequence

### Step 1 — Source data inventory

Pull every available data source. Categorize confidence per source:

| Source | Confidence | Use |
|---|---|---|
| Direct UTM-tracked clicks | HIGH | Hard attribution — direct evidence |
| CRM source field (manually tagged at lead capture) | MEDIUM-HIGH | Strong attribution if hygiene maintained |
| LinkedIn analytics post-by-post engagements | MEDIUM | Touchpoint evidence |
| DM thread metadata (which sequence, when) | HIGH | Hard attribution for DM-sourced deals |
| Calendar booking source field | MEDIUM-HIGH | Useful when attached to the booking |
| Prospect self-report on call ("I saw your post about X") | MEDIUM | Counts as influenced touchpoint, not first-touch |
| Inferred from temporal patterns (lead arrived 2 days after viral post) | LOW | Useful as proxy, never authoritative |
| Dark social (Shield/Taplio top-funnel impressions) | LOW-MEDIUM | Supports influence claims, never first-touch claim |

### Step 2 — Touchpoint extraction

Per closed deal in the period, build the touchpoint timeline. Every recorded interaction:

```yaml
deal_id: {anonymized_slug}
deal_value: {$ MRR or one-time}
deal_close_date: {date}
touchpoints:
  - timestamp: {date}
    type: post-impression / post-engagement / profile-visit / dm-sent / dm-received / call-booked / call-held / proposal-sent / closed
    source: organic-feed / cold-dm / inbound-dm / direct-link / referral
    confidence: HIGH / MEDIUM / LOW
    evidence: {source citation}
```

Goal: chronological touchpoint sequence per closed deal.

### Step 3 — First-touch + last-touch tagging

Per deal, identify:
- **First touch:** the first recorded touchpoint that put the lead in the funnel (often a post impression or cold DM)
- **Last touch:** the last touchpoint before close (often a call OR proposal email)

```yaml
deal_id: {slug}
first_touch:
  type: post-impression
  source: organic-feed
  date: {date}
  confidence: MEDIUM
last_touch:
  type: call-held
  source: discovery-call
  date: {date}
  confidence: HIGH
```

### Step 4 — Multi-touch journey reconstruction

The full chronological sequence with assigned weights. Default weighting (configurable per client):
- First touch: 30%
- Last touch: 30%
- Middle touchpoints: 40% distributed evenly

Per deal:

```yaml
deal_id: {slug}
journey:
  - touchpoint: {date} - first - post-impression - 30% weight
  - touchpoint: {date} - middle - dm-reply - 13% weight
  - touchpoint: {date} - middle - profile-visit - 13% weight
  - touchpoint: {date} - middle - call-booked - 14% weight
  - touchpoint: {date} - last - call-held - 30% weight
total_weight: 100%
```

### Step 5 — Channel attribution per deal

Aggregate touchpoint weights into channel buckets:

```yaml
deal_id: {slug}
channel_attribution:
  cold_dm: 26%
  inbound_dm: 0%
  inbound_content: 30%
  referral: 0%
  outbound_personal: 14%
  proposal_close: 30%
primary_channel: { name: inbound_content_then_outbound_close, confidence: MEDIUM }
```

`primary_channel` is the dominant trace pattern — useful for "where did this come from" reporting. Confidence reflects underlying source confidence.

### Step 6 — Dark-social inference

For deals where attribution is weak (limited touchpoint data), use proxy evidence:

| Proxy signal | Inference |
|---|---|
| Prospect mentioned specific post in call | Content-influenced; flag as "content-influenced (self-reported)" |
| Lead arrived within 7 days of a >50K-impression post | Possibly content-driven; flag as "content-influenced (temporal proxy)" with LOW confidence |
| Warm DM after months of post-engagement (Shield/Taplio shows recurring impressions on prospect's profile) | Long-runway content nurture; flag as "content-nurtured-warm-DM" |

Dark-social inference is recorded with explicit LOW confidence — never overstated.

### Step 7 — Aggregate channel weight (period-level)

Per period, what % of closed revenue traces to each channel:

```yaml
period: {date_range}
total_closed_revenue: {$}
channel_revenue_attribution:
  cold_dm: { revenue: $X, percent: Y%, confidence_band: ±Z% }
  inbound_dm: { revenue: $X, percent: Y%, confidence_band: ±Z% }
  inbound_content: { revenue: $X, percent: Y%, confidence_band: ±Z% }
  referral: { revenue: $X, percent: Y%, confidence_band: ±Z% }
  outbound_personal: { revenue: $X, percent: Y%, confidence_band: ±Z% }
unattributed_revenue: { amount: $X, percent: Y% }   # always non-zero — be honest about it
```

Confidence bands reflect aggregate uncertainty. A period dominated by HIGH-confidence sources has tight bands; LOW-confidence sources widen them.

### Step 8 — Output emission

The trace document:

```markdown
# Attribution Trace — {Scope} — Period {date_range}

## Summary

Total closed revenue this period: ${X}. Attribution confidence: HIGH / MEDIUM / LOW.

| Channel | Revenue | % | Confidence band |
|---|---|---|---|
| Inbound content | $X | Y% | ±Z% |
| Cold DM | $X | Y% | ±Z% |
| ... | | | |
| Unattributed | $X | Y% | — |

## Per-deal traces

[per-deal journey + channel attribution]

## Method notes

- First-touch + last-touch weight: 30% each. Middle touchpoints split remaining 40%.
- Confidence per source: HIGH (UTM, DM thread metadata), MEDIUM (CRM source field, post-engagement records), LOW (temporal proxy, dark-social inference).
- Unattributed revenue is always non-zero. We do not force assignment when evidence is absent.

## Visualization brief for designer

[chart spec — channel pie + per-deal sankey OR timeline]
```

## Verification checklist

1. Source data inventory complete — all available sources pulled, none silently dropped
2. Per-source confidence assigned and recorded
3. Every closed deal in period has a touchpoint timeline
4. First-touch + last-touch tagged per deal
5. Multi-touch weighting applied per workspace default OR client-specific config
6. Dark-social inference flagged explicitly with LOW confidence (never overstated)
7. Aggregate channel weights include unattributed bucket (non-zero, honest)
8. Confidence bands shown per channel (tight for HIGH-source-dominated, wide for LOW)
9. Method notes included in output (so client understands the model)
10. **Blind Output Test:** would a B2B founder reading this trust the trace? (Honest uncertainty > false certainty.) Pass = ship.

## Common failure modes

| Failure | Likely cause | Fix |
|---|---|---|
| Report claims 100% attributed (no unattributed bucket) | Method forced assignment instead of accepting uncertainty | Add unattributed bucket; widen confidence bands; revise method note |
| Single channel claims >80% with LOW confidence | Over-relied on temporal proxy or self-report | Downgrade primary_channel confidence; show sub-attribution alternatives |
| Trace differs week-to-week wildly | Source data hygiene issues (CRM source field empty for many deals) | Audit data hygiene; require source field at lead capture |
| Cold DM claims revenue but DM thread metadata missing | DM ops not logging through `/classify-dm-intent` | Backfill via thread review; fix logging upstream |
| Inbound content claims revenue but no UTM tracking + no self-report | Pure inference attribution | Either add UTM tracking before next period OR mark as LOW confidence in current trace |
| Client questions a specific deal's attribution | Edge case; trace doesn't match client's mental model | Walk through touchpoint timeline together; if client knows of touchpoint not in data, add as MEDIUM confidence |

## Cross-skill routing

- **Upstream:** `/run-client-dm-ops` (DM thread metadata), `/classify-dm-intent` (intent class per touchpoint), CRM data (manual + automated source-tagging)
- **Sibling:** `/build-client-report` (consumes the trace), `/pipeline-pulse` (consumes for Layer 2 channel attribution)
- **Downstream:** Renewal conversations (3-month rolling trace), `/strategic-advisory` (channel weight informs strategy questions)

## Cross-references

- `agents/client-reporter.md` (Cycle 2 — TBD) — owning persona
- `agents/agency-director.md` — consumer for /pipeline-pulse
- `INVARIANTS.md` N-3 (no invented metrics), A-9 (every action leaves a receipt)
- `spec/BLIND-OUTPUT-TEST.md` — trace passes BOT before client delivery
- `skills/build-client-report/SKILL.md`, `skills/pipeline-pulse/SKILL.md`
- `skills/classify-dm-intent/SKILL.md`, `skills/run-client-dm-ops/SKILL.md`
- `workflows/divisions/acquisition.md` — touchpoint definitions

---

*Skill version 1.0.0 — 2026-05-03*
