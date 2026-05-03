---
template_name: monthly-client-report
used_by_skill: /build-client-report (Step 8 — emits this template populated with period data)
output_format: monthly-client-report-doc
audience: signed client (founder / CMO / signatory) — the artifact that justifies the retainer and drives renewal
fillable_slots:
  - client_slug                            # kebab-case anonymized alias per ENCODING.md
  - client_legal_entity                    # the company name as it appears on the contract
  - reporting_period_start                 # YYYY-MM-DD (typically 1st of prior month)
  - reporting_period_end                   # YYYY-MM-DD (typically last day of prior month)
  - prior_period_start                     # YYYY-MM-DD (period before, for comparison)
  - prior_period_end                       # YYYY-MM-DD
  - offer_tier                             # retainer | retainer+ddm | retainer+content+ddm
  - offer_name                             # public-facing offer name
  - account_manager_slug                   # AM (anonymized slug)
  - report_delivered_date                  # YYYY-MM-DD
  - executive_summary_paragraph            # 1 paragraph, scannable, period overview + headline
  - deliverable_inventory_table            # committed vs actual table per /build-client-report Step 2
  - deliverable_variance_notes             # explanations for any +/-15% variances
  - outcome_metrics_table                  # success metrics with prior-period comparison
  - outcome_metrics_band_notes             # which metrics are in/out of healthy band
  - top_post_1                             # top performer #1 — date, pillar, hook formula, metrics, why-it-worked
  - top_post_2                             # top performer #2
  - top_post_3                             # top performer #3
  - top_dm_sequence_1                      # top DM sequence — class, reply rate, qualified rate, why-it-worked
  - top_dm_sequence_2                      # top DM sequence #2 (optional)
  - issues_actions_list                    # bulleted issues with paired actions taken
  - next_month_plan_bullets                # 3-5 specific bullets, evidence-tied
  - decisions_for_client_list              # 1-3 concrete asks (not "thoughts?")
  - voice_drift_trend                      # weekly drift values for the period
  - approval_rate_period                   # % of posts approved at first review
  - attribution_summary                    # channel attribution per /build-attribution-trace
  - one_page_summary_appendix              # forwardable 1-page version of the report
  - slack_email_intro_paragraph            # 80-120 word forwardable intro
---

# Monthly Client Report Template

## Purpose

This template produces the monthly client report — the artifact that justifies the retainer and drives renewal. It is the single most important touchpoint between the client and the agency that does not happen on a call. The client reads it in under 10 minutes and walks away with: (1) what shipped, (2) what worked, (3) what we're doing next month, (4) what they need to decide.

The report is signal-dense, attribution-clean, and decision-ready. Length: 800-1,500 words for the main report, 1-page summary appendix, and an 80-120 word forwardable intro. A monthly report that doesn't trigger a "good — keep going" reaction is a renewal-at-risk signal. The client should finish the report wanting to forward it to their team, not file it under "too long."

## When operator/AM uses it

