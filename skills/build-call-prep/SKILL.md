---
name: build-call-prep
slug: /build-call-prep
agent: call-prep-specialist
department: acquisition
output_format: call-prep-deck
required_profiles:
  ideal_customer_profile: 70              # recursive: agency-icp (target scope) or client-icp
  product_strategy: 70                    # recursive: target offer (service-tier or program-tier)
  case_studies.agency_case_studies: 50    # ≥1 vertical+stage match preferred
allowed_scopes:
  - agency-call                           # operator running discovery for agency's own retainer/program (default)
  - client-call                           # rare — agency runs discovery for a client's account
output_path: outputs/{date}/call-prep/{prospect-slug}.md
---

# /build-call-prep — Discovery Call Prep Deck

> **Purpose.** Produce a 1-2 page deep-research prep deck the operator reads in <10 minutes before a discovery call. The deck is the difference between a vibe call and a call that closes a $5K-$25K/month retainer. Outputs: prospect profile (verified facts only), trigger-event hypothesis (why this prospect is in market NOW), proof-anchor selection (the case study that lands), objection pre-frame (the 3 likely objections + pre-framed responses), discovery question sequence (5-8 questions that make the prospect articulate their own urgency), transition-to-pitch script, decision-tree by call outcome.
>
> **Bar.** A single discovery call held with this deck must produce one of two outcomes: a clean close on the call, or a clean disqualification with the reason logged for ICP refinement. No mushy middles. The operator must walk into the call with one named hypothesis about who this prospect is, what they need, and which case study to lead with — and walk out either with a signed client or a sharper ICP. A discovery call mishandled = $60K-$300K LTV walks out the door. The deck quality bar is calibrated to that math.
>
> **The 1-of-1 primitive.** Most agencies prep discovery calls with a CRM dump and a vibe. This deck is *encoded research* — the prospect's vertical, the prospect's trigger event, the prospect's likely objections, and the operator's pre-framed responses are all loaded before the operator opens Zoom. The call itself becomes execution, not discovery of basic facts.

## When to invoke

- Triggered automatically 24h before any `Booked` state call (per `workflows/divisions/acquisition.md` — `Booked → CallPrepBuilt`)
- Manual invoke when operator wants prep ahead of an unscheduled high-stakes inbound call
- Re-run when call gets rescheduled >48h out (intake data ages — refresh trigger event + case-study selection)
- Re-run when application data updates between booking and call (prospect adds revenue / team / stage)
- Never invoke without an `ApplicationSubmitted` upstream — per `INVARIANTS.md` N-12, application is required before booking, and the application IS the intake feed for this skill

## Required inputs

1. **Application form data** (from `/build-application` Cycle-2 output) — the qualification thread answers, ranked DQ status, declared revenue, declared team, declared trigger
2. **DM thread history** (full Replied + Qualifying state messages from `dm-strategist`) — verbatim language the prospect already used
3. **LinkedIn profile snapshot** — headline, current role, tenure, company stage, recent posts (last 30 days), who they engage with
4. **Company website + 1-pager** — positioning, customer logos visible on site, pricing if visible, hiring page (signal)
5. **Funding / news signals** — last 90 days of press, funding rounds, leadership changes, product launches, layoffs (each is a trigger event candidate)
6. **Role-context** — for each role archetype (founder / CMO / Head of Demand Gen / VP Sales), the workspace loads the role-specific decision criteria + power-map per `reference/operators/external/`
7. **Case-study library** — `case_studies.agency_case_studies[*]` filtered by vertical match + stage match + transformation match
8. **Objection library** — the agency's encoded objection-response pairs (lives in `reference/frameworks/sales/objection-library.md`)
9. **Operator's calendar** — to time-box prep delivery (deck must arrive ≥6h before call so operator can read async)

## The build sequence

### Step 1 — Prospect intake assembly (8 min)

Pull every available structured input on the prospect into a single intake bundle. Distinguish **verified facts** from **inferences**.

```yaml
prospect_intake:
  identity: { role_descriptor, company_descriptor, seniority, tenure_in_role_months, prior_role }
  application_data: { declared_revenue_band, declared_team_size, declared_trigger, declared_budget_band, declared_timeline }
  dm_thread_signals: { verbatim_pain_phrases, verbatim_desire_phrases, silent_objections_detected }
  linkedin_signals: { posting_cadence, content_themes, engagement_pattern, follower_count_band }
  company_signals: { stage, visible_customers, hiring_signals, competitive_set }
  news_signals_last_90d: []               # press, funding, leadership, layoffs, launches
  inference_gaps: []                      # what is INFERRED vs verified — flagged for operator awareness
```

