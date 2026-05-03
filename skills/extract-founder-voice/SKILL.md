---
name: extract-founder-voice
slug: /extract-founder-voice
agent: voice-extractor
department: foundations
output_format: voice-profile-yaml
required_profiles:
  brand_voice: 0          # zero floor — this skill BUILDS the profile
allowed_scopes:
  - agency-voice          # extract operator's own voice
  - client-voice          # extract a client's voice (recursive)
output_path:
  agency-voice: company.yaml::brand_voice.agency_voice
  client-voice: company.yaml::brand_voice.client_voices[{client_slug}]
---

# /extract-founder-voice — Voice Profile Extraction

> **Purpose.** Build an elite-tier voice profile (≥80% completeness) for either the agency operator or a specific client. The voice profile is the load-bearing input for every downstream content artifact. Wrong voice = client churn. Voice extraction is the single highest-leverage skill in the workspace.
>
> **Bar.** A post written from this profile must pass the Blind Output Test: shown alongside the operator's (or client's) actual posts, three readers cannot identify which post is AI-generated more than chance.

## When to invoke

- New operator onboarding to the workspace (extract `agency-voice`)
- New client signed (extract `client-voice` for that client_slug)
- Voice-drift detector flags a client subprofile (re-extract with focus on drifted dimensions)
- Operator (or client) has notably evolved their voice (quarterly cadence)

## Required inputs

The skill needs at minimum:
1. **20-50 of the subject's actual published posts** (LinkedIn primary; X/blog/YouTube transcripts secondary). Most-engaged 20 + most-recent 20 + most-rejected/least-engaged 10.
2. **3-5 transcripts of the subject speaking unguarded**: sales calls, podcast appearances, internal Loom recordings, voice notes, DM voice replies.
3. **The subject available for a 60-90 minute interview** (sync or async).
4. **For client-voice extraction:** the slug under which this voice will be filed (`client_slug`).

If any of these are missing, surface what's missing and offer two paths: (a) operator collects and re-runs, (b) skill produces a v0.5 draft profile from what's available, marked for upgrade.

## The extraction sequence (90 minutes)

Run in this order. Skipping or reordering degrades the output.

### Step 1 — Corpus ingestion (10 min)

Read the 20-50 posts. Read the transcripts. Build three working lists:

- **Repeated phrases** — phrases that appear 3+ times across the corpus
- **Idiosyncratic constructions** — sentence structures that don't appear in generic LinkedIn writing
- **Forbidden patterns** — what the corpus *never* does (no emoji walls, no "🚀 Let's dive in," no "happy to share," etc.)

Output Step 1 as an internal scratchpad — not yet structured.

### Step 2 — The 9-question voice interview (45 min)

Ask the subject these nine questions, in order. Record verbatim answers.

1. **"What do you actually believe about {their topic} that the market disagrees with?"** — surfaces contrarian beliefs (Profile 2 → `phrases_to_use`, `core_values`)
2. **"What's a sentence you would never write, that other LinkedIn writers in your space use constantly?"** — surfaces `phrases_to_avoid`
3. **"When you're explaining your work to a smart friend at dinner, what's the one analogy or example you always reach for?"** — surfaces signature mechanism + story
4. **"Tell me about a moment in the last 6 months when you almost lost a client (or a deal). What happened?"** — surfaces voice under stress + story bank
5. **"What's a piece of advice you've been giving for years that you think everyone else gets wrong?"** — surfaces unique mechanism + persuasion style
6. **"If you had to write a post that you'd be embarrassed to publish but you knew would go viral, what would it say?"** — surfaces the Pride/edge axis they suppress + the contrarian tension
7. **"Describe your ideal reader. Not your ICP — the actual person you want reading every post."** — sharpens voice calibration target
8. **"What's a phrase your team or your friends tease you for using too much?"** — surfaces `phrases_to_use` they don't notice
9. **"If a ghostwriter wrote 5 posts in your name and you saw them in your feed, what's the one thing that would make you say 'this isn't me'?"** — surfaces the highest-priority `phrases_to_avoid` and tone violations

### Step 3 — Pattern crystallization (15 min)

Cross-reference Step 1 (corpus) with Step 2 (interview). Three outputs:

**A. Communication style classification.** Pick one: `direct` / `consultative` / `contrarian` / `technical` / `storytelling`. The default for this workspace's archetype is `contrarian-direct` — modify only if the corpus + interview clearly point elsewhere.

**B. Tone framework (two-layer).** Primary tone (single word) + secondary tone (modifying word). Examples: `blunt-warm`, `analytical-irreverent`, `confident-self-deprecating`. The two layers prevent flat one-tone output.

**C. Persuasion style.** Pick one: `authority-by-proof` / `story-first` / `mechanism-first` / `contrarian-provocation`. This determines the structural bias of every post written from this profile.

