# Changelog

All notable changes to this workspace template are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.1.0] — 2026-06-12

### Changed — relicensed

- `LICENSE` — replaced MIT with the **Heuresis Source License 1.0**: free for solo developers, passion projects, testing, and education; company use, client deployment, or selling in any form requires a commercial license via [Syed@heuresis.ai](mailto:Syed@heuresis.ai); all marketing must link back to this repository, the sole distribution point; enforced with DMCA takedowns and platform copyright complaints.
- `README.md` — license badge, ownership row, and License section updated to match.
- `scripts/lib/banner.sh` — boot banner license line updated.

## [1.0.0] — 2026-05-03

### Added — full library expansion

**Skills (43 total, up from 5).**
- Foundations: `/build-positioning`, `/define-mission` (added)
- Offers: `/design-program-offer`, `/build-value-stack`, `/design-guarantee`, `/build-offer-ladder` (added)
- Marketing: `/build-hook-library`, `/build-posting-calendar`, `/write-linkedin-post`, `/ghostwrite-client-post`, `/repurpose-to-x`, `/repurpose-to-ig`, `/score-hook` (added)
- Acquisition: `/write-dm-sequence`, `/classify-dm-intent`, `/build-qualification-thread`, `/source-lead-list`, `/score-lead-intent`, `/build-call-prep`, `/summarize-discovery-call`, `/build-application`, `/triage-application`, `/no-show-recovery` (added)
- Fulfillment: `/onboard-client`, `/voice-drift-detector`, `/post-approval-tracker`, `/client-content-sourcing-pulse`, `/build-client-report`, `/build-attribution-trace`, `/run-client-dm-ops` (added)
- Program: `/onboard-program-student`, `/program-cohort-pulse`, `/build-curriculum`, `/build-first-win-trigger`, `/build-retention-pulse` (added)
- Cross-cutting: `/pipeline-pulse`, `/strategic-advisory`, `/diagnose-bottleneck` (added)

**Agents (18 total, up from 6).** 12 specialist personas added: voice-extractor, icp-builder, offer-architect, content-strategist, hook-writer, linkedin-ghostwriter, dm-strategist, lead-list-builder, call-prep-specialist, account-manager, client-reporter, program-mentor.

**Workflows (5 total, up from 1).** 4 FSMs added: marketing, fulfillment, program, foundations. Each with mermaid state diagram, state definitions table, trigger map, owner-by-state escalation, health metrics.

**Frameworks (19 total, up from 4).** Added: cold/warm/liked-post/comment-magnet sequence architectures, qualification-thread-design, booking-gate-construction, pillar-architecture, voice-extraction-9q, story-bank-seeding, voice-of-customer-extraction, transformation-hypothesis, value-stack-construction, guarantee-strength-tiers, voice-drift-detection, account-mgmt-protocol.

**Operator archetypes (4 total, up from 1).** Added: outbound-dm-operator, hybrid-full-stack-operator, founder-led-ghostwriter-agency. Each anonymized methodology descriptor with 17-section structure (lineage, thesis, arc, decision tree, content engine, acquisition engine, fulfillment shape, program shape, monetization, risk, adopt criteria, tactical details, cadence, workspace integration).

**Swipe file posts (11 total, up from 4).** 7 annotated real-pattern LinkedIn posts added across pillars: authority/02 stat-bait, authority/03 list-promise, proof/05 narrative case-study, proof/11 pier-mechanism, offer-bridge/07 mechanism-deep-dive, personality/08 personal-story, personality/09 insider-secret. Each with 5-axis hook scoring + virality mechanism + imitation failures + adaptation notes.

**Templates (6 added).** Retainer-proposal, onboarding-doc, monthly-client-report, voice-extraction-interview script, icp-build-interview script, va-daily-dm-playbook.

**Documentation (5 added).** QUICKSTART, ARCHITECTURE, RECURSIVE_CONTEXT, SKILL_AUTHORING, THE_BAR.

**Platform packs (5 added).** linkedin (the substrate), x (cross-post compounding), instagram (tertiary), youtube (long-runway complement), email (offer-side complement). Each with platform mechanics, hook adaptations, format library, cadence, account safety, workspace integration.

### Changed

- README expanded to reflect full library; pitch unchanged
- Cross-references throughout — every skill SKILL.md cites real frameworks (no `(Cycle 2 — TBD)` markers remaining for built work)

### Stats
- 135 markdown files total (up from ~37 at v1.0.0)
- ~50,000 lines of encoded operator intent
- Anonymization sweep clean — only INVARIANTS.md + .github files mention "Co-Authored-By: Claude" / "Generated with Claude" and only in the rule declarations that prohibit them
- Density now matches Growth-Operator-Agency archetype across the load-bearing dimensions (skills + agents + frameworks)

---

## [0.1.0] — 2026-04-22

### Added — initial template ship

**Spine.**
- `SYSTEM.md` — identity boot file with 4-layer cognitive architecture (Identity / Intelligence / Operational / Constraints), boot sequence, core loop, and recursive context primitive
- `ENCODING.md` — 6-profile Operator Context schema (Business / Brand Voice / ICP / Content Strategy / Case Studies / Product Strategy), recursive at agency-level + per-client subprofile-level
- `INVARIANTS.md` — 14 NEVERs + 14 ALWAYSes governing every agent
- `README.md` — workspace overview, two-business-line architecture, five departments, recursive-context primitive, voice-drift detection, and the publish-bar
- `company.yaml` — full schema template the operator fills in (agency-level fields + per-client subprofile stubs)
- `paperclip.manifest.yaml` — cron / webhook / event triggers + agent bindings + quality gates

**Structure.**
- 5-department architecture: Foundations, Marketing (Content), Acquisition (Sales), Fulfillment, Program
- 12-18 agents (1 director + 5 department heads + specialists)
- ~30 wedge skills + 15 expansion skills planned
- Reference library scaffolded: frameworks, operators (internal + external), platforms, playbooks, swipe-file, templates, verticals, knowledge

**Distinctive primitives.**
- Recursive context (agency profile + per-client subprofile, hydrated per-artifact)
- DM Intent Taxonomy (Cold / Warm / Liked-your-post / Comment-for-lead-magnet) with per-class sequences
- Voice-drift detection (weekly cadence per active client, cosine-similarity vs. validated profile)
- Hybrid service-tier + program-tier offer architecture with shared upstream profiles
- Three-Lens Audit (Pipeline → Channel → Layer) before any deliverable
- Blind Output Test ("would a B2B founder pay an agency for this?") gate for client/program-bound artifacts

### Notes

- Template ships with empty `company.yaml` profile fields — operator runs the wedge skills (`/extract-founder-voice`, `/build-icp`, `/design-retainer-offer`, `/build-content-engine`, `/build-outbound-engine`) to populate.
- Anonymization rule applies absolutely: no real operator names, no real client names, no real competitor names anywhere in any pushed artifact. Methodology descriptors only.
- No AI-tool attribution in any pushed artifact.