All identifiers use methodology descriptors (never real names per A-11). Inference gaps get explicit `[INFER:]` tags on the deck so the operator knows what's load-bearing fact vs. educated guess.

### Step 2 — Trigger-event mapping (8 min)

Discovery calls close when the operator names the *specific event in the last 30-90 days* that pushed the prospect from "this is a someday problem" to "I booked a call this week." Without a named trigger, the call drifts into general-LinkedIn-strategy education and the operator is teaching, not closing.

The trigger taxonomy (one of these is almost always live for a booked call):

| Trigger class | What to look for | Where to find it |
|---|---|---|
| **Hire fail** | In-house writer / SDR / marketer left in last 90 days | LinkedIn (departing employee), hiring page, application "what made you book" |
| **Agency churn** | Last agency relationship ended in last 90 days | DM thread mentions, application "what have you tried", press if material |
| **Funding event** | Raised in last 90 days, runway clock now ticking | Crunchbase, press, LinkedIn announcement |
| **Revenue ceiling** | Plateau pattern visible — flat MRR or flat headcount for 6+ months | Public revenue signals, press silence, LinkedIn cadence drop |
| **Competitor pain** | Competitor is winning visibly, prospect's content dropped off | Competitor LinkedIn growth, prospect content cadence, market conversation |
| **Exec frustration** | New CMO / new VP Sales in last 6 months — they own outcomes now | LinkedIn role change, hiring announcements |
| **Strategic mandate** | Board/founder publicly committed to a number that requires LinkedIn | Public commitments, press, post-funding messaging |
| **Personal-brand shift** | Founder or exec publicly committed to building presence in last 60 days | Their own LinkedIn cadence change, recent posts about it |

Output of Step 2:

```yaml
trigger_event:
  primary_class: ""                       # most likely trigger from taxonomy
  evidence: []                            # 2-4 supporting data points
  confidence: ""                          # HIGH (3+ evidence) | MEDIUM (2) | LOW (1, inferred)
  secondary_class: ""                     # next-most-likely (operator may discover primary is wrong)
  trigger_phrase_to_test: ""              # verbatim phrase to test in first 5 min ("Sounds like the in-house writer leaving was the moment this stopped being someday — fair?")
```

If trigger confidence is LOW, the deck opens the discovery sequence with trigger-elicitation rather than trigger-confirmation.

### Step 3 — Proof-anchor selection (8 min)

Pull from `case_studies.agency_case_studies[*]` and rank by match score. The case study that LEADS in the call must match on three dimensions:

```yaml
case_study_ranking:
  - case_study_slug: ""
    vertical_match: 0-1.0                 # exact = 1.0, adjacent = 0.6, distant = 0.2
    stage_match: 0-1.0                    # same revenue band + team size band
    transformation_match: 0-1.0           # same trigger class + same desired outcome
    composite_score: 0-1.0                # 0.4*vertical + 0.3*stage + 0.3*transformation
    starting_state_summary, ending_state_summary, time_horizon: ""
    transformation_quote: ""              # verbatim from client, cleared per N-3
    use_in_call_as: ""                    # primary | secondary | mention only
```

The deck declares the **lead anchor** (composite score ≥ 0.7 ideal, ≥ 0.5 acceptable) and a **secondary anchor** (different angle — usually a different vertical with the same trigger class, to demonstrate breadth).

If no case study scores ≥ 0.5: the deck flags this, declines to select an anchor, and routes the operator to a *narrative-first* call open ("I want to be honest — we don't yet have a case study in your exact vertical, but here's the mechanism we run and why it transfers"). Honest declination beats forcing a bad-fit anchor.

Per `INVARIANTS.md` N-3: never invent results. If a case study has no `proof_assets` path or `use_in_sales: false`, it is excluded from selection.

### Step 4 — Objection pre-frame (8 min)

For every booked discovery call, three objections are statistically likely. The deck names them, ranks by likelihood given prospect signals, and surfaces the operator's pre-framed response from the encoded objection library.

The objection-likelihood matrix:

