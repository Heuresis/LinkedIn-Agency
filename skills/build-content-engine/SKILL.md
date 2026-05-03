---
name: build-content-engine
slug: /build-content-engine
agent: content-strategist
department: marketing
output_format: content-engine-spec
required_profiles:
  brand_voice: 60                      # recursive: agency-voice or client-voice
  ideal_customer_profile: 70           # recursive: agency-icp or client-icp
  product_strategy.service_tier: 50    # need to know what we're funneling toward
allowed_scopes:
  - agency-content
  - client-content
output_path:
  agency-content: company.yaml::content_strategy.agency_content
  client-content: company.yaml::content_strategy.client_content[{client_slug}]
---

# /build-content-engine — Content Engine Specification

> **Purpose.** Build the content engine for a single subject (the agency operator OR a specific client). Output is a complete spec: pillars, post types, hook library, posting cadence, virality engine target, content-to-conversion bridge, batching protocol, and the editorial calendar template. Every downstream `/write-linkedin-post` and `/ghostwrite-client-post` consumes this spec.
>
> **Bar.** A ghostwriter handed this spec must be able to produce a publish-ready post in <30 minutes that the operator (or client) ships without rewrite. If they can't, the spec is too thin.

## When to invoke

- New operator onboarding (build for `agency-content`)
- New client signed (build `client-content` for that client_slug)
- Content engagement plateau ≥3 months (rebuild — pillars or hook taxonomy stale)
- New offer launching (rebuild — content-to-conversion bridge needs realignment)
- Vertical pivot (rebuild)

## Required inputs

1. Voice profile (≥60% — from `/extract-founder-voice`)
2. ICP profile (≥70% — from `/build-icp`)
3. Service tier offer (≥50% — from `/design-retainer-offer`) — the engine funnels toward an offer
4. **30-50 of the subject's past posts** with engagement data (impressions, comments, profile-clicks, calls-booked-from-post if tracked)
5. **5-10 of the subject's top-performing competitors' posts** (for what *not* to copy + for vertical-specific format expectations)
6. Subject available for a 60-90 minute build session

## The build sequence

### Step 1 — Pillar architecture (20 min)

A content engine runs on **3-5 pillars**. Fewer than 3 = monotonous. More than 5 = unfocused. Each pillar serves one of these objectives:

