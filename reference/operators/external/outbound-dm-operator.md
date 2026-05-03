---
archetype_slug: outbound-dm-operator
descriptor: "the outbound DM operator"
tier: external
version: 1.0
last_updated: 2026-05-03
signal:
  mode: reference
  genre: operator-archetype
  type: external-pattern-dossier
  format: markdown
  structure: "tightened-17-section"
diagnostic:
  primary_lever: Volume + Qualification
  awareness_levels_served: [Problem-aware, Solution-aware]
  sophistication_stages: [Aware, Skeptical]
  three_brain_register: [Limbic, Reptilian]
  influence_principles: [Reciprocity, Liking, Scarcity]
  emotions_leveraged: [Greed, Fear, Curiosity]
activation_signals:
  - "business_context.health_signals.runway_months <= 3"
  - "operator demonstrated capacity for volume work (tolerates 200-400 manual sends/week)"
  - "ideal_customer_profile.agency_icp.firmographics.linkedin_addressable_count >= 5K"
  - "operator willing to qualify hard (decline 60%+ of inbound conversations)"
  - "vertical has identifiable trigger events visible on LinkedIn (funding, hire, role change, growth signal)"
contraindications:
  - "operator unwilling to do volume work (cannot sustain 80+ manual personalized sends/week)"
  - "ideal_customer_profile.agency_icp.firmographics.revenue_stage = '$100M+ enterprise' (LinkedIn DM is wrong channel above mid-market)"
  - "vertical has no LinkedIn-visible signal — anonymous decision-makers, gov/regulated industries"
  - "operator's offer is sub-$2K/mo (LTV math collapses at this volume cost)"
  - "operator already has 25K+ engaged followers (content engine already viable; outbound is sub-optimal use of time)"
closest_siblings:
  - hybrid-full-stack-operator
  - cold-email-operator
  - sales-development-led-operator
related_anti-patterns:
  - "the-spray-and-pray-volume-operator" (sends 1500+ generic DMs/week, account suspended within 60 days)
  - "the-half-personalized-operator" (personalizes M1 only, sequence falls apart by M3)
  - "the-no-qualification-operator" (books every reply onto calendar, no-show rate 50%+)
skills_cited:
  - build-outbound-engine
  - source-lead-list
  - score-lead-intent
  - classify-dm-intent
  - write-dm-sequence
  - run-client-dm-ops
  - build-application
  - build-call-prep
  - build-qualification-thread
agents_loading:
  - foundations-head
  - sales-head
  - dm-operator
playbook_crosswalk:
  existing:
    - hybrid-full-stack.md (the meta-playbook — this archetype is the Year-1 component)
sources:
  notion_page_ids: []
  kb_files: []
  proof_as_product_anonymized: 0
  public_corroboration_count: 8+
  anonymization_status: clean
anti_pattern_guard: "When operator presents 'I want fast revenue from outbound' but `business_context.health_signals.short_term_goals` includes 'build personal brand' AND `years_active >= 3` AND `revenue_stage >= $50K/mo`, do not classify as this archetype — flag as inbound-content-authority candidate; outbound here is sub-optimal use of operator time."
---

# the outbound DM operator — Operator Archetype

> **Tier:** External · **Archetype slug:** outbound-dm-operator
> **Primary lever:** Volume + Qualification
> **Lead-gen channel(s):** LinkedIn outbound DM (Class C dominant) + cold email + light supporting content
> **Offer tier:** $3K-$10K/mo retainer + done-with-you (DWY) hybrid common
> **Time-to-revenue (typical):** 14-30 days from engine setup to first signed retainer
> **Activation signals:** see frontmatter `activation_signals`.

> **Why this matters:** the outbound DM operator is the short-runway, fast-revenue counterpart to the inbound content authority. Most LinkedIn agencies that survive their first 12 months do it via this archetype — outbound produces cash in week 2, content produces cash in month 9. The trade is well-defined: outbound is a rented pipeline (the moment sends stop, calls stop), and the operator's time produces no compounding asset. Most successful agencies eventually graduate from this archetype OR layer it under a content engine. Profile carefully — outbound-only past month 18 is a known plateau pattern.