| Objection class | High-likelihood prospect signals | Pre-frame approach |
|---|---|---|
| **Price ("too expensive")** | Revenue band <$2M, prior agency at much lower price, no funding signal | Anchor early to in-house cost equivalent + the named transformation — never defend price reactively |
| **Trust ("you'll burn me like the last one")** | Prior agency churn in last 12 months, DM thread mentions "tried before" | Lead with the case study that mirrors their prior-agency-churn scenario + the specific guardrail that prevents the same failure |
| **Timing ("not now / Q3 maybe")** | Vague trigger, no named event, soft DM-thread engagement | Surface the cost of waiting in their own metric language, but DO NOT push — soft timing = not actually qualified, route to nurture |
| **Authority ("need to talk to my partner / board")** | Co-founder structure, board-backed company, declared budget vague | Pre-frame the partner conversation early ("who else needs to be in this decision?") + offer to join a follow-up with the partner |
| **Capacity ("we don't have time to feed you")** | Founder posts dormant for 90+ days, application mentions "time" | Anchor on the agency's content-sourcing protocol that minimizes founder time — specific time-per-week number |
| **Voice ("an agency can't sound like me")** | Founder has strong existing voice, posts have personality, application shows pride in voice | Lead with the voice-extraction protocol + voice-drift-detector — the workspace-encoded mechanism IS the answer |
| **Channel skepticism ("LinkedIn is dead / saturated")** | Prospect's vertical has high-volume DM noise, prospect comments on cynical posts | Lead with the intent-taxonomy mechanism — show why segmented sequences work where blast cold doesn't |
| **Vertical fit ("you don't work with my vertical")** | Operator has limited proof in prospect's exact vertical | Lead with mechanism transferability + the secondary case study that demonstrates same-trigger different-vertical |

Output:

```yaml
objection_preframe:
  - objection_class: ""                   # from matrix above
    likelihood: ""                        # HIGH | MEDIUM | LOW
    triggering_signals: []                # what in the intake suggested this
    preframe_moment: ""                   # open / mid / close
    preframe_script: ""                   # 2-3 sentence operator script
    fallback_if_objection_lands_anyway: "" # second-line response from objection library
```

The deck surfaces the **top 3 by likelihood** — not all 8. Operator cognitive load matters; preparing for 8 leads to preparing for none.

### Step 5 — Discovery question sequence (8 min)

5-8 questions in a specific order, designed to make the prospect articulate their own urgency and decision criteria. Not interrogation — conducting. The principle: **questions that surface the prospect's own language about their own pain are 4-6× more conversion-effective than operator-narrated framing.** The prospect must say it. The operator's job is to ask the question that makes them say it.

| Q# | Purpose | Question template (operator customizes per prospect) |
|---|---|---|
| **Q1** | Trigger confirmation (or elicitation if confidence LOW) | "Walk me through the moment in the last few months where you went from 'maybe LinkedIn at some point' to 'I'm booking this call this week.' What changed?" |
| **Q2** | Pain quantification in their metric | "If we zoomed forward 90 days and nothing changed — what's the actual cost? Is that a revenue number, a hire you can't make, a deal you'd lose?" |
| **Q3** | Prior-attempt diagnosis | "What have you tried before this — in-house, an agency, doing it yourself — and what specifically didn't work?" |
| **Q4** | Decision criteria elicitation | "If you and I were having a conversation 6 months from now and you said 'this was the best call I made all year,' what would have had to happen?" |
| **Q5** | Decision-process surface | "Walk me through how a decision like this gets made on your side. Is this a you-decide thing, or is there a partner / board / co-founder who weighs in?" |
| **Q6** | Risk / fear surface | "What's the part of working with an agency that worries you most given everything you've seen?" |
| **Q7** | Budget reality check (only if not surfaced earlier) | "Ballpark — when you imagined this, were you thinking $X-Y/mo range or were you in a different zip code?" — operator names the actual range |
| **Q8** | Commitment-level test (the last question before transition) | "If we had a clear plan and the math worked, are you in a position to start in the next 30 days, or is this more like a 'see what's out there' moment?" |

The deck pre-fills Q1-Q8 with prospect-specific phrasing (Q1's "in the last few months" becomes the specific trigger window; Q3's options pre-load what the application/DM thread already revealed). Operator skips questions whose answer is already in the intake — a 60-min call has room for ~5 questions answered well. Deck ranks Q1-Q8 by *information-gain expected* and bolds the top 5.

### Step 6 — Transition-to-pitch script (5 min)

The most-leaked moment in a discovery call is the bridge from "we've talked about your situation" to "here's what working together looks like." Most operators fumble it — either launching into pitch with no permission gate (jarring) or over-asking permission (signals low conviction).

Pre-framed transition (operator personalizes 2-3 phrases):

