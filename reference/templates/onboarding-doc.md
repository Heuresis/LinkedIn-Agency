---
template_name: onboarding-doc
used_by_skill: /onboard-client (Step 7 — expectation-setting doc, written in parallel with the kickoff call)
output_format: client-facing-onboarding-doc
audience: signed client at kickoff — the doc they sign to acknowledge what they're signing up for operationally
fillable_slots:
  - client_slug                            # kebab-case anonymized alias per ENCODING.md
  - client_legal_entity                    # the company name as it appears on the contract
  - client_signatory_role                  # role of the person signing (founder, CMO, marketing director)
  - offer_tier                             # retainer | retainer+ddm | retainer+content+ddm | full-stack
  - offer_name                             # public-facing offer name
  - onboarding_start_date                  # YYYY-MM-DD — day 0
  - onboarding_end_date                    # YYYY-MM-DD — day 30
  - account_manager_slug                   # AM assigned (anonymized slug)
  - operator_slug                          # operator-of-record (anonymized slug)
  - channel_of_record                      # slack | notion | email | combo
  - channel_workspace_url                  # specific channel URL or ID
  - posts_per_month                        # from offer scope
  - dms_per_month                          # from offer scope (or N/A if content-only)
  - leads_per_refresh                      # from offer scope
  - reports_per_month                      # typically 1
  - calls_per_month                        # typically 1 strategy call
  - approval_sla_hours                     # client review window before nudge (default 24)
  - revision_rounds_max                    # typically 2 per post
  - voice_drift_threshold                  # 0.75-0.85, calibrated per client
  - voice_drift_audit_cadence              # weekly default
  - re_extraction_trigger                  # what triggers a re-extraction interview
  - weekly_summary_day                     # day of week the summary lands
  - monthly_report_day                     # day of month the report lands
  - monthly_call_day                       # day of month the strategy call holds
  - escalation_am_contact                  # AM channel + response window
  - escalation_fulfillment_head            # fulfillment-head escalation slug + window
  - escalation_operator                    # operator escalation slug + window
  - exit_clause_reference                  # contract section number
  - milestone_30_review_items              # bulleted list of what gets reviewed at day 30
  - signature_client_name_line             # for client to sign
  - signature_operator_name_line           # for operator to sign
  - signature_date_line                    # date of signature
---

# Onboarding Doc Template

## Purpose

This template produces the single-page expectation-setting document the client signs at kickoff. It is the operational contract that sits underneath the legal contract — codifying what ships when, how approvals work, what happens when voice drifts, the comm cadence, and the escalation path. It is the document the AM points to in month 3 when scope-creep questions surface.

The doc is intentionally short (1 page when printed, 800-1,200 words). A long onboarding doc that the client doesn't read is worse than no doc — it provides false legal cover without operational alignment. Scannable, signable, mutually understood in 10 minutes.

## When operator/AM uses it

- **Primary trigger:** `/onboard-client` Step 7 — written in parallel with the Step 2 kickoff call, presented for signature in the kickoff call OR within 5 days of kickoff (whichever first).
- **Re-issue trigger:** existing client renews into a re-scoped offer (treat as new onboarding per `/onboard-client` re-onboarding clause).
- **Reference trigger:** any time scope-creep, comm-cadence drift, or approval-workflow questions surface in month 2+. The doc is the canonical reference — disputes resolved by what was signed here, not by what either side remembers from the kickoff.
- **Do NOT use** before the legal contract is signed. The legal contract is the parent; this doc operationalizes it. Pre-contract use creates ambiguity about which document supersedes which.

## The template