## Source / Lineage

- Profile synthesized from public LinkedIn behavior of 8+ B2B agency operators in the outbound-led category, 2023-2026, observable via DM-volume signals + public outbound case studies
- Three methodology streams converge here:
  1. **Cold-email DNA** — the qualification-and-sequence discipline of cold-email ops (Predictable Revenue lineage), ported to LinkedIn DM with platform-specific adaptation
  2. **LinkedIn-DM evolution** — the 2018-2024 shift from automated mass-DM (which LinkedIn killed via shadow-banning) to manual-sent personalized sequences with intent classification
  3. **Sales-development discipline** — the SDR/BDR playbook of high-volume qualification + booking + handoff, run by the operator solo in the early stages
- Cross-referenced against ~10 outbound-led agency teardowns and operator podcasts
- No client engagement data, no partnership data, no proprietary information used — purely public-pattern synthesis

## Background

**Who this archetype is.** Founder-operator (or 2-3 person team) of a B2B LinkedIn agency that runs primarily on outbound DM volume. Sends 80-300 personalized DMs per week (across operator + VA + client accounts), classifies by intent, qualifies hard, books discovery calls, closes retainers. Content presence exists but is light — usually 1-2 supporting posts per week, not the primary lever.

**Track record (typical at maturity).**
- Operator's own LinkedIn followers: 2K-12K (audience is small because outbound, not content, is the lever)
- Outbound DM volume: 200-500/week (combined operator + VA + client account ops)
- Reply rate: 8-15% on cold (Class C); 30-50% on warm (Class W when content does run)
- Discovery calls: 6-15/week
- Close rate: 18-30% on qualified calls
- Retainer book: 8-20 clients at $3K-$8K/mo
- Annual revenue: $400K-$1.2M typical at maturity (lower ceiling than content-led; higher floor in Year 1)

**How they got there.** Fast curve. Month 1: engine setup + first 200 sends produce 1-2 calls. Month 2: 600 sends, 8-12 calls, 1-3 signed. Month 3-4: rhythm locks in, 1-3 signs/month. Year 1 close: 6-12 retainers, $30K-$60K/mo revenue. The compounding curve is FLAT, not exponential — month 18 looks like month 12 looks like month 9 (modulo seasonal noise) because the asset is the operator's send-volume capacity, not an audience. That plateau is the trigger to either layer content (becoming hybrid-full-stack-operator) or productize the engine (becoming run-client-dm-ops-as-service).

## Core Thesis

**"Cash flow now via outbound. Content as long-runway compounding asset, not survival channel."**

Unpacked:

1. The operator with <90 days runway cannot wait 6-12 months for content to compound. Outbound produces calls in week 2. The math is non-negotiable.
2. Outbound is a rented pipeline — sends stop, calls stop within 14 days. This is a feature in months 1-12 (predictable cash from predictable inputs) and a bug past month 18 (no compounding, capacity-trapped).
3. The intent taxonomy is the leverage. Operators who run one Class-C sequence at one list cap at ~7% reply rate and burn lists. Operators who classify intent and route to four sequences run 15-25% blended reply rate and don't burn lists.
4. Qualification is the unsung lever. The temptation is to book every reply onto calendar (volume = volume). The operators who survive past month 6 qualify HARD — decline 60%+ of replies, book only the qualified 40%, and run 22-30% close on qualified calls instead of 8-12% close on raw replies.
5. Content is the asset that lets the operator graduate from this archetype. Year 1: outbound carries revenue. Year 2: layer content seeding. Year 3+: content compounds enough that outbound volume can be cut in half without revenue loss. The hybrid is the goal; pure outbound past month 18 is a plateau.

## When this archetype fits