> "Based on what you've described, I want to share what I think the work would look like and what it would cost. Three things to walk through: the specific mechanism we'd run for {prospect's named pain}, what happened for {anchor case-study descriptor}, and the structure — pricing, scope, what's in, what's out. After that you'll know enough to say 'yes let's do this,' 'I need to think,' or 'this isn't it.' All three are fine answers. Sound good?"

The script does four things: names the prospect's pain in their language (continuity), anchors to the Step 3 case study (proof bridge), pre-frames three legitimate outcomes including "this isn't it" (lowers defensiveness, *raises* close rate), and asks permission with a low-friction "sound good?" (not heavy "may I proceed"). Deck pre-fills the `{}` slots from Steps 2 and 3.

### Step 7 — Decision-tree by call outcome (5 min)

The call ends in one of four outcomes. The operator should know the next move per outcome BEFORE the call starts so the close-the-call moment is crisp.

```yaml
post_call_decision_tree:
  yes_now:                # prospect commits live
    operator_action: "Lock start date, contract within 1hr, payment link attached"
    fsm: "CallHeld → Closed → ClientOnboarding"
    next_skill: "/onboard-client"
  yes_pending_stakeholder: # sold but partner/board approval needed
    operator_action: "Schedule joint follow-up within 7d, send 1-page summary to forward"
    fsm: "CallHeld → ProposalSent (stub serves as forwardable)"
    next_skill: "/summarize-discovery-call (proposal-stub mode)"
  soft_objection:         # specific objection unresolved
    operator_action: "Acknowledge, route to objection-handling resource, 5-day re-touch"
    fsm: "CallHeld → CallObjection → ObjectionHandled"
    next_skill: "/summarize-discovery-call (objection-followup mode)"
  hard_fit_issue:         # mutual recognition of mis-fit
    operator_action: "Honest decline, optional resource pointer, clean exit"
    fsm: "CallHeld → NotAFit → archive (ICP refinement signal logged)"
    next_skill: "/summarize-discovery-call (decline mode)"
```

Per `INVARIANTS.md` A-13: the workspace recommends; the operator confirms.

### Step 8 — Output emission