- **Primary trigger:** cron — 1st of each month at 10:00 per active client (per `paperclip.manifest.yaml`).
- **Secondary trigger:** mid-month "executive update" requests from the client (rare — most don't ask, but the template runs at any cadence).
- **Renewal-prep trigger:** operator pulls last 3 months of reports before any renewal conversation. The reports ARE the renewal pitch — no separate sales artifact.
- **Issue-spike trigger:** if a major issue surfaces between cycles (voice drift hard-fail, scope dispute, attribution discrepancy), the AM may issue an interim report focused on that issue + actions taken.
- **Do NOT use** without `/build-attribution-trace` output for the period — attribution is load-bearing for the outcome metrics section.
- **Do NOT use** without `/voice-drift-detector` weekly audits for the period — drift trend is load-bearing for credibility.

## The template

```markdown
# Monthly Report — {client_legal_entity}

**Client slug:** `{client_slug}`
**Reporting period:** {reporting_period_start} → {reporting_period_end}
**Prior period (for comparison):** {prior_period_start} → {prior_period_end}
**Offer tier:** {offer_tier} — {offer_name}
**Account manager:** `{account_manager_slug}`
**Report delivered:** {report_delivered_date}

---

## Executive summary

{executive_summary_paragraph}

---

## 1. What shipped this period

{deliverable_inventory_table}

**Variance notes:**

{deliverable_variance_notes}

Per the offer commitment, every deliverable is counted against what was promised. Variance ≥ +/-15% gets a paired note explaining the why. No silent variance.

---

## 2. What it produced — outcome metrics

{outcome_metrics_table}

**Healthy-band reading:**

{outcome_metrics_band_notes}

Each metric is read against the prior period AND against the workspace's healthy band for this offer tier (cross-reference `workflows/divisions/acquisition.md` health metrics table). A metric improving but still below band gets the same flag as a metric declining within band — context, not just direction.

---

## 3. Top 3 posts this period

### Post 1
{top_post_1}

### Post 2
{top_post_2}

### Post 3
{top_post_3}

The "why this worked" hypothesis on each is grounded in `/score-hook` axes (stake, pattern-break, specificity, payoff, voice-fit) — not vibes. The point of naming the mechanism is so we can repeat it next month.

---

## 4. Top DM sequences this period

### Sequence 1
{top_dm_sequence_1}

### Sequence 2
{top_dm_sequence_2}

DM sequences are read on the reply-rate-to-qualified-rate ratio (a high reply rate with a low qualified-rate means we're talking to the wrong people; a low reply rate with a high qualified-rate means our M1 is too narrow). The ratio is what matters, not either number alone.

---

## 5. Issues + actions taken

{issues_actions_list}

Every issue this period appears here, paired with what we did. The intent is that you do NOT discover an issue from another channel — anything that surfaced this month surfaces here, with the AM's response in the same paragraph.

---

## 6. Plan for next month

{next_month_plan_bullets}

Each bullet is specific (not "ship more posts"), tied to this period's evidence (citing the metric or pattern that drove the decision), and measurable (you'll see it land or miss in next month's report).

---

## 7. Decisions for you

{decisions_for_client_list}

These are concrete asks — actual decisions you need to make this month, not "thoughts?" Each has the context, the recommendation, and the specific yes/no/modify that unblocks us.

---

## 8. Voice + approval health

**Voice-drift trend (weekly):** {voice_drift_trend}
**Approval rate this period:** {approval_rate_period} (% of posts approved at first review)
**Attribution summary:** {attribution_summary}

Voice and approval rate are leading indicators of churn. If either declines for 2 consecutive months, the AM raises it to fulfillment-head per the escalation path in your onboarding doc — even if outcome metrics look fine.

---

## Appendix A — 1-page summary (forwardable)

{one_page_summary_appendix}

---

## Appendix B — Slack/email-ready intro

{slack_email_intro_paragraph}
```

## Fill-in guidance

### `client_slug`, `client_legal_entity`
Slug = the internal kebab-case alias from `ENCODING.md`; legal entity = the actual company name as it appears on the contract. Both appear in the header so the client sees their own name AND so the AM can cross-reference internal subprofiles by slug.

### `reporting_period_start`, `reporting_period_end`, `prior_period_start`, `prior_period_end`
Default reporting period is the previous calendar month. Prior period is the month before that. For biweekly or quarterly cadences, adjust accordingly. Period dates MUST match the attribution-data window — if attribution data covers Jan 1-29 because of a tracking gap, the period dates reflect Jan 1-29, not Jan 1-31. Honesty over neatness.

### `offer_tier`, `offer_name`, `account_manager_slug`
Pull from `clients.{slug}.offer_tier`, `clients.{slug}.offer_name`, `clients.{slug}.account_manager`. The slug is internal-only per A-11; the rendered version delivered to the client uses the AM's actual name.

### `report_delivered_date`
The date the report lands in the client's channel of record. Documented because renewal conversations sometimes pull on "when did we tell them about X" — having delivery dates on every report makes that auditable.

### `executive_summary_paragraph`
1 paragraph (4-7 sentences, ~100-150 words), scannable in 30 seconds. Structure:
- Sentence 1: period overview ("This month shipped per commitment with one notable over-delivery on Authority pillar.")
- Sentences 2-3: headline outcome ("Inbound DMs from content rose 38% vs prior period, driven by two top-decile posts on the Pier pillar.")
- Sentences 4-5: notable issue + action ("Voice-drift soft-failed on week 2; recalibration cleared by week 3 audit.")
- Sentences 6-7: forward look ("Plan for next month doubles down on the Pier pattern. One decision needed from you on DM volume increase.")

**Common mistake:** opening with throat-clearing ("This month was a strong one for our partnership..."). Cut to signal — the executive summary is the only section a busy client may read. Make it carry the report.

### `deliverable_inventory_table`
Pull from `/build-client-report` Step 2. Format as a markdown table with columns: Deliverable | Committed | Actual | Variance %. Every committed deliverable from the offer scope appears, even if actual is 0. Hidden deliverables = renewal-at-risk surprises.

### `deliverable_variance_notes`
For every variance ≥ +/-15%, a 1-2 sentence note explaining the why. Example: "Posts shipped 22 vs 20 committed (+10%) — over-delivered Authority posts to backfill week-2 ICP-mismatch from prior period." If no variances ≥ 15%, write "All deliverables within +/-15% of commitment." Do NOT skip the section — silence reads as oversight.

### `outcome_metrics_table`
Pull from `/build-client-report` Step 3. Markdown table with columns: Metric | This period | Prior period | Δ | Trend (improving/stable/declining). Every metric in `clients.{slug}.success_metrics` appears — never invent new metrics mid-engagement per N-3.

**Common mistake:** showing metrics without prior-period comparison. Numbers without context are signal-poor — a "120 calls booked" reads totally differently if prior was 80 vs 200. Always include prior period.

### `outcome_metrics_band_notes`
1-2 sentences per metric that is meaningfully outside its healthy band (high or low). Example: "Inbound DMs/month at 47 (band: 30-60) — solidly in band. Profile clicks at 1,200 (band: 1,500-2,500) — below band; driven by the Pier-pillar over-rotation reducing Authority-pillar volume. Plan section addresses." Cross-reference `workflows/divisions/acquisition.md` for the health bands per offer tier.

### `top_post_1`, `top_post_2`, `top_post_3`
Per top performer:
- Post date + pillar + hook formula
- Metrics (impressions, engagement rate, profile clicks, inbound DMs from this post)
- "Why this worked" — hypothesis grounded in `/score-hook` axes (e.g., "stake-axis 18/20 because the post named a specific $-amount-at-risk; pattern-break 17/20 because the hook contradicted the dominant ICP belief that 'more LinkedIn posting = more leads'")

**Common mistake:** "this post performed well" with no mechanism. The mechanism is the point — it's what makes the next month's pillar shifts evidence-grounded instead of vibes-grounded.

### `top_dm_sequence_1`, `top_dm_sequence_2`
Per sequence:
- Intent class (Cold / Warm / Liked-post / Comment-magnet) per the DM Intent Taxonomy
- Sequence variant ID (so the AM can reference the exact M1/M2/M3 wording)
- Reply rate
- Qualified-reply rate (per `/classify-dm-intent`)
- Calls-booked attributed
- "Why this worked" — hypothesis grounded in the intent-class match, M1 personalization quality, or M2 calibration

If only one sequence shipped this period, ship one. Two preferred but not required.

### `issues_actions_list`
Bulleted list. Each entry: **bold issue + paired action.** Example:
- **Week 2 voice-drift soft-fail (similarity 0.76, threshold 0.80):** AM recalibrated ghostwriter against last 10 reference posts; week-3 audit returned to 0.83. Root cause: template-default leak on Authority pillar. Prevention: added 5 client-specific phrases to phrases_to_avoid list.
- **Week 3 approval-channel silence (4 days no response):** AM nudged via secondary channel; client confirmed travel + caught up over weekend. Approval rate held at 92% for the period.

If no issues this period, write "No material issues this period. Voice-drift held above threshold all four weeks; approval rate at {value}; no scope tension surfaced." Do NOT skip the section.

**Common mistake:** burying issues, hoping the client doesn't notice. Per N-9 + N-3, every issue surfaces. Surfacing issues with paired actions is the trust-building move that makes renewal feel obvious.

### `next_month_plan_bullets`
3-5 bullets. Each bullet is specific, evidence-tied, measurable. Example:
1. **Doubling down on Pier pillar:** Top 2 posts this month were both Pier (+38% inbound DMs). Pillar mix shifts +5% to Pier next month (now 25% vs 20%); Authority drops -5% (now 25% vs 30%).
2. **Testing comment-magnet ratio at 30% (vs current 20%):** Based on this period's L-class M2 conversion lift (12% → 18%), expanding the test to half the DM volume next month. Judged by: qualified-reply rate at 30 days.
3. **Retiring the Tuesday-morning posting slot:** Posted 4x in this slot, all bottom-decile. Pattern: client's ICP active LinkedIn time is Tues-Thurs 8-10am ET (per the algorithm-fit data); Tuesday morning competes with peak commute scroll.
4. **Sourcing focus — operator-debt-confessional pattern:** Top post was an operator-debt confessional on the Pier pillar; need 3 more first-person operator-debt stories next month. AM is asking client for: any time you almost shut down the agency, any pricing bet that nearly broke you.
5. **DM pivot — adding lead-magnet sequence to comment-magnet contacts:** Comment-magnet contacts are the warmest in the pipeline but the conversion sequence is weakest. Building dedicated 4-step sequence next month.

### `decisions_for_client_list`
1-3 concrete asks. Not "thoughts?" — actual decisions:
1. **Approve next month's pillar shift?** (+5% Pier, -5% Authority — see Plan section #1)
2. **Increase DM volume from 25/day to 40/day?** Capacity available; ICP fit at current quality bar (88% per source-lead-list audit). Cost: same. Upside: ~15-20 more qualified replies/month at current conversion. Yes / No / Increase to a different number?
3. **Cohort recruit conversation:** You mentioned wanting to recruit a cohort by Q3. Want to schedule a 30-min strategy call this month to map the launch sequence?

**Common mistake:** soft-asking ("let us know if any of this resonates"). Soft asks erode authority. Reframe to specific decisions.

### `voice_drift_trend`
Weekly drift values for the period. Format: "Week 1: 0.84 / Week 2: 0.76 (soft-fail, recalibrated) / Week 3: 0.83 / Week 4: 0.85." Single-line summary. The trend matters more than any single value.

### `approval_rate_period`
% of posts approved at first review (no revision rounds requested). Healthy band: 75-90%. Below 75% means brief is misaligned; above 90% means the client may not be reading carefully (also a flag).

### `attribution_summary`
2-3 sentences from `/build-attribution-trace` output. Example: "Channel attribution this period: 62% LinkedIn-content (up from 48% prior), 28% LinkedIn-DM (down from 38% prior), 10% other-source. Confidence: HIGH (CRM tags clean, calendar UTM consistent, no untagged calls in period)."

### `one_page_summary_appendix`
The forwardable 1-pager: executive summary + outcome metrics table + the 3 plan bullets + the decisions section. ~400-600 words. Designed for the client's exec team or board to read without context. Format-clean, citation-light, decision-ready.

### `slack_email_intro_paragraph`
80-120 word forwardable summary the client can paste into a Slack DM or email. Voice: their voice (since they'll be paraphrasing it, not quoting it). Structure: headline outcome + 1 supporting metric + the next-month plan one-liner + a closing reference to the full report.

Example: "Quick update on LinkedIn ops for [period]: Inbound DMs from content rose 38% (47 this month vs 34 last). Two Pier-pillar posts drove most of the lift — both confessional founder-stories about pricing. Plan for next month doubles down on the Pier pattern; one decision pending from me on DM volume. Full report attached / linked — 10 min read."

## Quality bar

The monthly report passes the Blind Output Test per `spec/BLIND-OUTPUT-TEST.md` before delivery, against this artifact-specific criterion:

**Pass criterion:** Show the report to 2 of 3 simulated reviewers in the client signatory role (B2B founder OR CMO matching the client's `client_icps[{slug}]` segment). Ask: "If you read this report on the 1st of the month, would you (a) finish it in under 10 minutes, (b) be able to forward the 1-page summary to a partner without re-explaining anything, (c) walk away with a clear sense of what you need to decide this month, (d) feel the spend was justified by what's in the report?" Pass = 2 of 3 yes on all four. Fail = identify the gap (usually padded executive summary, missing prior-period comparison, top-performer hypotheses ungrounded, or decisions section soft-asking) and iterate.

Specific checks:
1. Period dates correct AND matched to attribution-data window (no fudging)
2. Every committed deliverable accounted for (no silent variance per Step 2)
3. Every success metric has prior-period comparison + healthy-band reading per Step 3
4. Top performers each have "why this worked" hypothesis grounded in `/score-hook` axes per N-3
5. Every issue paired with action taken (no unaddressed issues per N-9 spirit)
6. Next-month plan items are specific, evidence-tied, measurable per Step 6
7. Decisions section asks for actual decisions (not opinions) per Step 7
8. Voice-drift trend included for the period
9. Approval rate included
10. Attribution summary included with confidence reading
11. Report length 800-1,500 words main + 1-page summary appendix + 80-120 word intro
12. No banned vocabulary per `spec/BANNED-VOCABULARY.md`
13. No real names per A-11 (slugs in template; rendered version uses actual AM/client names as appropriate)
14. No invented metrics per N-3 (every metric traces to attribution data or success-metrics list)

Fail any check = halt, do not deliver, route to AM for revision.

## Cross-references

- `skills/build-client-report/SKILL.md` — the skill that emits this template populated with period data
- `skills/onboard-client/SKILL.md` — Step 8 defines the success metrics this report measures against
- `skills/voice-drift-detector/SKILL.md` — feeds the voice-drift trend in section 8
- `skills/post-approval-tracker/SKILL.md` — feeds the approval-rate value in section 8
- `skills/build-attribution-trace/SKILL.md` — feeds the attribution summary in section 8
- `skills/score-hook/SKILL.md` — provides the axis scoring for top-performer hypotheses in section 3
- `skills/classify-dm-intent/SKILL.md` — provides the qualified-reply rates in section 4
- `skills/run-client-dm-ops/SKILL.md` — provides the raw DM operational data
- `skills/diagnose-bottleneck/SKILL.md` — triggered if outcome metrics show period underperformance
- `reference/templates/onboarding-doc.md` — the doc that codified the success metrics this report tracks
- `reference/templates/retainer-proposal.md` — the doc this report's renewal pitch eventually references back to
- `reference/frameworks/fulfillment/account-mgmt-protocol.md` — the AM operating model that drives report cadence
- `INVARIANTS.md` N-3 (no invented metrics), N-9 (every issue surfaces), A-9 (every action leaves a receipt), A-11 (anonymized descriptors only)
- `workflows/divisions/acquisition.md` — health metric ranges for the band-reading in section 2
- `paperclip.manifest.yaml` — the 1st-of-month cron that triggers this report
- `spec/BLIND-OUTPUT-TEST.md` — the verification protocol for monthly-report Quality bar

---

*Template version 1.0.0 — 2026-05-03*