This archetype is the right call when ALL of the following hold:

- **Operator runway is short (≤3 months).** Content compounding takes 6-12 months. The operator who can't survive that window has no choice — outbound first, content later.
- **Operator can do volume work.** This is unglamorous: 80-200 manual personalized sends/week, every week, for 18+ months. Operators who burn out at volume cannot run this archetype solo (must hire VAs by month 6).
- **Vertical addressable on LinkedIn.** ICP must be reachable — visible decision-makers, identifiable on Sales Nav with clean firmographic filters, with LinkedIn-active behavior. Sub-vertical examples: SaaS founders, B2B agency owners, mid-market SDRs, e-commerce DTC founders, professional services partners. Anti-examples: anonymous gov contractors, regulated medical device buyers, traditional industrial buyers (low LinkedIn presence).
- **Operator willing to qualify hard.** This archetype's economics depend on a 60%+ DQ rate at the application stage. Operators who book every interested reply produce 50%+ no-show rates and burn through their reputation in the vertical within 12 months.

## The arc — three phases

### Phase 1 — Months 1-4: Outbound-only, pure-volume

- Engine setup: list sourcing → intent taxonomy → sequence drafts → qualification thread → application → calendar gate
- Daily cadence: 60-80 sends/day operator + (optionally) 80-120 sends/day VA
- Weekly: 400-700 total sends, 30-50 replies, 15-25 qualified, 5-10 calls, 1-3 signs
- Content: zero or 1 light post/week; not the lever
- Revenue: $5K → $25K MRR by end of month 4
- Risk: operator burns out; LinkedIn account warned for high-volume sends; list quality plateaus by week 8 (need fresh sources)

### Phase 2 — Months 5-12: Outbound + content seeding

