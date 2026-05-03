# Changelog

All notable changes to this workspace template are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.0.0] — 2026-05-03

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
