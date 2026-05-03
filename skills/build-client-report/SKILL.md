---
name: build-client-report
slug: /build-client-report
agent: client-reporter
department: fulfillment
output_format: monthly-client-report
required_profiles:
  client.slug: declared
  attribution_data: available
  offer_deliverables_spec: exists
allowed_scopes:
  - single-client
output_path:
  single-client: outputs/{date}/client-reports/{client_slug}-{period}.md
---

# /build-client-report — Monthly Client Report

> **Purpose.** Build the monthly client report. The artifact that justifies the retainer + drives renewal. Output is signal-dense, attribution-clean, decision-ready. The client reads it in <10 minutes and walks away with: (1) what shipped, (2) what worked, (3) what we're doing next month, (4) what they need to decide.
>
> **Bar.** A monthly report that doesn't trigger a "good — keep going" reaction is a renewal-at-risk signal. The client should finish the report wanting to forward it to their team, not file it under "too long".

## When to invoke

- **Cron — 1st of each month at 10:00 (per active client).** Per `paperclip.manifest.yaml`.
- Manually for mid-month "executive update" requests
- Required input to renewal conversations (operator pulls last 3 months of reports before any renewal call)
- Triggered by client request (rare — most don't ask, but always available)

## Required inputs

1. `client_slug` declared
2. Reporting period (default: previous calendar month; configurable for biweekly or quarterly)
3. Attribution data for the period (LinkedIn analytics, CRM exports, calendar data, third-party tools — whatever the client uses)
4. Offer deliverables spec from `/onboard-client` Step 8 + offer scope from contract
5. Success metrics list (defined in `/onboard-client` Step 8 — the client agreed these are how the engagement is judged)
6. Last 3 months of monthly reports (for trend continuity)
7. `/voice-drift-detector` audits for the period (drift trend section)
8. `/post-approval-tracker` weekly digests (approval health section)
9. `/build-attribution-trace` output for the period (channel attribution section)

## The build sequence

### Step 1 — Period scope load

```yaml
report_period:
  client_slug: {slug}
  period_start: {date}
  period_end: {date}
  prior_period_for_comparison: {date_range}
  offer_tier: {tier}
  success_metrics: [{list from onboarding}]
```

### Step 2 — Deliverable inventory

Every artifact shipped this period, counted against the offer commitment:

```yaml
deliverables:
  posts_shipped: { committed: N, actual: M, variance: % }
  dms_sent: { committed: N, actual: M, variance: % }
  leads_sourced: { committed: N, actual: M, variance: % }
  calls_booked: { committed: N, actual: M, variance: % }
  applications_received: { committed: N, actual: M, variance: % }
  monthly_strategy_call: { committed: 1, actual: 0/1 }
```

Variance ≥ +/-15% triggers a note in the report ("over-delivered Authority posts to backfill ICP-mismatch from December" — context, not just number).

### Step 3 — Outcome metrics

Per success metric from onboarding, with prior-period comparison:

| Metric | This period | Prior period | Δ | Trend |
|---|---|---|---|---|
| Impressions / month | {N} | {M} | +/-X% | improving / stable / declining |
| Profile clicks / month | {N} | {M} | +/-X% | ... |
| Inbound DMs from content / month | {N} | {M} | +/-X% | ... |
| Qualified DMs (per /classify-dm-intent) | {N} | {M} | +/-X% | ... |
| Calls booked / month | {N} | {M} | +/-X% | ... |
| Applications received | {N} | {M} | +/-X% | ... |
| Closed pipeline value | {$} | {$} | +/-X% | ... |
| Attribution-traced revenue | {$} | {$} | +/-X% | ... |

Each metric ≥ healthy band per the workspace's metric ranges (cross-reference `workflows/divisions/acquisition.md` health metrics table).

### Step 4 — Best-performing this period

Top 3 posts (by client's primary metric), top 1-2 DM sequences (by reply-rate-to-qualified-rate ratio), top 1 call source (by close rate). Each entry has a "why this worked" hypothesis grounded in the workspace's frameworks (hook formula, virality mechanism, intent class match).

```markdown
## Top 3 posts this period

### {post date} — {pillar} — {hook formula}
- Impressions: N
- Engagements: N (engagement rate %)
- Profile clicks: N
- Inbound DMs from this post: N

**Why it worked:** {hypothesis grounded in /score-hook axes — e.g., "stake-axis 18/20 because the post named a specific $-amount-at-risk; pattern-break 17/20 because the hook contradicted the dominant ICP belief"}.
```

### Step 5 — Issues + actions taken

Anything that surfaced this period — voice-drift events, approval delays, sourcing gaps, scope tension — with what was done. The client should NOT discover an issue from the report; they should see issues + the AM's response in the same paragraph.

```markdown
## Issues + actions

- **Week 2 voice-drift soft-fail (similarity 0.76, threshold 0.80):** AM recalibrated ghostwriter against last 10 reference posts; week-3 audit returned to 0.83. Root cause: template-default leak on Authority pillar. Prevention: added 5 client-specific phrases to phrases_to_avoid list.
- **Week 3 approval-channel silence (4 days no response):** AM nudged via secondary channel; client confirmed travel + caught up over weekend. Approval rate held at 92% for the period.
- {etc}
```

### Step 6 — Next month plan

3-5 bullets — what we're doubling down on, what we're testing, what we're retiring. The plan must be SPECIFIC (not "ship more posts") and tied to the period's evidence.

```markdown
## Plan for {next month}

1. **Doubling down on {pillar}:** Top 2 posts this month were both Authority; pillar mix shifts +5% to Authority next month
2. **Testing {hypothesis}:** {specific A/B with metric to judge}
3. **Retiring {pattern}:** {what stopped working and why we're stopping it}
4. **Sourcing focus:** {pillar where sourcing was thin — specific prompts AM is asking client for}
5. **DM pivot:** {if applicable — e.g., "shifting comment-magnet ratio from 20% to 30% based on this period's L-class M2 conversion lift"}
```

### Step 7 — Decisions client needs to make

1-3 concrete asks. Not "thoughts?" — actual decisions:

```markdown
## Decisions for you

1. **Approve next month's pillar shift?** (+5% Authority, -5% Pier — see Plan section)
2. **Increase DM volume from 25/day to 40/day?** Capacity available; ICP fit at current quality bar (88% per source-lead-list audit). Cost: same. Upside: ~15-20 more qualified replies/month at current conversion.
3. **Cohort recruit conversation:** You mentioned wanting to recruit a cohort by Q3. Want to schedule a 30-min strategy call this month to map the launch sequence?
```

### Step 8 — Output emission

Three artifacts, all from the same data:

1. **Full report markdown:** `outputs/{date}/client-reports/{slug}-{period}.md` (typically 800-1500 words, scannable in 10 minutes)
2. **1-page summary:** the metrics table + the 3 plan bullets + the decisions section (for the client's executive team or board)
3. **Slack/email-ready intro paragraph:** 80-120 word forwardable summary the client can paste

Optional appendix: chart visualizations brief for designer (if client has standing visual report request).

## Verification checklist

1. Period dates correct and matched to attribution-data window
2. Every committed deliverable accounted for (no silent variance)
3. Every success metric reported with prior-period comparison
4. Top performers have "why this worked" hypothesis grounded in framework
5. Every issue has an action paired with it (not unaddressed)
6. Next-month plan items are specific, evidence-tied, measurable
7. Decisions section asks for actual decisions (not opinions)
8. Voice-drift trend included for the period
9. Approval-rate trend included
10. Attribution trace included with confidence intervals
11. **Blind Output Test:** would a B2B founder paying $5K-$25K/month feel this report justifies the spend? Pass = ship; fail = iterate

## Common failure modes

| Failure | Likely cause | Fix |
|---|---|---|
| Report >2000 words, client doesn't read | Padding instead of signal density | Cut to 800-1500; move appendices to separate doc |
| Metrics shown without context (just numbers) | Missing prior-period comparison + healthy band ref | Add Δ column + healthy band reference for every metric |
| "Top performer" claims with no why | Hypothesis missing | Re-run with /score-hook axis breakdown for top posts |
| Issues not surfaced (client discovers from another channel) | AM trying to hide problems | Hard rule: any drift, delay, gap surfaces in report — paired with action |
| Plan items vague ("ship more proof posts") | Plan disconnected from period evidence | Every plan item must cite period data ("this month's top post was Proof — going +5% on the pillar") |
| Decisions section asks for "thoughts" | Soft-asks erode authority | Reframe to actual decisions client must make this month |
| BOT failed (report sounds like agency boilerplate) | Voice/positioning generic | Rewrite with client-specific framing in opening + closing paragraphs |

## Cross-skill routing

- **Upstream:** `/voice-drift-detector` (drift trend), `/post-approval-tracker` (approval health), `/build-attribution-trace` (channel attribution), `/run-client-dm-ops` (DM ops metrics)
- **Sibling:** `/build-attribution-trace` for the channel section
- **Downstream:** Renewal conversations; `/pipeline-pulse` (per-client health rollup); `/strategic-advisory` if client raises strategic question after report
- **On underperformance period:** triggers `/diagnose-bottleneck` for the symptom

## Cross-references

- `agents/client-reporter.md` (Cycle 2 — TBD) — owning persona
- `agents/fulfillment-head.md`, `agents/agency-director.md`
- `INVARIANTS.md` A-9 (every action leaves a receipt), N-3 (no invented metrics)
- `spec/BLIND-OUTPUT-TEST.md` — report passes BOT before delivery
- `skills/onboard-client/SKILL.md` — success metrics source
- `skills/voice-drift-detector/SKILL.md`, `skills/post-approval-tracker/SKILL.md`, `skills/build-attribution-trace/SKILL.md`
- `skills/run-client-dm-ops/SKILL.md`
- `skills/score-hook/SKILL.md` — top-performer hypothesis source
- `workflows/divisions/acquisition.md` — health metric ranges
- `paperclip.manifest.yaml` — 1st-of-month cron

---

*Skill version 1.0.0 — 2026-05-03*