```markdown
# Client Onboarding — {client_legal_entity}

**Client slug:** `{client_slug}`
**Offer tier:** {offer_tier} — {offer_name}
**Onboarding window:** {onboarding_start_date} → {onboarding_end_date}
**Account manager:** `{account_manager_slug}`
**Operator of record:** `{operator_slug}`

This document codifies how we operate together for the term of the contract. It supplements (does not replace) the signed legal contract. Where this document and the contract conflict, the contract governs. Where the contract is silent on operational detail, this document governs.

---

## 1. What ships when

| Deliverable | Cadence | Volume |
|---|---|---|
| Ghostwritten LinkedIn posts (your voice) | Weekly | {posts_per_month} per month |
| Personalized cold DMs (intent-classified) | Daily Mon-Fri | {dms_per_month} per month |
| Lead-list refresh (ICP-filtered) | Monthly | {leads_per_refresh} contacts per refresh |
| Monthly client report | Monthly | {reports_per_month} on day {monthly_report_day} |
| Monthly strategy call | Monthly | {calls_per_month} on day {monthly_call_day} |
| Voice-drift audit | Weekly | Run by AM, surfaced in weekly summary |
| Weekly operations summary | Weekly | Every {weekly_summary_day} |

Anything not on this list is out of scope. Out-of-scope work is quoted in writing and approved by you before any work starts. The most common out-of-scope requests we receive: video editing, paid-ad management, email-list management, internal-team training, custom graphic design beyond LinkedIn-native formats. None of these are included; all are quotable separately.

---

## 2. Approval workflow

**Channel of record:** {channel_of_record} ({channel_workspace_url})

This is the single channel for posts, DM drafts, and operational decisions. Side-channel approvals (text, DMs to the operator's personal account, drive-by mentions in the strategy call) do NOT count. If it's not in {channel_of_record}, it didn't happen.

**Response SLA — your side:** {approval_sla_hours} hours from when we post a draft. Beyond {approval_sla_hours} hours, the AM nudges via secondary channel; beyond 48 hours, the post moves to the next slot in the calendar (we don't burn a posting day waiting on approval — the calendar matters more than any single post).

**Response SLA — our side:** AM responds to your direct asks (questions, drafts requested, scope clarifications) within 24 hours during business days, 48 hours weekends.

**Revision rounds:** Maximum {revision_rounds_max} revision rounds per post. Beyond {revision_rounds_max}, the post is killed and we move to the next concept — repeated revisions on a single post are usually a brief problem (the post should never have been written), not an execution problem. The AM surfaces "we're hitting the revision ceiling on Authority pillar this week — let's recalibrate the brief in the next strategy call."

---

## 3. Voice-drift policy

We run a voice-drift audit weekly per `INVARIANTS.md` A-12. The audit compares the last 20 ghostwritten posts against your validated voice profile (built during voice-extraction in week 1).

**Your voice-drift threshold:** {voice_drift_threshold} (cosine similarity)

**What triggers what:**
- Drift ≥ {voice_drift_threshold}: green. Continue.
- Drift soft-fail (within 0.05 of threshold): yellow. AM recalibrates ghostwriter against last 10 reference posts; re-audit next week.
- Drift hard-fail (>0.05 below threshold): red. Halt ghostwriting. {re_extraction_trigger}.

You will see drift events in your weekly summary, paired with the corrective action we took. The intent is that you never discover voice drift — we surface it before the next post ships.

---

## 4. Communication cadence

| Touch | Cadence | Format | Owner |
|---|---|---|---|
| Weekly operations summary | Every {weekly_summary_day} | {channel_of_record} message + 1-page doc | AM |
| Monthly client report | Day {monthly_report_day} of month | Doc delivered + Slack/email-ready summary | AM + reporter |
| Monthly strategy call | Day {monthly_call_day} of month | 45 min video call | AM + (operator if quarterly) |
| Quarterly scope review | Every 90 days | 30 min video call | AM + operator |

Outside these touches, ad-hoc questions go to {channel_of_record}. The AM is your primary point of contact. The operator joins quarterly scope reviews and any session escalated per section 5.

---

## 5. Escalation path

If something is not working, escalate in this order — do not skip levels:

1. **First — your AM:** {escalation_am_contact}
2. **Second — fulfillment head:** {escalation_fulfillment_head} (if AM has not resolved within 5 business days OR the issue is structural — repeated voice drift, repeated SLA misses, repeated scope tension)
3. **Third — operator:** {escalation_operator} (if fulfillment head has not resolved within 5 business days OR the issue is contractual — pricing, scope renegotiation, performance against guarantee)
4. **Exit clause:** per contract section {exit_clause_reference}. We will not invoke the exit clause without first running steps 1-3. We expect the same in reverse — no exit invocation from your side without an attempt at AM and fulfillment-head resolution.

The intent of this path is that 90% of issues resolve at level 1, the remainder at level 2, and exit-clause discussions are rare and structural. We track escalation frequency as an internal metric — repeated level-2 escalations on the same issue trigger a structural review, not just another fix.

---

## 6. Day-30 milestone check-in

On day {onboarding_end_date}, the AM and operator-of-record will run a 45-minute milestone review with you. We'll review together:

{milestone_30_review_items}

The intent of this milestone is twofold: (a) confirm the engagement is on track and you have the visibility to judge that, (b) catch any drift between what you signed up for and what you're experiencing before month 2 starts. If any item is off, we agree remediation in writing before month 2 fulfillment continues.

---

## 7. Acknowledgment

By signing below, you acknowledge:

- You have read sections 1-6 above and understand the operational contract.
- You agree to the channel-of-record discipline in section 2 (no side-channel approvals count).
- You agree to the voice-drift policy in section 3 and the threshold of {voice_drift_threshold}.
- You agree to the escalation path in section 5 (steps 1-3 before exit-clause discussion).

| Party | Signature | Date |
|---|---|---|
| {signature_client_name_line} | _____________________ | {signature_date_line} |
| {signature_operator_name_line} | _____________________ | {signature_date_line} |

*This document supplements the legal contract signed {onboarding_start_date}. Where this document and the contract conflict, the contract governs.*
```

