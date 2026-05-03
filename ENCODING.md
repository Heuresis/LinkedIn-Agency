# ENCODING — Operator Context Profile Schema

> **The variable substitution contract.** This file defines the 6-profile schema that every skill pulls from. `company.yaml` stores the actual values for a specific operator. The completeness of each profile determines which skills can execute.
>
> **Recursive structure:** the schema applies *twice* — once at the agency level (the operator's own agency context) and once at the client level (per-client subprofiles for every active DFY client). Skills that produce client-bound artifacts hydrate from the client subprofile, not the agency profile. Skills that produce agency-bound artifacts hydrate from the agency profile.
>
> **Weighting maps to Recursive ICP Principle:** ICP 20% (highest, since both agency-ICP and client-ICP must be elite) + Voice 15% + Product 20% + Content 15% + Business 15% + Case Studies 15% = 100%.

## The 6 Profiles

### Profile 1 — Business Context (weight 15%)

```yaml
business_context:
  basic_info:
    agency_name: string                # The agency's brand name (operator-owned)
    operator_name: string              # The agency owner / founder (kept private; do not surface in client-bound artifacts)
    website_url: string
    industry_focus: string             # B2B Marketing | Lead Gen | Personal Brand | Vertical-specific
    business_model: string             # DFY-only | Program-only | Hybrid (DFY + Program) | DFY + DWY + Program
    revenue_tier: string               # pre-revenue | $10K-$50K/mo | $50K-$250K/mo | $250K-$1M/mo | $1M+/mo
    years_active: integer
    team_size: integer

  positioning:
    mission_statement: string          # One-sentence. What this agency exists to do.
    vision_statement: string           # One-sentence. What world this agency is building toward.
    core_values: [string]              # 4-6 values (specific, not platitudes)
    unique_selling_proposition: string # The one-sentence USP — what no other LinkedIn agency can claim
    competitive_advantages: [string]   # 5-7 specific advantages, each tied to evidence
    category_positioning: string       # Category they own (e.g. "DM-led B2B lead generation for SaaS founders")

  market_position:
    target_markets: [string]           # Which markets they sell to (the agency's ICP at the segment level)
    customer_segments: [string]        # Distinct segments inside the target markets
    competitors:                       # Direct + adjacent competitors with positioning notes
      - name_descriptor: string        # Use methodology descriptors, never real competitor names
        what_they_do: string
        how_we_differ: string

  partnerships_distribution:
    key_partnerships: [string]
    distribution_channels: [string]    # LinkedIn organic | YouTube | DM outreach | Referral | Speaking | Podcast
    referral_engine: string            # How current clients/students become next clients/students

  operations:
    tech_stack:
      crm: string                      # GoHighLevel | HubSpot | Close | Notion | Custom
      sales_navigator_tier: string     # Free | Premium | Sales Navigator | Recruiter
      dm_automation: string            # ManyChat | LinkedHelper | Heyreach | None
      booking: string                  # Calendly | Savvycal | Cron
      client_communication: string     # Slack | Loom | Voxer | WhatsApp
      content_scheduling: string       # Taplio | Buffer | Native LinkedIn | Notion
      docs_sops: string                # Notion | Slite | Coda
      training_delivery: string        # Loom | Skool | Circle | Custom
      payment: string                  # Stripe | PayPal | Custom
      analytics: [string]
    team_structure:
      roles: [string]
      responsibilities: {}             # Keyed by role
      hiring_pipeline_status: string

  health_signals:
    current_challenges: [string]       # 3-5 honest pressure points
    short_term_goals: [string]         # Next 90 days
    long_term_goals: [string]          # Next 12-24 months
    key_metrics:                       # The 5-7 numbers the operator watches weekly
      - metric_name: string
        current_value: string
        target_value: string

  brand_assets:
    logo_descriptor: string
    typography: string
    color_palette: string
    voice_guidelines_summary: string   # 1-line — full breakdown lives in Profile 2
```

### Profile 2 — Brand Voice (weight 15%)

```yaml
brand_voice:
  agency_voice:                        # The operator's own voice — used for the agency's content
    communication_style: string        # direct / consultative / contrarian / technical / storytelling
    tone_framework:
      primary: string                  # e.g. "blunt-warm"
      secondary: string                # e.g. "anti-establishment-with-proof"
    personality_traits: [string]       # 3-5 traits
    language_patterns: [string]        # Cadence patterns, signature constructions
    phrases_to_use: [string]           # Verbatim phrases the operator uses
    phrases_to_avoid: [string]         # Words/phrases that break voice
    persuasion_style: string           # authority-by-proof / story-first / mechanism-first / contrarian-provocation
    authority_positioning: string      # Category-king / challenger / insider / outsider-with-edge
    story_bank:                        # 5-12 stories the operator tells regularly
      - story_slug: string
        story_type: string             # Origin / Transformation / Conflict / Revelation / Anti-Hero
        emotional_arc: string
        key_lessons: [string]
        proof_anchors: [string]        # Numbers/specifics that make the story credible

  client_voices:                       # KEY: list of voice subprofiles, one per active client
    - client_slug: string              # Anonymized internal slug — never a real name in artifacts
      vertical: string
      role: string                     # Founder / CEO / Sales Leader / Marketing Leader
      voice_extraction_status: string  # interview-pending | extracted-draft | extracted-validated | sharpened
      communication_style: string
      tone_framework: {}
      personality_traits: [string]
      language_patterns: [string]
      phrases_to_use: [string]
      phrases_to_avoid: [string]
      pet_peeves: [string]             # What this client refuses to say or sound like
      story_bank: []
      voice_drift_threshold: float     # Cosine-similarity threshold below which `/voice-drift-detector` flags
      last_drift_audit_date: string
      last_drift_audit_score: float
```

### Profile 3 — Ideal Customer Profile (weight 20% — HEAVIEST, recursive)

```yaml
ideal_customer_profile:
  agency_icp:                          # Who the agency sells to
    demographics:
      age_range: string
      gender_split: string
      geography: [string]
      income_range: string
      education: string
    firmographics:
      role: string                     # Founder / CEO / Sales Leader / Marketing Leader / Head of Demand Gen
      company_stage: string            # Solo | 2-10 | 11-50 | 50+
      revenue_stage: string            # <$1M | $1M-$10M | $10M-$50M | $50M+
      industry: [string]
      sales_motion: string             # Self-serve | Inside sales | Outbound-led | Channel
    psychographics:
      pain_points: [string]            # 7-12 specific pains in the ICP's verbatim language
      desires: [string]                # 7-12 outcomes
      fears: [string]
      aspirations: [string]
      beliefs: [string]                # What they currently believe about LinkedIn / agencies
      objections: [string]             # Sale-killing objections (min 12 — agencies face many)
    behavioral_patterns:
      buying_triggers: [string]        # Trigger events: failed last agency, hired in-house ghostwriter who quit, content hit a plateau, raised a round, hit revenue ceiling
      decision_making_process: string  # Solo | partner-consult | board-approval | committee
      research_behavior: [string]      # Where they search, who they follow, what they read
      trust_signals_required: [string] # What proof they need before signing
    market_sophistication:
      awareness_level: string          # Most leads land at Solution-aware / Product-aware
      solution_awareness: string       # Naive | Aware | Skeptical | Exhausted (LinkedIn services market is Skeptical→Exhausted)
      market_maturity: string          # Mature
      competitive_density: string      # High
    voice_of_customer:
      actual_customer_language: [string] # Verbatim from sales calls, DMs, application forms
      pain_language_patterns: [string]
      desire_language_patterns: [string]
      objection_patterns: [string]
    limiting_belief_diagnosis:
      dominant_belief: string          # Helpless > Hopeless > Worthless typical for B2B founders re LinkedIn

  client_icps:                         # Per-client ICP subprofiles. Each active client has one.
    - client_slug: string
      vertical: string
      icp_status: string               # extraction-pending | drafted | validated | proven-by-results
      demographics: {}
      firmographics: {}
      psychographics: {}
      behavioral_patterns: {}
      market_sophistication: {}
      voice_of_customer: {}
      limiting_belief_diagnosis: {}
      content_resonance_evidence: [string]  # Top-performing client posts that confirm ICP signal
      dm_reply_evidence: [string]            # Top-replying DM threads that confirm ICP signal
```

### Profile 4 — Content Strategy (weight 15%, recursive)

```yaml
content_strategy:
  agency_content:                      # The agency's own content (operator-owned LinkedIn presence)
    content_pillars:
      - pillar_name: string
        pillar_objective: string       # What this pillar accomplishes (authority / proof / personality / offer-bridge)
        topic_clusters: [string]
        conversion_bridge: string      # How this pillar bridges to the agency's offer
        evidence_of_resonance: [string] # Past posts in this pillar that hit
    post_types:
      - post_type_name: string         # Authority post | Personal story | Lead magnet post | Mechanism breakdown | Case study | Contrarian take | Listicle | Pier (lurking-leverage) | Day-in-life
        purpose: string
        structure: string
        when_to_use: string
        examples_path: string          # Path to swipe-file examples
    hook_library:
      hook_taxonomy_path: string       # `reference/frameworks/content/hook-anatomy.md`
      proven_hooks: [string]           # Verbatim hooks the agency has used that hit
      banned_hooks: [string]           # Hooks dead on LinkedIn in current quarter
    posting_cadence:
      frequency: string                # 5x/week | daily | 2x/day
      time_blocks: [string]            # Tuesday 9am ET / Thursday 11am ET ... etc
      batching_protocol: string        # Sunday-batch-week | Daily-spike | Mixed
    virality_engine:
      virality_engine_path: string     # `reference/frameworks/content/virality-engine.md`
      target_metric: string            # 100K impressions/post target | 10K min | etc
      viral_post_anatomy: string

  client_content:                      # Per-client content strategy subprofiles
    - client_slug: string
      content_pillars: []
      post_types: []
      hook_library:
        proven_hooks: []
        banned_hooks: []
      posting_cadence: {}
      virality_engine: {}
      ghostwriter_assigned: string     # Internal role
      voice_drift_threshold: float
      content_sourcing_protocol: string # How raw material gets from client → ghostwriter
```

### Profile 5 — Case Studies & Client Results (weight 15%)

```yaml
case_studies:
  agency_case_studies:                 # Client transformations the agency uses as proof
    - client_slug: string              # Anonymized
      vertical: string
      starting_state:
        followers: integer
        avg_post_impressions: integer
        inbound_calls_per_month: integer
        outbound_meetings_per_month: integer
        revenue_from_linkedin: integer
      ending_state:                    # After N months of engagement
        followers: integer
        avg_post_impressions: integer
        inbound_calls_per_month: integer
        outbound_meetings_per_month: integer
        revenue_from_linkedin: integer
      time_horizon_months: integer
      key_unlocks: [string]            # What broke through (mechanism, hook, pivot, ICP refinement)
      transformation_language: [string] # Verbatim phrases the client used to describe the change
      proof_assets: [string]           # Paths to screenshots, videos, before/after metric snapshots
      use_in_sales: boolean            # Cleared for use in sales materials with anonymization?
      use_in_content: boolean          # Cleared for use in agency content with anonymization?

  program_case_studies:                # Student transformations from the program
    - student_slug: string             # Anonymized
      starting_state:
        agency_revenue_monthly: integer
        client_count: integer
        time_per_client_per_week_hours: integer
      ending_state:
        agency_revenue_monthly: integer
        client_count: integer
        time_per_client_per_week_hours: integer
      time_horizon_months: integer
      key_unlocks: [string]
      transformation_language: [string]
      proof_assets: [string]

  proof_asset_inventory:
    screenshots: integer
    video_testimonials: integer
    written_testimonials: integer
    metric_dashboards: integer
    before_after_comparisons: integer
```

### Profile 6 — Product Strategy (weight 20%)

```yaml
product_strategy:
  service_tier:                        # The DFY agency services
    offers:
      - offer_slug: string
        offer_name: string             # Public-facing
        offer_type: string             # DFY content + DM | DFY content only | DFY DM only | DFY full-stack
        price_point_monthly: integer
        contract_term_months: integer
        positioning: string
        core_promise: string
        unique_mechanism: string       # What makes THIS retainer work — must be specific
        deliverables_per_month:
          - deliverable_name: string
            volume: string             # e.g. "20 LinkedIn posts" / "300 personalized DMs" / "1 client report"
            quality_bar: string        # The standard the deliverable must meet
        scope_guardrails: [string]     # What is explicitly OUT of scope (prevents scope creep)
        value_stack:                   # Each component with a dollar value (anchor)
          - component: string
            value_usd: integer
        bonuses:                       # 3-5 bonuses, each counters a specific objection
          - bonus_name: string
            value_usd: integer
            objection_countered: string
        guarantee: string              # Specific risk-reversal — performance, timeline, or refund
        target_avatar: string          # Which client ICP segment this is for
        onboarding_protocol_path: string

  program_tier:                        # The high-ticket education program for other operators
    offers:
      - offer_slug: string
        offer_name: string
        offer_type: string             # 1:1 mentorship | Group cohort | Mastermind | Self-paced + community
        price_point: integer
        payment_options: [string]
        program_length_months: integer
        positioning: string
        core_promise: string
        unique_mechanism: string
        curriculum:
          - module_number: integer
            module_name: string
            module_objective: string
            deliverables: [string]
        cadence:
          mentorship_calls_per_month: integer
          group_calls_per_month: integer
          office_hours: string
          community_engagement: string
        success_metrics:               # What "success" looks like for a graduate
          - metric_name: string
            target_value: string
        guarantee: string
        target_avatar: string

  offer_ladder:
    free: [string]                     # Lead magnets, content, freebies
    low_ticket: [string]               # $7-$97 tripwires (often empty for this archetype)
    mid_ticket: [string]               # $300-$2K (mid-ticket education)
    high_ticket: [string]              # $5K-$25K (program / DFY retainer)
    custom: [string]                   # Enterprise / white-label / equity-backed

  pricing_psychology:
    pricing_strategy: string           # Identity-aligned / value-based / outcome-anchored / cohort-anchored
    payment_options: [string]
    comparison_anchoring: string       # What it's anchored against (an in-house hire, last failed agency, etc)
    value_justification: string

  economics_validation:
    ltv_per_dfy_client: integer
    cac_per_dfy_client: integer
    ltv_cac_ratio_dfy: float           # Target ≥ 3.0
    gross_margin_pct_dfy: float        # Target ≥ 60% for service; ≥ 80% for program
    ltv_per_program_student: integer
    cac_per_program_student: integer
    breakeven_per_cohort_months: float
```

---

## Completeness Scoring

Each profile is scored 0-100:

- 0% = empty
- 25% = skeleton (basic_info populated)
- 50% = foundation (core fields populated)
- 75% = solid (nested fields populated with verbatim voice-of-customer evidence)
- 90% = optimized (every field, verbatim language, proven mechanisms tagged with evidence)
- 100% = elite (profile output is blind-test indistinguishable from operator's own thinking)

The weighted sum = overall Context Quality Score.

**Recursive scoring:** for any per-client subprofile (`brand_voice.client_voices[*]`, `ideal_customer_profile.client_icps[*]`, `content_strategy.client_content[*]`), the same scoring applies. A skill that produces a client-bound artifact reads the *client subprofile* score, not the agency-level score.

## How Skills Use Profiles

Every skill declares required profiles + minimum completeness in its `SKILL.md` frontmatter:

```yaml
required_profiles:
  ideal_customer_profile.agency_icp: 70
  product_strategy.service_tier: 70
  brand_voice.agency_voice: 60
```

For client-bound skills, the requirement targets the client subprofile:

```yaml
required_profiles:
  ideal_customer_profile.client_icps[client_slug]: 75
  brand_voice.client_voices[client_slug]: 80
  content_strategy.client_content[client_slug]: 60
```

If any required profile is below threshold, the skill refuses to execute and **interview-mode** activates to fill the gap.

## How the Boot Sequence Reads ENCODING.md

1. Boot reads `SYSTEM.md`
2. Boot reads `INVARIANTS.md`
3. Boot reads `ENCODING.md` (this file) to know the schema
4. Boot reads `company.yaml` to load actual values
5. Boot computes profile completeness scores (agency-level + per-client)
6. Boot determines which skills are unlocked
7. Agent operates within those unlocks

## Variable Substitution

Throughout skill bodies, reference profiles with `{{path.to.field}}` notation:

- `{{business_context.basic_info.agency_name}}`
- `{{brand_voice.agency_voice.phrases_to_use}}`
- `{{brand_voice.client_voices[acme].phrases_to_use}}` (recursive — `acme` is the client_slug)
- `{{ideal_customer_profile.agency_icp.voice_of_customer.pain_language_patterns}}`
- `{{product_strategy.service_tier.offers[0].deliverables_per_month}}`

Skills hydrate these at execution time from `company.yaml`.

## Authoritative Schema

This file is the **authoritative schema**. `company.yaml` must conform. If an operator has data outside this schema, extend this file first, then add to `company.yaml`. Never store schema-violating data in `company.yaml`.

---

*Version: 1.0.0 — 2026-05-03. Based on the 6-Profile Operator Context Architecture + Recursive ICP Principle + Signal Theory.*

*LinkedIn Marketing Agency — a Heuresis workspace template. heuresis.ai*