### Step 4 — Story bank seeding (10 min)

From the interview answers (especially Q3, Q4, Q5), extract 5-12 stories. For each:

```yaml
- story_slug: short-descriptor
  story_type: Origin | Transformation | Conflict | Revelation | Anti-Hero
  emotional_arc: setup → tension → resolution → lesson
  key_lessons: [the 1-3 takeaways the story always points to]
  proof_anchors: [the specific numbers, names-of-things, dates that make it credible]
```

These become the source pool every post-pillar can reach into.

### Step 5 — Output the profile (10 min)

Hydrate the YAML schema (`brand_voice.agency_voice` for agency, `brand_voice.client_voices[slug]` for client):

```yaml
{scope_path}:
  communication_style: ""                        # from Step 3A
  tone_framework:
    primary: ""                                  # from Step 3B
    secondary: ""                                # from Step 3B
  personality_traits: []                         # 3-5, evidence-anchored to interview answers
  language_patterns: []                          # 5-10 cadence patterns from corpus + interview
  phrases_to_use: []                             # 10-25 verbatim phrases (ranked by frequency × distinctiveness)
  phrases_to_avoid: []                           # 10-25 — Q9 answers + corpus-confirmed never-uses
  persuasion_style: ""                           # from Step 3C
  authority_positioning: ""                      # category-king | challenger | insider | outsider-with-edge
  story_bank: []                                 # from Step 4
  voice_drift_threshold: 0.82                    # default — adjust later based on rejection signal
```

Mark profile completeness honestly:
- ≥80% if all fields populated, story bank ≥7, phrases_to_use ≥15
- 60-79% if structural fields populated but story bank or phrases_to_use thin
- <60% if interview cut short or corpus thin → flag for re-extraction

## Validation gate (mandatory before declaring done)

Run this check before marking the profile complete:

1. **Pull 5 actual published posts by the subject** (not used in extraction).
2. **Generate 5 posts from the new profile** on similar topics.
3. **Show the 10 posts to 3 readers** who know the subject's work. Ask: "rate each from 1 (definitely not them) to 5 (definitely them)."
4. **Pass criterion:** AI-generated posts average ≥3.5 across all readers AND no AI post scores ≤2 from any reader.

If pass: profile shipped, mark `voice_extraction_status: extracted-validated`.
If fail: surface which posts failed and why → re-run Step 2 questions {related to the failure dimension} → iterate.

## Output format

```yaml
---
artifact_type: voice-profile
scope: agency-voice | client-voice
client_slug: null | "<slug>"
profile_completeness: 0-100
extraction_date: YYYY-MM-DD
validation_gate: passed | failed | not-yet-run
profiles_used: [brand_voice]
frameworks_used: [voice-extraction-9q, story-bank-seeding]
confidence: HIGH | MEDIUM | LOW
gap_flags: [list of fields needing operator follow-up]
---

{the populated YAML profile from Step 5}

## Validation gate result
{pass/fail summary, individual reader scores, posts that failed if any}

## Next actions
{what the operator should do next — schedule re-extraction, ship to ghostwriter, monitor drift, etc.}
```

## Failure modes and guards

- **Generic-LinkedIn-voice contamination.** Symptom: profile reads as "professional, authoritative, direct" — could be anyone. Guard: if Step 2 answers are short or hedge-laden, re-do the interview with a different framing — "not what you'd say in a job interview, what you'd say in a Slack rant to your co-founder."
- **Confusing the operator's voice with the operator's brand voice.** The operator's brand voice is what they publish; the profile we want is what they actually say. If publishable-voice and actual-voice differ, the profile follows actual-voice and the publishable layer is a `style_translation` field added separately.
- **Skipping the validation gate.** Sacred. Never skip. If the gate fails twice, escalate to the operator with a brief and pause ghostwriting until resolved.
- **Voice extracted from a stale corpus.** If the most-recent posts are >6 months old, the voice will calibrate to past-operator, not current-operator. Require last-90-days posts in the corpus.

## Cross-references

- `reference/frameworks/foundations/voice-extraction-9q.md` — the canonical interview script with deeper rationale per question
- `reference/frameworks/foundations/story-bank-seeding.md` — story-bank protocol
- `reference/templates/voice-extraction-interview.md` — operator-facing interview template
- `INVARIANTS.md` N-1, N-2, N-6, A-9, A-12 — voice rules
- `skills/voice-drift-detector/SKILL.md` — the weekly drift check this profile feeds

## Examples

See `examples/` — three anonymized outputs:
- `example-agency-voice-contrarian-direct.md` — operator who runs an outbound-led agency, contrarian-direct style
- `example-client-voice-saas-founder-technical.md` — B2B SaaS founder client, technical-with-edge style
- `example-client-voice-coach-storytelling.md` — executive coach client, storytelling-warm style

---

*Skill version 1.0.0 — 2026-05-03*