- Outbound volume holds at 300-500/week
- Content layers in: 2-3 posts/week, primarily Authority + Proof pillars (lighter on Personality — operator hasn't built audience for vulnerability posts yet)
- Content is calibrated for **Class W signal generation** — posts seed warm leads who later get sequenced via Class W treatment (45%+ reply rate vs. 11% on cold)
- Class L (liked-your-post) DMs become viable as engagement starts forming on posts
- Revenue: $25K → $50K MRR
- The shift: outbound efficiency improves because Class W and Class L take share from Class C — operator sends fewer cold messages for the same call volume

### Phase 3 — Months 13+: Hybrid (content carrying weight)

- Outbound volume can drop to 150-250/week without revenue loss because Class W + Class L now carry 40-60% of pipeline
- Content cadence holds at 3-5 posts/week
- Operator approaches the hybrid-full-stack-operator archetype's profile (route check at this point — many operators graduate)
- The decision: keep outbound DM as core lever (stay outbound-DM-operator) OR pivot to content-led (graduate to inbound-content-authority over months 12-18)
- Operators who don't decide stay stuck in transitional limbo with both engines underperforming

## The decision tree (when to graduate)

```
Operator at month 12 — assess:

  Is content producing ≥30% of inbound DMs?
  ├── YES → Graduate to hybrid-full-stack-operator
  │         (Reduce outbound volume by 30%, lean into content)
  └── NO  → Have you hired/built a VA team?
            ├── YES → Productize outbound as a service to other operators
            │         (Run-client-DM-ops becomes the new revenue lever)
            └── NO  → Continue outbound-DM-operator for one more 6-month cycle
                      Re-assess at month 18; if still no content traction:
                      operator likely has voice-extraction or pillar-misfit
                      problem; route to /extract-founder-voice deep-dive
```

## The acquisition engine

This archetype's acquisition engine runs the **DM Intent Taxonomy** (per `reference/frameworks/dm/intent-taxonomy.md`) with heavy emphasis on Class C and Class W:

| Class | Volume share at maturity | Reply rate | Sequence | Time-allocation |
|---|---|---|---|---|
| **C (Cold)** | 65-75% | 8-15% | 5-7 msgs / 14-21d | 60-70% of operator's send time |
| **W (Warm)** | 15-25% | 30-50% | 3-5 msgs / 7-14d | 15-20% of send time |
| **L (Liked-your-post)** | 5-10% | 50-70% | 2-4 msgs / 3-7d | 10-15% of send time |
| **M (Comment-for-magnet)** | <5% (often zero in pure-outbound phase) | 80-95% | Class M sequence | <5% of send time |

The Class M underweight is intentional in months 1-12 — the operator doesn't have the content volume to drive comment-trigger CTAs at scale. Class M scales with content scale, so it grows in Phase 2-3 as content layers in.

**The cold-list quality flywheel.** Outbound-led operators live and die by list quality. The mature engine sources lists from 4-6 channels and rotates: Sales Nav (primary), event attendee scrapes (secondary), funded-company lists (trigger-event sourcing), competitor's commenters (high-intent harvest), warm-network referrals (low-volume / high-quality), and LinkedIn job-change alerts (trigger-based). Single-source list strategy plateaus by month 6.

**The qualification thread before calendar.** Per `INVARIANTS.md` N-12, no calendar booking without qualification thread. The thread surfaces firmographic fit, trigger event, budget signal, and decision-making process across 2-4 message exchanges before the calendar link drops. This single discipline lifts close rate from 8-12% to 22-30% by filtering out the 60%+ of replies that aren't fits.

## The fulfillment shape

Outbound-led agencies tend to run **leaner fulfillment** than content-led peers. The economics push this:

- **Less ghostwriting work.** Clients of outbound-led agencies are often hiring the agency to RUN their own outbound, not to write their content. The deliverable is sent DMs + qualified meetings, not posts.
- **More done-with-you (DWY) hybrid.** "We'll set up your outbound engine + train your VA + run sequences for first 60 days, then handoff" is a common offer shape — capacity-friendly for the operator, value-dense for the client.
- **More frequent active-hands work, less judgment work.** Outbound delivery is more rep-able by VAs than content delivery (which requires per-client voice judgment). Outbound-led agencies hire VAs faster and earlier than content-led peers.
- **Lower per-client retainer typical.** $3K-$8K/mo vs. $8K-$15K/mo for content-led. The volume of clients is higher (15-25 vs. 8-12) but per-client effort is lower.
- **Light or absent program tier.** Outbound is harder to teach as a program because the lever is operational discipline at volume, not transferable IP. Programs that try to teach outbound often produce graduates who can't sustain volume — the program tier here is usually a "DM-ops-as-a-service" productized offer rather than a teach-the-method program.

## The program shape (or absence)

Most operators in this archetype do NOT run a program tier. The reasons:

1. **The IP is operational discipline, not transferable framework.** Content-led operators can teach pillar architecture, hook formulas, voice extraction — these are transferable. Outbound discipline (sending 80 personalized DMs/day, every day, qualifying hard, not getting demoralized by 90% non-replies) is operational character, not IP.
2. **The case-study pool degrades fast.** Outbound case studies depreciate within 6-12 months — channels saturate, ICPs shift, list sources burn. Content case studies hold for 24+ months.
3. **The natural revenue extension is productizing the service, not teaching it.** Operators who scale past $100K/mo typically launch a "we'll run your outbound" productized service tier that's a higher-margin extension of the core retainer, not a course.

The exceptions: outbound operators who DO run programs typically teach the **hybrid transition** ("how to layer content under outbound so you can graduate from rented pipeline") — this is taught to other outbound-led operators looking to graduate, not to the agency's own ICP.

## Monetization mechanics

**Year 1 economics (typical):**
- 10-15 retainers at $3K-$5K/mo = $30K-$75K MRR
- 70-80% gross margin (low fulfillment cost — VA delivery)
- Operator owner-comp: $200K-$400K/yr Year 1
- Reinvestment: VA hires, list-sourcing tools (Sales Nav, scrapers), CRM

**Year 2-3 economics (if scaling within archetype):**
- 18-25 retainers at $4K-$8K/mo = $80K-$180K MRR
- VA team of 2-4 running daily DM ops
- Operator transitions from full-time sender to manager-of-senders
- Revenue: $1M-$2M ARR, profit margin compresses to 50-65% (team costs)

**Year 2-3 economics (if graduating to hybrid):**
- Outbound volume cut 30-50%, content compound takes share
- Per-client retainer rises from $4K-$5K to $8K-$12K (content-led pricing power)
- Lower client volume (12-18 vs. 25), higher per-client revenue
- Margin recovers to 70-80%
- Audience asset starts compounding — Year 3+ becomes inbound-led with outbound supplement

**The cash-flow advantage.** This archetype's defining economic feature is fast time-to-revenue. The operator with $15K runway can be cash-positive within 60-90 days. No other archetype matches this.

## Risk profile

1. **LinkedIn account-suspension risk (volume threshold).** LinkedIn enforces invisible volume thresholds — accounts that exceed ~150 outbound-DM-to-non-connections per week trigger soft warnings; ~250+ trigger account restrictions; ~400+ trigger suspensions. The operator running 80 sends/day from one account WILL hit this within 90 days. Mitigation: rotate across operator + VA accounts + client accounts (with permission); never exceed ~120 sends/week per single account; warm up new accounts over 30 days before high-volume use.

2. **Operator burnout from manual sends.** 60-80 personalized sends/day is mentally heavy work — high-context-switching, low-feedback (90% non-reply), repetitive. Operators who try to run this solo past month 6 typically burn out. Mitigation: hire first VA by month 4-5; build send-template + personalization-snippet library to reduce per-send cognitive load; cap operator's personal send volume at 30/day past month 6 (the rest moves to VA capacity).

3. **Audience asset undervalued (no compounding inbound).** This archetype's biggest hidden cost is that the operator's time produces no asset. Year 2 revenue depends entirely on Year 2 send-volume. There is no Year-2 free pipeline from Year-1 audience. Operators who don't add content seeding by month 6-9 stay capacity-trapped indefinitely. Mitigation: explicit content layer-in by month 5 (Phase 2 of the arc), even at light cadence (2 posts/week); track Class W and Class L share of pipeline as the leading indicator that content is starting to carry weight.

4. **List-source burnout.** Single-source list strategy plateaus around month 6 — the same Sales Nav filters get over-mined, ICP fatigue sets in (recipients have seen 15 similar DMs from competitor agencies in the prior month). Mitigation: 4-6 source diversification by month 4; trigger-event sourcing (funded companies, role changes, hires) to surface fresh prospects; monthly list-quality audits.

5. **Reputation risk in the vertical.** High-volume + low-qualification = the operator becomes "that DM-spammer" in the vertical's gossip network. This burns inbound viability for years. Mitigation: hard qualification at the application gate; soft-DQ language baked into M1 of every cold sequence; explicit "we might not be a fit" framing that signals the operator isn't desperate.

6. **The plateau pattern at month 18.** Operators who don't graduate or productize plateau at $50K-$80K MRR — the math is capacity-bound (hours × send rate × close rate). Mitigation: explicit decision at month 12 (graduate to hybrid OR productize outbound-as-service); don't drift past month 18 without decision.

## Adopt this archetype if...

- Operator has ≤3 months runway and needs revenue in 60-90 days
- Operator has demonstrated capacity for volume / repetitive work (or willing to hire VAs early)
- ICP is LinkedIn-addressable with ≥5K identifiable prospects
- Operator can stomach a 60%+ DQ rate (won't book every reply onto calendar)
- Vertical has trigger events visible on LinkedIn (funding, hires, role changes, growth signals)
- Operator's offer is ≥$2K/mo (LTV math supports the per-lead acquisition cost)
- Operator is comfortable with cash-now / no-compounding economics for the first 12 months
- Operator commits explicitly to the Year-2 graduation decision (won't drift past month 18)

## Don't adopt if...

- **Operator has ≥9 months runway.** Outbound is sub-optimal use of that runway — content compounding produces a more durable asset over the same window. Route to inbound-content-authority or hybrid-full-stack.
- **Operator already has 25K+ engaged followers.** The content engine is already viable; outbound is sub-optimal use of operator time. Route to inbound-content-authority.
- **ICP is enterprise ($100M+).** LinkedIn DM is the wrong channel above mid-market — enterprise buyers don't reply to LinkedIn DMs. Route to enterprise-ABM or referral-led acquisition strategy.
- **Vertical has no LinkedIn-visible signal.** Anonymous decision-makers, gov / regulated industries, traditional industrial buyers — outbound DM math doesn't work. Route to vertical-specific channel strategy (events, cold email, partner referral).
- **Operator's offer is sub-$2K/mo.** Per-lead acquisition cost (~$50-150 in operator + VA time) doesn't pencil at sub-$2K LTV. Either raise offer tier or route to a different acquisition model.
- **Operator is unwilling to do volume work AND unwilling to hire VAs.** This archetype requires one of the two. Without either, the engine never scales past 4-6 retainers.

## Specific tactical details worth isolating

Six micro-tactics this archetype uses that are easy to miss when looking at the high-level archetype:

1. **The "wrong?" close on M1 of cold sequences.** Ending the first cold message with "wrong?" or "no need to reply if it's a miss" lifts reply rate ~2x by removing the implicit ask burden. Per `intent-taxonomy.md` Class C section. Operators who skip this micro-tactic run 5-7% reply on cold; operators who use it run 9-14%.

2. **The voice-note in M3 of warm sequences.** When a class-C prospect replies to M2 and the conversation transitions to offer in M3, delivering M3 as a 30-45 second LinkedIn voice note (vs. text) lifts call-book rate ~2-3x. Voice still has scarcity on LinkedIn DMs in 2026 (~3% of operators use it). The voice note signals "human operator, not template" in a way text cannot.

3. **The trigger-event sourcing layer.** Most operators source from static Sales Nav lists. The high-leverage layer is trigger-based sourcing — alerts on funded companies (Crunchbase + LinkedIn cross-reference), new VP hires (LinkedIn job-change alerts), role-change patterns (companies hiring 5+ in a function within 30 days). Trigger-event prospects have 2-3x higher reply rates than static-list prospects because the timing matches their actual buying window.

4. **The qualification thread compression.** Generic qualification flows run 6-8 questions and feel like an interview — prospects bail. The compressed version runs 3 questions: (a) firmographic confirm ("are you currently doing {X} in-house?"), (b) trigger surface ("what changed in the last 90 days that has you looking?"), (c) decision-process signal ("if this looked right, who else needs to be in the conversation?"). Three questions across 2 message exchanges before the calendar link.

5. **The 24-hour "don't follow up" discipline on M1 cold.** Counter-intuitive: when M1 doesn't get a reply within 24 hours, the impulse is to send M2 immediately. The mature operator waits 48-72 hours. Reasoning: M2 sent at <24h reads as "automated drip"; M2 sent at 48-72h reads as "thoughtful person remembering to follow up." Reply rate on M2 lifts ~30% with the wait.

6. **The "client account ops" multiplier.** Mature outbound operators run DM ops not just from their own account but from CLIENT accounts (with permission, contracted scope). A single VA running 3 client accounts at 80 sends/account/week = 240 sends from 4 different LinkedIn profiles, no single-account suspension risk, 3x the pipeline output. This is the operational scale lever that lets the agency push past $80K MRR without hiring a second VA.

## Observable Cadence (typical operating week)

| Block | Hours/week | Activity |
|---|---|---|
| List sourcing + verification | 3-5 | Sales Nav exports + trigger-event scans + manual verify |
| DM sending (operator personal) | 8-12 | 30-60 sends/day, M-F, with personalization layer |
| DM sending (VA managed) | varies | Operator manages VA queue + reviews flagged messages |
| Reply triage + classification | 5-8 | Daily morning block, intent-classify every reply, route to qualification thread or DQ |
| Qualification thread management | 3-5 | 2-4 active threads at any time, M1-M3 conversational depth |
| Discovery calls | 6-12 | 6-15 calls/week typical at maturity |
| Light content posting | 2-3 | 1-2 supporting posts/week, low priority vs. send volume |
| Strategy + planning + retro | 2-3 | Weekly review of reply rates per sequence + per list source |
| Total | 29-48 hrs/wk | High end requires VA support; sub-30 hrs only sustainable with mature VA team |

The job is more time-intensive in the absolute hours than the inbound-content-authority archetype (which runs 19-31 hrs/wk). The trade is that the work converts to revenue in 14-30 days vs. 6-12 months. Outbound time is direct conversion to revenue; content time is compounding asset.

## Workspace integration

- **Skills that load-bear here:**
  - `/build-outbound-engine` — the engine spec that operationalizes this archetype
  - `/source-lead-list` — list-sourcing layer (Sales Nav, trigger-events, competitor harvest)
  - `/score-lead-intent` — pre-send intent scoring on cold prospects
  - `/classify-dm-intent` — runtime classifier for inbound replies (Class C/W/L/M routing)
  - `/write-dm-sequence` — per-class sequence writer (most heavily used: Class C and Class W)
  - `/run-client-dm-ops` — the client-account-ops multiplier (when operator productizes outbound)
  - `/build-application` — qualification gate before calendar
  - `/build-call-prep` — discovery call prep from qualified threads
  - `/build-qualification-thread` — the 3-question compressed qualification flow
- **Agents:**
  - `sales-head` should reference this archetype when operator context flags `runway_months <= 3` AND `revenue_stage = '<$50K/mo'`
  - `dm-operator` sub-agent runs the per-class sequence execution under sales-head supervision
  - `foundations-head` references this archetype during operator onboarding as the Year-1 default for short-runway operators
- **Knowledge layer:**
  - This profile is cited by `reference/playbooks/hybrid-full-stack.md` as the Year-1 component
  - Counterpart to `reference/operators/external/inbound-content-authority.md` (the long-runway alternative)
  - Cross-referenced by `reference/frameworks/dm/intent-taxonomy.md` as the canonical implementation of Class C-dominant taxonomy use
- **Anti-pattern guard:**
  - When an operator with ≥9 months runway tries to adopt this archetype, the workspace flags `runway-overuse` and recommends evaluating inbound-content-authority instead
  - When an operator at $50K+/mo with 25K+ followers tries to scale outbound rather than lean into content, flags `lever-mismatch` and routes to hybrid-full-stack-operator

## Cross-references

- `reference/playbooks/hybrid-full-stack.md` — the meta-playbook this archetype is the Year-1 component of
- `reference/operators/external/inbound-content-authority.md` — the long-runway counterpart archetype
- `reference/operators/external/hybrid-full-stack-operator.md` — the archetype that runs both engines (the typical graduation target)
- `reference/frameworks/dm/intent-taxonomy.md` — canonical taxonomy that this archetype operationalizes
- `reference/frameworks/dm/cold-sequence-architecture.md` — Class C sequence design (heavy use)
- `reference/frameworks/dm/warm-sequence-architecture.md` — Class W sequence design (Phase 2-3 use)
- `reference/frameworks/dm/qualification-thread-design.md` — the 3-question compressed qualification flow
- `reference/frameworks/dm/booking-gate-construction.md` — calendar-gate discipline
- `INVARIANTS.md` N-5 — never run outbound DMs without intent classification
- `INVARIANTS.md` N-12 — never book calendar without qualification thread

---
*v1.0 — 2026-05-03.*
