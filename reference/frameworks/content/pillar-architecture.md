# Pillar Architecture

> The structural rules for the 3-5 content pillars that define what a subject (the agency operator OR a specific client) publishes on LinkedIn — Authority, Proof, Personality, Offer-bridge, Pier — and how the mix calibrates to phase, audience awareness, and scope.

## Why this framework exists

A LinkedIn content engine without pillars is a shuffle of disconnected posts. Some weeks it reads as authority; some weeks as personality; some weeks as direct sell. The audience has no reason to follow because there's no compounding theme. Engagement spikes randomly and never plateaus into pipeline.

Pillars solve three problems at once. They give the engine **focus** (3-5 themes the operator owns, not 12 topics they dabble in). They give the audience **predictability** (the reader knows what they're going to get and self-selects in or out). They give the algorithm **signal** (LinkedIn's recommender weights consistency-of-topic when deciding which followers see your content — a pillar-disciplined account compounds; a scattered account doesn't).

The five pillar types — Authority, Proof, Personality, Offer-bridge, Pier — are the canonical mix for a B2B service-business engine in 2026. Each plays a distinct conversion role. Authority demonstrates competence. Proof demonstrates results. Personality demonstrates identity (and earns the "I want to work with this person" reaction). Offer-bridge converts. Pier compounds algorithmic distribution. A working engine ships all five in calibrated ratios. A broken engine ships only 1-2 (most often Authority + Personality, leaving Proof + Offer-bridge + Pier on the table).

This framework defines how pillars get chosen, mixed, and recalibrated.

## The 7 rules

### Rule 1 — 3-5 pillars per content engine (fewer = monotonous, more = unfocused)

The optimal pillar count is 3-5. Below 3, the audience experiences the engine as repetitive — every post sounds like the last one and engagement plateaus inside 90 days. Above 5, the engine reads as scattered — the operator can't go deep enough on any single pillar to develop proprietary point-of-view, and topic clusters within each pillar shrink to 1-2 (which exhausts material in weeks).

The default for this archetype's agency-content is 5 pillars (Authority + Proof + Personality + Offer-bridge + Pier). For early-stage operators with thin proof, 4 pillars (drop Proof, redistribute to Authority + Personality) until 2-3 case studies exist. For high-volume program operators with deep audience, 5 pillars with sub-pillars under Authority and Proof.

For client-content engines, 3-5 is also the band, but the choice is downstream of the client's solution-awareness target (Rule 3). A client whose audience is problem-aware doesn't need 5 pillars yet; they need 3 well-executed pillars that build awareness first. A client whose audience is most-aware needs all 5 with Offer-bridge dominant.

### Rule 2 — Pillar mix calibrates to phase

The pillar mix is not static. It calibrates to the engine's phase: Phase 1 (audience-building, <5K followers, no inbound from content), Phase 2 (audience-converting, 5K-25K followers, inbound starting), Phase 3 (audience-monetizing, 25K+ followers, inbound is primary acquisition).

**Phase 1 default mix:** 30% Authority + 30% Personality + 15% Proof + 15% Pier + 10% Offer-bridge. Heavy Authority and Personality builds the "this person knows things AND I like them" foundation. Proof is light because there's not enough material yet. Pier is the multiplier — leveraging others' high-engagement posts to compound distribution. Offer-bridge stays low (<15%) because aggressive selling on a small audience burns the followers you do have.

**Phase 2 default mix:** 25% Authority + 25% Proof + 20% Personality + 20% Offer-bridge + 10% Pier. Proof rises (case studies exist now). Offer-bridge rises (audience can convert). Authority + Personality stay anchor. Pier reduces (the account has its own engagement gravity now).

**Phase 3 default mix:** 20% Authority + 25% Proof + 15% Personality + 30% Offer-bridge + 10% Pier. Offer-bridge dominates because monetization is primary. Proof stays high (this is what the audience expects). Personality reduces but never below 15% (this is what differentiates from competitor accounts at scale).

Recalibration happens at phase transitions, not weekly (per Rule 6).

### Rule 3 — Pillar mix calibrates to ICP solution-awareness

For client-content (and for agency-content where the agency targets a specific solution-awareness band), the mix calibrates to where the ICP is in the awareness spectrum: problem-aware, solution-aware, most-aware.

**Problem-aware ICP (knows they have a problem, doesn't know solutions exist):** heavy Personality + Pier. Personality builds trust before the prospect can engage with solutions. Pier (engaging with related conversations the ICP already follows) puts the operator into the prospect's visible orbit. Authority and Proof are present but secondary — the ICP isn't ready to evaluate frameworks yet. Offer-bridge is minimal.

**Solution-aware ICP (knows solutions exist, comparing approaches):** heavy Proof + Authority. The ICP is in evaluation mode. Proof shows results from your specific approach. Authority shows why your mechanism works. Personality stays moderate (still matters for trust). Offer-bridge rises to 20-25% because this ICP is buying-curious.

**Most-aware ICP (knows your specific approach, deciding whether to buy):** heavy Offer-bridge. The ICP is at decision point. Offer-bridge moves them from interest to action. Proof reinforces the decision. Authority is moderate (they already trust competence). Personality and Pier are low (no need to keep building).

The solution-awareness calibration is per-account, set at engine build, recalibrated when the account grows past a band. Default for new client-content: assume solution-aware unless the ICP profile explicitly says otherwise.

### Rule 4 — Each pillar maps to 2-4 post types

Pillars are themes; post types are forms. A pillar without 2-4 post types collapses into a single repeating format (every Authority post is a 5-bullet listicle) and audience boredom kicks in within 30 days. A pillar with >4 post types loses identity — readers stop recognizing the pillar's signature.

The post-type mapping (default for this archetype):

| Pillar | Post types (2-4 per pillar) |
|---|---|
| **Authority** | Authority post (mechanism breakdown), Listicle (specific-numbered learnings), Contrarian-reframe, Insider-secret hook posts |
| **Proof** | Case-study post, Failure-lesson, Before-after transformation, Thought-proof-CTA |
| **Personality** | Personal-to-business, Day-in-life, Loop-open story, Question-stake hook posts |
| **Offer-bridge** | Hook-loop-CTA, Direct-CTA post, Lead-magnet post (comment-trigger), Thought-proof-CTA |
| **Pier** | Pier post (controversial thesis), Pattern-break, Comment-as-content (high-substance reply on others' posts) |

Each post type carries its own structure (hook formula, body template, CTA pattern) — see `reference/frameworks/content/hook-anatomy.md` for hook formulas and `reference/swipe-file/posts-linkedin/{post-type}/` for annotated golden examples.

The 2-4 mapping ensures variety within a pillar without losing pillar identity. Authority post + Listicle + Contrarian-reframe is recognizable as Authority. Authority post + Listicle + Day-in-life + Lead-magnet post + Pier post is incoherent — those post types span 4 pillars.

### Rule 5 — Per pillar, define: name + objective + topic clusters + conversion bridge + evidence of resonance

Every pillar in the spec carries five required attributes. Without them, the pillar is a label, not a working production unit:

```yaml
- pillar_name: ""                        # short, distinctive, operator-owned (e.g. "The Recursive ICP Method")
  pillar_objective: ""                   # which of the 5 pillar types: Authority/Proof/Personality/Offer-bridge/Pier
  topic_clusters: []                     # 4-7 clusters this pillar pulls from (e.g. for Authority: "DM intent classification", "qualification thread design", "application gate construction")
  conversion_bridge: ""                  # how this pillar moves the reader toward the offer (e.g. "Authority post → comment-trigger 'PLAYBOOK' → DM with PDF → qualification thread → application")
  evidence_of_resonance: []              # past posts in this pillar that hit (with engagement data — impressions, comments, calls-from-post)
```

**Pillar name** is operator-owned and distinctive — "The Recursive ICP Method" carries weight; "Marketing Tips" doesn't. Names compound — once "The Intent Taxonomy" is established, every reference reinforces the operator's IP.

**Topic clusters** are the 4-7 themes within the pillar. Without clusters, ghostwriters improvise topics and the pillar drifts. With clusters, every post is a defensible execution of a known theme.

**Conversion bridge** is what makes the pillar revenue-producing instead of engagement-only. Per the failure-modes table in `/build-content-engine`, pillars without bridges produce engagement without revenue. Authority pillars are the most-frequently bridge-skipped because the operator "feels salesy" — always include at least a soft bridge.

**Evidence of resonance** prevents the pillar from being aspirational instead of empirical. Pull 3-5 past posts in the pillar with engagement data; if you can't, the pillar is a hypothesis, not a pillar.

### Rule 6 — Per phase or audience-state shift, recalibrate (don't recalibrate weekly — quarterly)

Pillar mixes are quarterly, not weekly. Recalibrating the mix every Monday in response to last week's engagement produces noise — engagement data needs 4-8 weeks of signal to indicate a real trend vs. an algorithm pocket or a single viral outlier. Operators who recalibrate weekly produce engines that never compound because the audience never knows what to expect.

**Quarterly recalibration cadence:** at end of Q1 (90 days post-launch), at end of Q2 (180 days), at end of Q3 (270 days), at end of Q4 (annual review). Each recalibration: pull last 90 days of engagement data per pillar, compare against pillar weight, decide whether to (a) hold mix, (b) shift weights ±5-10%, (c) sunset a pillar, (d) introduce a new pillar.

**Off-cycle recalibration triggers:** phase transition (account crosses 5K, 25K, or 100K followers — Rule 2 applies), new offer launch (Offer-bridge pillar weight rises during launch window), client onboarding for client-content engines (build new mix for that client per Rule 3), audience drift (engagement plateau >3 months — sunset stale pillar, introduce new one).

The week-level decision is which post types to publish on which days; the quarter-level decision is which pillars to weight how heavily. Don't confuse the two cadences.

### Rule 7 — Recursive scope: agency content engine OR per-client content engine — never confused

The framework applies recursively to two scopes that must never be confused: the agency's own content engine (operator's own LinkedIn) and per-client content engines (one engine per client account the agency ghostwrites for). Each scope has its own pillar mix, its own topic clusters, its own evidence of resonance, its own conversion bridges, its own calendar.

Per N-1 and N-6, voice contamination between scopes is a sacred-rule violation — agency voice on a client account causes churn at month 4. The same applies to pillar contamination: agency pillars on a client account are a recursion failure. The agency's "Recursive ICP Method" pillar is not transferable to a client account that doesn't share that mechanism.

Per A-6, recursive context is load-bearing. The skill (`/build-content-engine`) hydrates from the correct scope: `agency-content` reads from `company.yaml::content_strategy.agency_content`; `client-content` reads from `company.yaml::content_strategy.client_content[{client_slug}]`. Pillar mix is set per scope, recalibrated per scope, and shipped per scope. Cross-pollination of pillar names, topic clusters, or conversion bridges between agency and client engines is a recursion violation.

The runtime guard: every artifact emitted from `/build-content-engine` carries a `scope` field (agency-content or client-content) and a `client_slug` (null for agency, slug for client). Downstream skills (`/write-linkedin-post`, `/ghostwrite-client-post`) refuse to consume an artifact whose scope doesn't match the request scope.

## Pillar spec template

```yaml
content_pillars:
  - pillar_name: "<distinctive operator-owned name>"
    pillar_type: Authority | Proof | Personality | Offer-bridge | Pier
    weight_percentage: 10-30                 # sums with other pillars to 100
    post_types: [<2-4 post types per Rule 4>]
    topic_clusters:
      - cluster: "<topic 1>"
        depth: shallow | medium | deep
        material_pool: [<list of source materials, past posts, frameworks, case studies>]
      # ... 3-6 more clusters
    conversion_bridge:
      bridge_type: comment-trigger | DM-to-call | direct-CTA | lead-magnet
      bridge_copy: "<the actual CTA text>"
      downstream_state: "Class M (lead magnet) | Class L (liked-post) | Application gate"
    evidence_of_resonance:
      - { post_url: "<URL>", date: YYYY-MM-DD, impressions, comments, calls_booked, notes }
      # ... 2-4 more pieces of evidence
  # ... 2-4 more pillars

pillar_mix_calibration:
  scope: agency-content | client-content
  client_slug: null | "<slug>"
  current_phase: Phase-1 | Phase-2 | Phase-3
  icp_solution_awareness: problem-aware | solution-aware | most-aware
  mix_set_at: YYYY-MM-DD
  next_recalibration_date: YYYY-MM-DD          # quarterly default
  off_cycle_triggers_armed: [phase-transition, new-offer-launch, audience-drift]
  rationale: "<one paragraph: why this mix for this phase + awareness state>"
```

## Failure modes

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| Pillar overlap | Two pillars with overlapping topic_clusters and conversion_bridges; audience can't distinguish them | Pillars defined by aspiration, not material | Collapse into one pillar with two sub-clusters; per Rule 5, evidence-of-resonance check would have caught this |
| Pillar without bridge | High engagement, no revenue | Operator skipped conversion_bridge because "Authority feels salesy" | Per Rule 5, every pillar requires at least a soft bridge; rebuild bridge for the under-converting pillar |
| Mix misaligned with phase | Phase 1 account running 30% Offer-bridge; followers churn | Operator copied a Phase 3 mix without recalibrating | Per Rule 2, reset to Phase 1 default; Offer-bridge ≤15% until 5K followers |
| Mix misaligned with awareness | Most-aware ICP receiving 50% Personality content; engagement-without-conversion | Per Rule 3, awareness-state was set wrong at engine build | Re-evaluate ICP solution-awareness; rebuild mix per Rule 3 default for actual awareness state |
| Pillar count <3 or >5 | Engine reads as monotonous (≤2) or scattered (≥6) | Operator chose pillar count by convenience, not by Rule 1 | Audit pillars; collapse overlap (>5) or split too-broad pillar (≤2) |
| Weekly mix recalibration | Engine never compounds; audience never recognizes pattern | Operator reacting to weekly engagement data | Per Rule 6, quarterly cadence; weekly decisions are post-type, not pillar weight |
| Agency pillars on client account | Client churns at month 4 with "this doesn't sound like my brand" | Per Rule 7, scope confusion in `/build-content-engine` build | Rebuild client-content from client ICP + client voice; per A-6, hydrate from `client_content[slug]` only |
| Pillar without evidence of resonance | Pillar is hypothesis, never validates | Operator built pillar from intuition without checking past-post data | Per Rule 5, every pillar requires 3-5 past-post evidence; if absent, demote to "experimental" status with 30-day validation window |
| Post types not mapped to pillars | Ghostwriter improvises post type per post; output drifts | Per Rule 4, pillar definition skipped post-type mapping | Map 2-4 post types per pillar; ghostwriter selects from mapped set, not from full library |

## Cross-references

- `skills/build-content-engine/SKILL.md` — the build skill that produces engine specs from this framework
- `skills/write-linkedin-post/SKILL.md` — consumes the pillar spec for agency posts
- `skills/ghostwrite-client-post/SKILL.md` — consumes the pillar spec for client posts (per-client scope)
- `skills/audit-hook-library-decay/SKILL.md` — quarterly check that runs alongside pillar recalibration
- `skills/build-icp/SKILL.md` — produces the ICP solution-awareness signal that calibrates Rule 3 mix
- `skills/extract-founder-voice/SKILL.md` — voice profile that prevents Rule 7 scope contamination
- `reference/frameworks/content/hook-anatomy.md` — the 9 hook formulas mapped to post types in Rule 4
- `reference/frameworks/content/virality-engine.md` — the 5 mechanisms each pillar can integrate
- `reference/swipe-file/posts-linkedin/` — annotated examples per post type (one folder per type)
- `reference/platforms/linkedin/algorithm-notes.md` — quarterly algorithm shifts that may trigger off-cycle recalibration
- `INVARIANTS.md` N-1 (client voice load-bearing), N-6 (no agency-client voice contamination), A-6 (recursive context for client scope), A-9 (rejection signal updates pillar mix)

---

*Framework version 1.0.0 — 2026-05-03*
