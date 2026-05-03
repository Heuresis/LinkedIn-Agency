# Skill Index

> Registry of every skill in this workspace. Each skill is a folder containing `SKILL.md` (the instruction set), `examples/` (golden outputs), and optionally `adapters/{runtime}.md` (runtime-specific bindings).

## Wedge — Cycle 1 (the first 5 to run)

These five define the workspace. Every operator runs these in order before anything downstream.

| # | Skill | Department | Agent | Required Profiles |
|---|---|---|---|---|
| 1 | `/extract-founder-voice` | Foundations | voice-extractor | brand_voice (for the agency, OR for a client by slug) |
| 2 | `/build-icp` | Foundations | icp-builder | ideal_customer_profile (recursive: agency or client) |
| 3 | `/design-retainer-offer` | Foundations | offer-architect | product_strategy.service_tier ≥ 30, business_context ≥ 50, ideal_customer_profile.agency_icp ≥ 70 |
| 4 | `/build-content-engine` | Marketing | content-strategist | content_strategy (recursive), brand_voice ≥ 60, ideal_customer_profile ≥ 70 |
| 5 | `/build-outbound-engine` | Acquisition | dm-strategist | ideal_customer_profile ≥ 75, product_strategy.service_tier ≥ 60, brand_voice ≥ 60 |

## Foundations (4)

| Skill | Agent | Status |
|---|---|---|
| `/extract-founder-voice` | voice-extractor | ✅ Cycle 1 |
| `/build-icp` | icp-builder | ✅ Cycle 1 |
| `/build-positioning` | foundations-head | Cycle 2 |
| `/define-mission` | foundations-head | Cycle 2 |

## Offers & Programs (5)

| Skill | Agent | Status |
|---|---|---|
| `/design-retainer-offer` | offer-architect | ✅ Cycle 1 |
| `/design-program-offer` | offer-architect | Cycle 2 |
| `/build-value-stack` | offer-architect | Cycle 2 |
| `/design-guarantee` | offer-architect | Cycle 2 |
| `/build-offer-ladder` | offer-architect | Cycle 2 |

## Marketing — Content (8)

| Skill | Agent | Status |
|---|---|---|
| `/build-content-engine` | content-strategist | ✅ Cycle 1 |
| `/build-hook-library` | hook-writer | Cycle 2 |
| `/build-posting-calendar` | content-strategist | Cycle 2 |
| `/write-linkedin-post` | linkedin-ghostwriter | Cycle 2 |
| `/ghostwrite-client-post` | linkedin-ghostwriter | Cycle 2 |
| `/repurpose-to-x` | content-strategist | Cycle 3 |
| `/repurpose-to-ig` | content-strategist | Cycle 3 |
| `/score-hook` | hook-writer | Cycle 3 |

## Acquisition — Sales (8)

| Skill | Agent | Status |
|---|---|---|
| `/build-outbound-engine` | dm-strategist | ✅ Cycle 1 |
| `/write-dm-sequence` | dm-strategist | Cycle 2 |
| `/classify-dm-intent` | dm-strategist | Cycle 2 |
| `/build-qualification-thread` | dm-strategist | Cycle 2 |
| `/source-lead-list` | lead-list-builder | Cycle 2 |
| `/build-call-prep` | call-prep-specialist | Cycle 2 |
| `/build-application` | acquisition-head | Cycle 2 |
| `/triage-application` | acquisition-head | Cycle 3 |

## Fulfillment (7)

| Skill | Agent | Status |
|---|---|---|
| `/onboard-client` | account-manager | Cycle 2 |
| `/voice-drift-detector` | account-manager | Cycle 2 |
| `/post-approval-tracker` | account-manager | Cycle 2 |
| `/client-content-sourcing-pulse` | account-manager | Cycle 3 |
| `/build-client-report` | client-reporter | Cycle 2 |
| `/build-attribution-trace` | client-reporter | Cycle 3 |
| `/run-client-dm-ops` | dm-strategist | Cycle 3 |

## Program (5)

| Skill | Agent | Status |
|---|---|---|
| `/onboard-program-student` | program-mentor | Cycle 3 |
| `/program-cohort-pulse` | program-head | Cycle 3 |
| `/build-curriculum` | program-mentor | Cycle 3 |
| `/build-first-win-trigger` | program-mentor | Cycle 3 |
| `/build-retention-pulse` | program-head | Cycle 3 |

## Cross-cutting (3)

| Skill | Agent | Status |
|---|---|---|
| `/pipeline-pulse` | agency-director | Cycle 2 |
| `/strategic-advisory` | agency-director | Cycle 2 |
| `/diagnose-bottleneck` | agency-director | Cycle 2 |

**Total: 40 skills planned** (5 wedge + 35 expansion)

## Skill file structure

Every skill folder contains:

```
skills/{skill-slug}/
├── SKILL.md                ← instruction set (this is what the agent reads)
├── examples/               ← 3-5 golden outputs (anonymized)
│   ├── example-1.md
│   ├── example-2.md
│   └── example-3.md
└── adapters/               ← runtime-specific implementations (optional)
    ├── claude.md
    ├── gpt.md
    └── http-openapi.md
```

`SKILL.md` frontmatter declares:
```yaml
---
name: extract-founder-voice
slug: /extract-founder-voice
agent: voice-extractor
department: foundations
output_format: voice-profile-yaml
required_profiles:
  brand_voice: 0   # zero floor — this skill BUILDS the profile
allowed_scopes:
  - agency-voice
  - client-voice
---
```

## Boot integration

When a user invokes a skill, the runtime reads:
1. `SYSTEM.md` (identity)
2. `INVARIANTS.md` (sacred rules)
3. `ENCODING.md` (schema)
4. `company.yaml` (values)
5. `agents/{agent-slug}.md` (persona)
6. `skills/{skill-slug}/SKILL.md` (instructions)
7. Reference files cited by the skill

Then computes profile completeness, gate-checks, and either executes or activates interview-mode to fill gaps.