## Fill-in guidance

### `client_slug`, `client_legal_entity`, `client_signatory_role`
The slug is the internal kebab-case alias per `ENCODING.md` (e.g., `acme-saas-q2-2026`). The legal entity is the actual company name as it appears on the contract — used here because this doc is a legal-adjacent acknowledgment. The signatory role is the role of the person signing (most often founder or CMO; occasionally marketing director if the founder delegated).

**Common mistake:** putting the slug in the document title where the legal entity should be. The slug is internal; the entity name is client-facing.

### `offer_tier`, `offer_name`
Pull from `product_strategy.service_tier.offers[{slug}]`. Tier is the standardized tier label (retainer, retainer+ddm, etc.); name is the public-facing offer name.

### `onboarding_start_date`, `onboarding_end_date`
Day 0 = the date the contract signs (or the date the kickoff call holds, whichever is later by client convention). Day 30 = start + 30 calendar days. NOT business days — calendar days. The client expects the day-30 milestone to land on a calendar date, not a fuzzy "about a month from now."

### `account_manager_slug`, `operator_slug`
Anonymized internal slugs. Per A-11, no real names appear in pushed templates. The actual AM and operator names appear in the rendered version delivered to the client; the template here uses slug placeholders.

### `channel_of_record`, `channel_workspace_url`
Single channel — not "all three." The single-channel discipline is the most-violated and most-load-bearing operational rule. Default ranking by client preference: Slack > Notion > email. Email is workable but slowest; Notion works for clients who already live in Notion; Slack is highest-throughput. The channel_workspace_url is the specific workspace/channel URL or ID.

**Common mistake:** "Slack OR email, whichever is easier." This is not a channel of record — it's an excuse. Pick one.

### `posts_per_month`, `dms_per_month`, `leads_per_refresh`, `reports_per_month`, `calls_per_month`
Pull from `product_strategy.service_tier.offers[{slug}].deliverables_per_month`. Use the offer's committed volumes. Do NOT round up to make the table look fuller. If the offer commits 18 posts/month, write 18, not 20.

### `approval_sla_hours`, `revision_rounds_max`
Default approval SLA is 24 hours; the client may negotiate to 48 (B2B founders with travel-heavy schedules). Default revision rounds is 2; some clients negotiate 3 (rare — usually flag for AM that this client will burn time in revisions). Both values appear in the offer doc; consistency between offer doc and onboarding doc is mandatory.

### `voice_drift_threshold`, `voice_drift_audit_cadence`, `re_extraction_trigger`
Threshold is calibrated during the voice-extraction interview (`/extract-founder-voice` Step 5). Default 0.80; tightened to 0.85 for clients with strongly idiosyncratic voice; loosened to 0.75 for clients whose voice is more flexible. Cadence is weekly by default per A-12 — never less.

The re-extraction trigger is the explicit condition: "Halt ghostwriting. AM books a 30-minute voice recalibration interview within 5 business days; we resume only after the recalibration audit clears." Be specific — vague language ("we'll address it") fails the doc's purpose.

### `weekly_summary_day`, `monthly_report_day`, `monthly_call_day`
Default: weekly summary every Monday, monthly report on the 1st, monthly call on the first Friday. Adjusted to client preference (some prefer end-of-week summaries; some prefer mid-month reports). Whatever the cadence, it's specific — "every Monday by noon ET," not "early in the week."