| Pillar Type | Objective | Reader takeaway |
|---|---|---|
| **Authority** | Demonstrate expertise via specific mechanism / framework / contrarian take | "This person knows things I don't" |
| **Proof** | Demonstrate results via case study / before-after / receipt | "This person has done what I want to do" |
| **Personality** | Demonstrate values + identity via story / opinion / behind-the-scenes | "I want to work with this person" |
| **Offer-bridge** | Connect content to the offer via call-out / lead magnet / CTA-engineered post | "I should take the next step" |
| **Pier** | Engage the algorithm via lurking-leverage / engagement-bait / contrarian provocation | (Reader doesn't notice — algorithm does) |

The pillar mix for this archetype's agency-content typically lands on:
- 25% Authority
- 25% Proof
- 20% Personality
- 20% Offer-bridge
- 10% Pier

For client-content, the mix shifts based on the client's solution-awareness target:
- Solution-aware clients: heavier Proof + Authority
- Problem-aware clients: heavier Personality + Pier (build awareness first)
- Most-aware clients: heavier Offer-bridge

For each pillar, define:

```yaml
- pillar_name: ""                        # short, distinctive
  pillar_objective: ""                   # which of the 5 types above
  topic_clusters: []                     # 4-7 topic clusters this pillar pulls from
  conversion_bridge: ""                  # how this pillar moves the reader toward the offer
  evidence_of_resonance: []              # past posts in this pillar that hit (with engagement data)
```

### Step 2 — Post type library (15 min)

For each pillar, list the post *forms* it uses. This archetype's library:

| Post Type | Pillar fit | Structure |
|---|---|---|
| **Authority post** | Authority | Hook → mechanism breakdown → 3-5 specific points → contrarian close |
| **Personal-to-business** | Personality + Authority | Hook → personal moment → bridge to professional lesson → takeaway |
| **Pier post** | Pier | Hook → controversial thesis → list of evidence → invite-the-fight close |
| **Day-in-life** | Personality | Hook → time-stamped narrative → contradiction → lesson |
| **Failure-lesson** | Proof + Personality | Hook → failure with specific stakes → what I changed → result |
| **Listicle** | Authority | Hook → "X things I learned about Y" → numbered list → close |
| **Hook-loop-CTA** | Offer-bridge | Hook → loop (problem-then-pause) → resolution → soft CTA |
| **Thought-proof-CTA** | Proof + Offer-bridge | Hook → thought → proof anchor → CTA to lead magnet |
| **Contrarian reframe** | Authority + Pier | "Everyone says X. They're wrong. Here's why" → reframe → mechanism |
| **Case-study post** | Proof | "Client did X. Here's what we changed" → before/after → mechanism |

For each post type, point to `reference/swipe-file/posts-linkedin/{post-type}/` for annotated golden examples.

### Step 3 — Hook library (15 min)

Hooks are the highest-leverage 7-12 words on LinkedIn. Build the per-subject hook library:

**Proven hooks** — pull from past posts with top-decile engagement. Verbatim. List 15-25.

**Hook formulas the subject is allowed to use** — pull from `reference/frameworks/content/hook-anatomy.md`. Examples:
- The contrarian-reframe hook: "Most {role}s do X. The ones who win do Y."
- The specific-stakes hook: "I lost {specific dollar amount} in {specific time} because of {specific mistake}."
- The named-mechanism hook: "The {named mechanism} that {specific outcome}."
- The before-after hook: "{Time period} ago, {bad state}. Today, {good state}. Here's the thing that changed."
- The list-promise hook: "{Number} {things} I learned from {credible source/experience}."
- The pattern-break hook: "Stop doing {common practice}. Do {contrarian practice} instead."

**Banned hooks** — what's dead this quarter on LinkedIn:
- "🚀 Excited to share..."
- "In this post, I'll cover..."
- "Did you know that..."
- "Three things I've learned..."
- "Here's the truth about..."
- "[Brand] is hiring!" as a content post
- Any hook starting with an emoji

The banned list refreshes quarterly per `reference/platforms/linkedin/algorithm-notes.md`.

### Step 4 — Posting cadence (10 min)

Three knobs:

**Frequency.** Default for this archetype:
- Agency-operator account: **5x/week** (Mon-Fri)
- Client account: **3-5x/week** depending on tier
- Plateau-recovery cadence: **7x/week for 30 days** (saturation push to escape an algorithm pocket)

**Time blocks.** Default for B2B audiences:
- Tue/Wed/Thu 8:30am-10:30am operator-local-time (best engagement windows for B2B in 2026)
- Avoid Mon AM (low attention), Fri PM (low attention), weekends (low B2B engagement)
- Override per client based on their ICP's actual engagement data

**Batching protocol.** Default: **Sunday-batch-week** — write 5 posts Sunday afternoon, schedule via {tool}, publish on cadence. Alternative: **Daily-spike** — write fresh each morning (only sustainable for high-energy operators with clear material). Default to Sunday-batch unless operator explicitly prefers daily.

### Step 5 — Virality engine integration (10 min)

The virality engine (`reference/frameworks/content/virality-engine.md`) defines what makes a post hit 100K+ impressions. Each post engine integrates one or more of:

- **The pier mechanism** — leverage another high-engagement post (yours or someone else's) by replying first with the most insightful comment, then publishing your own post that references the conversation
- **The first-12-impression bet** — first 12 reads decide whether the algorithm shows it to 12K. Hook + first-line cohesion is the leverage point
- **The dwell-time engineering** — line breaks, single-sentence paragraphs, narrative loops keep readers for 8+ seconds
- **The comment-reply harvest** — replying to every comment in the first 60 minutes signals algorithm relevance
- **The cross-post compound** — same idea, three formats (text, carousel, video) — each post becomes the fuel for the next

For each subject, set a `target_metric`:
- New account / under 5K followers: 5K-15K impressions/post baseline, 50K+ on best
- 5K-25K followers: 15K-40K baseline, 100K+ on best
- 25K-100K followers: 40K-100K baseline, 250K+ on best
- 100K+ followers: 100K+ baseline, 1M+ on best

### Step 6 — Content-to-conversion bridge (10 min)

For every pillar, name the bridge that moves a reader from content into the offer. Examples:

- **Authority post → discovery call:** "Read this in the post, want to apply it to your business? Comment 'X' for the application link" → DM auto-reply with link → application → call
- **Proof post → lead magnet:** "Want the playbook from this case study? Comment 'PLAYBOOK' for the doc" → DM auto-reply with PDF → email capture → nurture sequence
- **Personality post → DM-to-call:** "Replying to my DMs today. Want to talk about your LinkedIn growth? Send a note." → DM thread → qualification → call
- **Offer-bridge post → direct CTA:** "I have 3 spots open this month for the program. Reply 'INFO' for details." → direct DM → application → call

Every pillar must have at least one named bridge. Pillars without bridges produce engagement without revenue.

### Step 7 — Editorial calendar template (5 min)

The output spec includes a 4-week rolling editorial calendar template:

```yaml
calendar_4_week_template:
  week_1:
    monday: { pillar, post_type, topic_cluster, hook_formula, bridge_to_offer }
    tuesday: { ... }
    wednesday: { ... }
    thursday: { ... }
    friday: { ... }
  week_2: { ... }
  week_3: { ... }
  week_4: { ... }
```

Default rotation pattern (preserves variety while honoring pillar weights):
- Mon: Personality / Pier
- Tue: Authority
- Wed: Proof
- Thu: Authority / Offer-bridge
- Fri: Personality / Pier

### Step 8 — Output the engine spec

Hydrate `content_strategy.{scope}` in `company.yaml`:

```yaml
{scope_path}:
  content_pillars: []                          # from Step 1
  post_types: []                               # from Step 2 with examples_path links
  hook_library:
    hook_taxonomy_path: "reference/frameworks/content/hook-anatomy.md"
    proven_hooks: []                           # from Step 3
    banned_hooks: []                           # from Step 3
  posting_cadence:
    frequency: ""
    time_blocks: []
    batching_protocol: ""
  virality_engine:
    virality_engine_path: "reference/frameworks/content/virality-engine.md"
    target_metric: ""                          # impressions/post target
    viral_post_anatomy: ""                     # subject-specific anatomy notes
  content_to_conversion_bridge: []             # from Step 6 — named bridges per pillar
  calendar_4_week_template: {}                 # from Step 7
```

For client-content, also set:
```yaml
ghostwriter_assigned: ""                       # internal role
voice_drift_threshold: 0.82                    # mirrors voice profile threshold
content_sourcing_protocol: ""                  # how raw material gets from client → ghostwriter
```

## Validation gate (mandatory before declaring done)

Two checks:

1. **The 5-post-from-spec test.** Generate 5 posts from the spec — one per pillar. Score each on a 1-5 rubric: voice match, hook strength, mechanism specificity, bridge clarity, banned-vocabulary clean. Pass: average ≥4.0, no post below 3.0.

2. **The ghostwriter handoff test.** Hand the spec + the test posts to a ghostwriter who has not seen the subject's prior work. Ask: can you produce 3 publish-ready posts in 90 minutes using only this spec? Pass: yes, and operator (or client) ships them with <10% rewrite.

If either fails, the spec is too thin. Re-run Step 1 (pillar specificity) and Step 3 (hook library depth).

## Output format

```yaml
---
artifact_type: content-engine-spec
scope: agency-content | client-content
client_slug: null | "<slug>"
profile_completeness: 0-100
build_date: YYYY-MM-DD
validation_gate: passed | failed | not-yet-run
profiles_used: [content_strategy, brand_voice, ideal_customer_profile, product_strategy]
frameworks_used: [pillar-architecture, hook-anatomy, virality-engine]
confidence: HIGH | MEDIUM | LOW
---

{the populated content_strategy YAML from Step 8}

## Client-facing / Operator-facing brief
{a 1-page narrative summary of the engine — pillar logic, cadence, what to expect}

## Validation gate result
{the 5-post test scores, the ghostwriter handoff result}

## Calendar — next 4 weeks (filled)
{the rotation template, populated with specific topics for the next 20 posts}

## Next actions
{usually: assign ghostwriter, schedule first batch, set virality-engine baseline measurement}
```

## Failure modes and guards

- **Pillar overlap.** If two pillars have overlapping topic_clusters and overlapping conversion_bridges, they're one pillar with two names. Collapse.
- **Hook library staleness.** Hooks decay. The banned list updates quarterly. Skill `/audit-hook-library-decay` runs every 90 days.
- **Bridge missing on Authority pillar.** Most operators forget to put a bridge on Authority posts because they "feel salesy." Authority posts without bridges produce engagement without revenue. Always include at least a soft bridge.
- **Calendar drift.** The 4-week template must be re-populated weekly with specific topics. Empty calendar = ghostwriter improvisation = voice drift.
- **Skipping the ghostwriter handoff test.** Sacred for client-content. If the spec doesn't survive a fresh ghostwriter, it'll fail when the inevitable team change happens.

## Cross-references

- `reference/frameworks/content/pillar-architecture.md` — pillar-design protocol
- `reference/frameworks/content/hook-anatomy.md` — full hook taxonomy
- `reference/frameworks/content/virality-engine.md` — virality-engine framework
- `reference/swipe-file/posts-linkedin/` — annotated examples per post type
- `reference/platforms/linkedin/algorithm-notes.md` — refreshed quarterly
- `INVARIANTS.md` N-1, N-2, N-6, A-9 — voice and ghostwriting rules
- `skills/write-linkedin-post/SKILL.md` — consumes this spec for agency posts
- `skills/ghostwrite-client-post/SKILL.md` — consumes this spec for client posts

## Examples

See `examples/`:
- `example-agency-content-engine-hybrid-archetype.md` — engine for the operator's own LinkedIn
- `example-client-content-engine-saas-founder.md` — engine for a B2B SaaS founder client
- `example-client-content-engine-services-founder.md` — engine for a B2B services founder client

---

*Skill version 1.0.0 — 2026-05-03*