Hydrate the deck using `reference/templates/call-prep-template.md` (or inline structure if template absent). Run the **Blind Pre-Call Audit** before delivery — three simulated reviewers (a senior operator who's run 100+ discovery calls, the prospect themselves, an SDR new to the vertical). Pass = ≥2 of 3 reviewers say YES on "this makes the call easier and won't embarrass the brand." Below pass, iterate on weakest dimension.

Deck delivers to operator with a **read-time estimate** (target ≤10 min). If estimate exceeds 12 min, the deck is too long — operator skims and the prep advantage collapses. Re-tighten.

## Validation gate (mandatory before declaring done)

Three checks:

1. **The trigger-event evidence test.** Trigger event must have ≥2 evidence points. If only 1, classification confidence drops to LOW and the deck explicitly tells the operator to lead with trigger elicitation, not confirmation. Below 1, the deck halts and surfaces "insufficient signal — request operator review of intake before delivery."

2. **The case-study match test.** Lead anchor composite score must be ≥0.5 OR the deck must declare "no anchor — use mechanism narrative" honestly. Inventing a stretch-fit anchor breaks N-3 in spirit (over-claims relevance). If no anchor scores ≥0.5 and the operator wants to proceed anyway, override is logged.

3. **The 10-minute read test.** Estimated read time ≤10 minutes. The deck length must respect that the operator is prepping between calls, not on a quiet morning. If the deck runs >10 minutes, it has not earned the additional length — cut.

## Output format

```yaml
---
artifact_type: call-prep-deck
scope: agency-call | client-call
prospect_slug: ""                         # anonymized — never real name
client_slug: null | "<slug>"              # only for client-call scope
target_offer_slug: ""                     # which offer this call is funneling toward
profile_completeness: 0-100
build_date: YYYY-MM-DD
call_date: YYYY-MM-DD HH:MM TZ
trigger_confidence: HIGH | MEDIUM | LOW
case_study_anchor_score: 0-1.0
estimated_read_time_minutes: 0
blind_pre_call_audit: passed | failed | not-yet-run
profiles_used: [ideal_customer_profile, product_strategy, case_studies.agency_case_studies, brand_voice]
frameworks_used: [trigger-event-taxonomy, case-study-match-score, objection-preframe-matrix, discovery-question-architecture, transition-script, post-call-decision-tree]
confidence: HIGH | MEDIUM | LOW
gap_flags: []
---

## TL;DR (operator reads this if nothing else — 60 seconds)
{3-line summary: who they are, why they booked, which case study you lead with}

## Prospect intake (verified facts)
{the populated prospect_intake YAML from Step 1, with [INFER:] tags on inferences}

## Trigger event hypothesis
{the populated trigger_event YAML from Step 2 + the verbatim test phrase to use in the first 5 min}

## Proof anchor selection
{primary anchor + secondary anchor + brief on how to introduce each}

## Top 3 objections + pre-frames
{the top-3 objection_preframe entries from Step 4 — operator-facing scripts}

## Discovery question sequence
{Q1-Q8 with prospect-specific phrasing, top 5 bolded by information-gain rank}

## Transition-to-pitch script
{the personalized transition from Step 6}

## Post-call decision tree
{the four-branch tree from Step 7 — operator confirms branch on call exit}

## Validation gate result
{trigger-evidence test, case-study-match test, 10-min-read test}

## Operator next actions
{calendar tap-throughs, what skills auto-trigger post-call by branch}
```

## Failure modes and guards

- **Generic deck — feels like a CRM dump.** Reads as "facts about a prospect" rather than "named hypothesis + scripted plan." Guard: trigger event MUST be named (LOW confidence acceptable; missing is not). Case-study match MUST be scored (declined-honestly acceptable; un-attempted is not).
- **Inventing a case study match.** Operator pressure to "find SOME case study" leads to stretching a 0.3-match anchor. Per `INVARIANTS.md` N-3, never invent results. If no anchor scores ≥0.5 (Step 3), declare honest no-anchor and route to mechanism narrative.
- **Over-long deck.** A 4-page deck skimmed in 90 seconds is worse than a 1-page deck read carefully. Guard: 10-minute read-time gate.
- **Missing trigger event.** The most damaging miss. Without a trigger, the call drifts. Guard: if Step 2 produces zero evidence, deck halts and surfaces "needs operator review of intake before delivery — possible mis-booked call."
- **Templated discovery questions.** Q1-Q8 written generically means the operator improvises live and skips the questions. Guard: each question hydrated with prospect-specific phrasing from Steps 1-2.
- **Ignoring application data.** Application answers are the highest-quality intake signal. Guard: application-data section is mandatory. If application is missing, skill MUST refuse (per `INVARIANTS.md` N-12).
- **Voice contamination on client-call scope.** Deck must hydrate from client's ICP/voice, not agency's. Guard: scope check at Step 1 routes to correct profile path.
- **Forgetting the post-call decision tree.** Operator finishes with no pre-loaded next move, fumbles the close. Guard: Step 7 is mandatory — every deck ends with the four branches.

## Cross-references

- `agents/acquisition-head.md` — owner of CallPrepBuilt → CallHeld
- `agents/call-prep-specialist.md` — agent persona for this skill
- `workflows/divisions/acquisition.md` — FSM `Booked → CallPrepBuilt → CallHeld`
- `INVARIANTS.md` A-13, N-12, N-3, N-8, A-11
- `spec/CONTEXT-THRESHOLDS.md` — `/build-call-prep` threshold row
- `skills/build-application/SKILL.md` (Cycle 2 — TBD) — application-data input
- `skills/build-outbound-engine/SKILL.md` — DM-thread input
- `skills/design-retainer-offer/SKILL.md` — target_offer input
- `skills/build-icp/SKILL.md` — ICP input
- `skills/summarize-discovery-call/SKILL.md` — post-call from the four branches
- `skills/no-show-recovery/SKILL.md` — runs if the prospect doesn't show
- `reference/frameworks/sales/trigger-event-taxonomy.md`
- `reference/frameworks/sales/objection-library.md`
- `reference/frameworks/sales/discovery-question-architecture.md`
- `reference/templates/call-prep-template.md`
- `reference/swipe-file/call-recordings-analysis/` — annotated past-call breakdowns
- `reference/swipe-file/application-questions/` — annotated application answers

## Examples

See `examples/`:
- `example-call-prep-saas-founder-agency-churn-trigger.md` — B2B SaaS founder, prior-agency-churn trigger, anchored to vertical-match case study
- `example-call-prep-services-founder-funding-trigger.md` — B2B services founder, recent-funding trigger, anchored to stage-match case study
- `example-call-prep-program-prospect-revenue-ceiling.md` — agency-operator prospect for program tier, revenue-ceiling trigger
- `example-call-prep-low-confidence-trigger.md` — deck handling LOW trigger confidence (elicitation-mode discovery)

---

*Skill version 1.0.0 — 2026-05-03*