### `escalation_am_contact`, `escalation_fulfillment_head`, `escalation_operator`
Each entry has the channel + response window. Example: "AM via {channel_of_record}, 24-hour response Mon-Fri." NOT a phone number unless explicitly approved by the operator (per channel-of-record discipline, phones are not channels of record).

### `exit_clause_reference`
The specific section number from the legal contract that contains the exit clause. Cross-referenced so the client can navigate from this doc to the contract section without ambiguity. If the contract has no explicit exit clause, this is a contract problem, not an onboarding-doc problem — fix upstream.

### `milestone_30_review_items`
Bulleted list of what the day-30 review covers. Pull from `/onboard-client` Step 9 verification checklist. Example:
- All 6 client subprofiles completeness ≥70% (voice, ICP, content, case studies)
- ≥5 ghostwritten posts shipped, voice-drift ≥ threshold on all
- DM ops running daily (if in scope) with ≥1 qualified reply
- ≥80% of posts in queue reviewed and approved by client
- Success metrics defined and ready for monthly-report cadence

**Common mistake:** generic "we'll check in on progress." Specific items make the milestone real and falsifiable. Generic items make it a vibe check.

### `signature_client_name_line`, `signature_operator_name_line`, `signature_date_line`
The line ABOVE the signature, naming who is signing. Example: "Signed: [Client signatory name], [signatory role], [client_legal_entity]." The signature itself is captured in whatever signature flow the operator uses (DocuSign, HelloSign, e-sign in the client portal). If signed in person, ink + date.

## Quality bar

The onboarding doc passes a Blind Output Test before being presented for signature, against this artifact-specific criterion:

**Pass criterion:** Show the doc to 2 of 3 simulated reviewers in the client signatory role (B2B founder OR CMO matching `agency_icp` segment). Ask: "If you signed this at kickoff and a question came up in month 3 about scope, voice quality, or who to talk to when something breaks — could you find the answer in this doc in under 60 seconds?" Pass = 2 of 3 yes. Fail = identify the gap (usually buried escalation path, vague drift policy, or missing day-30 milestone) and iterate.

Specific checks:
1. Document fits on one printed page (1,000-1,500 words target)
2. Channel of record is named specifically (one channel, with URL/ID)
3. Approval SLA and revision-rounds-max are concrete numbers
4. Voice-drift threshold is a specific value (not "high" or "tight")
5. Re-extraction trigger is explicit (not "we'll address it")
6. Communication cadence has specific days/cadence (not "regularly")
7. Escalation path lists 3 levels with named owners + response windows
8. Day-30 milestone has 4-6 specific check items (not "we'll review progress")
9. Signature block has client name + operator name + date lines (not blank "X" markers)
10. Document explicitly states what supersedes what (legal contract vs this doc)
11. No banned vocabulary per `spec/BANNED-VOCABULARY.md`
12. No real client/operator names in template form (slugs only) per A-11

Fail any check = halt, do not present for signature, route to AM for revision.

## Cross-references

- `skills/onboard-client/SKILL.md` — Step 7 invokes this template; the full onboarding sequence drives the field values
- `skills/design-retainer-offer/SKILL.md` — produces the offer scope that drives section 1's deliverables
- `skills/extract-founder-voice/SKILL.md` — produces the voice profile and threshold for section 3
- `skills/voice-drift-detector/SKILL.md` — runs the weekly audit referenced in section 3
- `skills/post-approval-tracker/SKILL.md` — runs the approval SLA tracking referenced in section 2
- `skills/build-client-report/SKILL.md` — produces the monthly report referenced in section 4
- `reference/templates/retainer-proposal.md` — the doc that converted the prospect into a client (precedes this doc)
- `reference/templates/monthly-client-report.md` — the artifact this onboarding sets up to be defensible
- `reference/frameworks/fulfillment/account-mgmt-protocol.md` — the AM operating model that drives sections 4-5
- `INVARIANTS.md` A-6 (operator/AM review for client-bound), A-12 (weekly voice-drift audit), A-11 (anonymized descriptors only)
- `ENCODING.md` — recursive subprofile schema referenced by client_slug
- `spec/BLIND-OUTPUT-TEST.md` — the verification protocol for onboarding-doc Quality bar

---

*Template version 1.0.0 — 2026-05-03*
