# Skill Authoring — Write Your Own Agents and Skills

> **For operators who want to extend the workspace beyond what ships.** Every shipped skill follows the same structure. Once you know the structure, you can add a new one in an afternoon. This doc walks you through it in plain language, end to end.

---

## When you'd write a new skill vs. customize an existing one

Default to customizing before writing new. The shipped skills cover the high-leverage 90% of a B2B LinkedIn agency. Most "I need a new skill" instincts are actually "I need to tune the inputs or output format of an existing one."

| Situation | Do this |
|---|---|
| Different output format from a shipped skill | Edit the Output Format section of the existing `SKILL.md`. Don't fork. |
| Add a step to a shipped skill (e.g., a custom approval flow) | Add a step. Don't fork. |
| Serve a vertical the workspace hasn't encoded yet | Don't write a new skill — add a `reference/verticals/{slug}.md` file. Shipped skills pick it up. |
| Distinct workflow with its own artifact (podcast pitch sequence, event-booth follow-up, partnership outreach loop) | Write a new skill. This doc is for you. |
| Different agent persona for an existing skill | Add an agent file in `agents/` and reference it in the skill's frontmatter. Don't fork the skill. |

The threshold for a new skill is "produces a distinct artifact that no shipped skill produces, and that artifact has its own quality bar." If you cannot name the artifact in one phrase, you do not yet have a skill — you have a workflow that should compose existing skills.

---

## The skill folder structure

Every skill lives in `skills/{skill-slug}/` with this layout:

```
skills/{skill-slug}/
├── SKILL.md                 ← the instruction set the agent reads
├── examples/                ← 3-5 anonymized golden outputs
│   ├── example-1.md
│   ├── example-2.md
│   └── example-3.md
└── adapters/                ← runtime-specific implementations (optional)
    ├── claude.md
    ├── gpt.md
    └── http-openapi.md
```

`SKILL.md` is mandatory. `examples/` is mandatory if the skill produces a creative artifact (a post, a sequence, a proposal); optional for diagnostic skills (`/diagnose-bottleneck`, `/pipeline-pulse`). `adapters/` is optional and only needed if the skill behaves differently on different runtimes — most skills are runtime-agnostic and skip this folder entirely.

---

## The 9 required body sections of every SKILL.md

Every shipped skill body has these nine sections in this order. They are not negotiable. Skipping a section causes downstream skills to break, agents to mis-execute, or the Blind Output Test to flag the artifact.

| # | Section | What it contains |
|---|---|---|
| 1 | **Title + tagline** | `# /skill-name — Short Description`. Then a blockquote with one sentence on purpose and one sentence on the bar (what "good" looks like). |
| 2 | **When to invoke** | The triggers. Cron, event, FSM transition, manual operator request. List every condition under which this skill should run. |
| 3 | **Required inputs** | Numbered list. Profile completeness floors. External data (transcripts, lead lists, post archives). Slugs and parameters. If a required input is missing, the skill knows what to surface. |
| 4 | **The build sequence** | Numbered steps the agent works through. Each step has a time estimate, a clear output, and a transition to the next step. This is the actual instruction set. |
| 5 | **Output format** | The exact YAML/markdown shape the artifact takes. Includes the metadata block (`profiles_used`, `frameworks_used`, `confidence`, etc.) per `INVARIANTS.md` A-2. |
| 6 | **Validation gate** | The check that runs before the artifact is declared done. For client-bound and program-bound work, this includes the Blind Output Test reference. |
| 7 | **Failure modes and guards** | A table of common ways this skill fails + how the workspace catches each one. Read this section before shipping the skill. |
| 8 | **Cross-skill routing** | Upstream (what produces my inputs), Sibling (what runs alongside me), Downstream (what consumes my output). Tells the agent how to compose. |
| 9 | **Cross-references** | File paths to the frameworks, invariants, agents, and other skills that this skill depends on. |

A skill missing section 6 (validation gate) ships without quality control. A skill missing section 4 (build sequence) is a slogan, not a skill. A skill missing section 8 (cross-skill routing) cannot be composed into the workflows that make this workspace compound.

---

## The frontmatter

Every SKILL.md starts with YAML frontmatter. This is what the runtime reads to route, gate, and execute.

```yaml
---
name: skill-name                         # kebab-case, matches folder name
slug: /skill-name                        # the slash-command form
agent: agent-slug                        # the agent persona (must exist in agents/)
department: foundations | marketing | acquisition | fulfillment | program | cross-cutting
output_format: artifact-type-name
required_profiles:
  profile.path: 70                       # minimum completeness score (0-100)
  another.path: 60
allowed_scopes:
  - agency-voice
  - client-voice
output_path:
  agency-voice: company.yaml::brand_voice.agency_voice
  client-voice: outputs/{date}/{slug}/{client_slug}/{artifact}.md
---
```

Field-by-field:

- **`name`** — kebab-case identifier. Matches the folder name. No spaces, no underscores, no capitals.
- **`slug`** — the slash-command form. Operators type this; runtimes route on it.
- **`agent`** — agent persona that executes this skill. Must reference an existing `agents/{agent-slug}.md` file.
- **`department`** — one of foundations / marketing / acquisition / fulfillment / program / cross-cutting. Used for routing in `agents/agency-director.md`.
- **`output_format`** — the artifact-type label. Downstream skills key off this when consuming.
- **`required_profiles`** — the gate. Map of profile path to minimum completeness score. Runtime checks every entry before execution. Below threshold = halt + interview-mode.
- **`allowed_scopes`** — which scopes this skill accepts (agency-only, client-only, or both). Inherently agency-only skills should not list client scopes; inherently client-only skills should not list agency scopes.
- **`output_path`** — where the artifact lands. Different scopes can land in different paths. Use template variables (`{date}`, `{slug}`, `{client_slug}`).

---

## The recursive-scope decision

Before writing the skill body, decide its scope shape. This is the most consequential design decision in the skill.

| Your skill is | Set `allowed_scopes` to | Examples |
|---|---|---|
| Agency-only | `[agency-voice]` (agency-side scope name) | `/design-retainer-offer`, `/build-positioning`, `/define-mission` |
| Client-only | `[client-voice]` (or `[single-client]`); require `client_slug` | `/ghostwrite-client-post`, `/voice-drift-detector`, `/build-client-report` |
| Both | `[agency-X, client-X]` | `/extract-founder-voice`, `/build-icp`, `/build-content-engine` |

Decision rule: "Is the artifact about the agency, or about one specific client?" If both, ask "Would the same prompt produce different correct answers for agency vs. client?" Yes → recursive. No → pick one.

Read `docs/RECURSIVE_CONTEXT.md` before finalizing. Skills that get scope wrong leak voices across drawers — the failure mode the whole workspace exists to prevent.

---

## The required_profiles + thresholds

Every skill has a context bar. Below the bar, work cannot pass the Blind Output Test. Above the bar, it can. To set thresholds correctly:

1. **Read `spec/CONTEXT-THRESHOLDS.md`.** The master table is calibrated against false-positive vs. false-negative cost asymmetry. Follow the same logic.
2. **Identify the load-bearing profile.** What single profile, if thin, breaks the artifact? Ghostwriting → voice. Lead lists → ICP. Proposals → product strategy. Set this profile high (75-85).
3. **Identify supporting profiles.** What profiles tune but don't make-or-break the artifact? Set these in 50-70.
4. **For client-bound skills, target the client subprofile.** Path is `brand_voice.client_voices[{client_slug}]`, not `brand_voice.agency_voice`. The recursive scope rule from `docs/RECURSIVE_CONTEXT.md`.
5. **Default high.** False positive: operator does an interview they didn't strictly need (hours). False negative: workspace ships a generic-feeling artifact and the client churns (months of revenue). Err high. Operator can `--override` when confident.

Example from `/ghostwrite-client-post`:

```yaml
required_profiles:
  brand_voice.client_voices[{client_slug}]: 85          # load-bearing
  content_strategy.client_content[{client_slug}]: 70    # supporting
  ideal_customer_profile.client_icps[{client_slug}]: 60 # supporting
```

The 85 on voice is set because below 85, the post will likely fail BOT on the voice-fit axis.

---

## The output_path

Where does the artifact go? Two patterns:

**Pattern A — Profile updates.** The skill produces structured data that updates a profile in `company.yaml` — for skills like `/extract-founder-voice` and `/build-icp` that hydrate the schema:

```yaml
output_path:
  agency-voice: company.yaml::brand_voice.agency_voice
  client-voice: company.yaml::brand_voice.client_voices[{client_slug}]
```

**Pattern B — Artifact files.** The skill produces a markdown artifact that lands in `outputs/` — for skills that produce ship-grade work products (posts, DM sequences, proposals, reports):

```yaml
output_path:
  client-post: outputs/{date}/posts/clients/{client_slug}/{post-slug}.md
  agency-post: outputs/{date}/posts/agency/{post-slug}.md
```

Some skills do both — `/extract-founder-voice` writes the YAML profile AND emits a validation-gate report.

Use template variables (`{date}`, `{client_slug}`, `{post-slug}`, `{week}`) so paths stay consistent across runs and downstream skills can find prior artifacts.

---

## Cross-skill routing — upstream, sibling, downstream

Section 8 of every SKILL.md is the routing block. Three directions:

- **Upstream** — what skills produce your inputs. Voice profile → `/extract-founder-voice`. ICP → `/build-icp`. Naming upstream lets the workspace auto-route operators back to fill gaps when your skill is invoked too early.
- **Sibling** — skills that run alongside yours, share data, gate each other, or operate on the same artifact. Example: `/ghostwrite-client-post` and `/voice-drift-detector` — drift-detector reads what ghostwriter produces; ghostwriter checks drift before publishing.
- **Downstream** — skills that consume your output. Naming downstream forces you to think about output format — your output is someone else's input.

A skill with no upstream, no siblings, and no downstream is probably not a skill — it is an isolated script. Re-think whether it belongs in the workspace.

---

## Verification checklist

Before you ship, walk through this checklist. If any check fails, fix before merging.

1. **Frontmatter complete.** Every field present. `agent` references an existing agent file.
2. **All 9 body sections present.** Title, when-to-invoke, inputs, build sequence, output format, validation gate, failure modes, cross-skill routing, cross-references.
3. **Recursive scope decision documented.** `allowed_scopes` matches the skill's actual nature (agency / client / both).
4. **Thresholds set per `spec/CONTEXT-THRESHOLDS.md` logic.** Load-bearing profile high (75+); supporting profiles middle (50-70).
5. **Output path uses template variables, not hardcoded paths.**
6. **Validation gate is concrete.** A specific test — not a hand-wave.
7. **BOT reference present** if skill produces client-bound or program-bound work (see `docs/THE_BAR.md`).
8. **Failure modes table has ≥3 entries.** Every skill fails in predictable ways. Document the top three.
9. **Cross-references include the relevant `INVARIANTS.md` rules.** No skill operates outside the 14 NEVERs / 14 ALWAYSes.
10. **At least 2 anonymized examples in `examples/`.** Golden outputs that show the skill at its best.

---

## Common failure modes when authoring a new skill

| Failure | What goes wrong | Fix |
|---|---|---|
| Frontmatter missing `required_profiles` | Executes against empty context, ships generic output | Always set required profiles. Use 0 only for skills that BUILD the profile. |
| `allowed_scopes` includes both when only one fits | Operator can invoke client-mode on an agency-only skill; leaks contexts | Pick the scope shape deliberately. When in doubt, narrower. |
| No validation gate | Ships bad work undetected, encoding gap goes unflagged | Add a concrete test. Bad artifacts surfaced fast are cheap; shipped to clients are expensive. |
| No failure-mode table | Future operators repeat avoidable mistakes; the skill never compounds | Write the table. Encode your domain experience. |
| No cross-skill routing | Skill becomes an island, never composed into workflows | Name upstream, siblings, downstream. If you can't, the skill probably doesn't belong. |
| No examples folder | Agents have no anchor for "what success looks like" | Ship 3-5 anonymized golden outputs. Examples teach faster than instructions. |
| Hardcoded paths in `output_path` | Files collide across runs; downstream skills can't find prior artifacts | Use template variables (`{date}`, `{client_slug}`, etc.). |
| Skill agent doesn't exist in `agents/` | Runtime can't load persona overlay; skill executes with default tone | Reference an existing agent or add a new agent file first. |
| `INVARIANTS.md` rules not cited | Skill quietly bypasses sacred rules; hard violation surfaces eventually | Cite the rules that govern your skill's domain. |
| Skill ships without `docs/THE_BAR.md` gate logic | First client-bound output fails BOT; encoding gap surfaces late | Build BOT into the validation gate from day one. |

---

## The Blind Output Test gate

Every skill that produces client-bound or program-bound work ships through the Blind Output Test. The test asks: would a B2B founder pay an agency for this artifact?

When you author a new skill that produces client-facing or student-facing work, your validation gate (section 6 of SKILL.md) MUST include a Blind Output Test step. Read `docs/THE_BAR.md` for the full protocol — the 3 simulated reviewer profiles, the 5-axis rubric, the pass criteria, and what to do on fail.

The test is not optional. A skill that bypasses the BOT eventually ships work that drifts toward the commodity-agency mean — the exact failure mode the workspace exists to prevent.

For internal-only skills (diagnostics, profile audits, working drafts), the BOT is not required. Section 6 should still have a validation gate, but it can be a different kind of check (e.g., "does this profile audit produce a numeric score that matches operator review on a sample of 5 clients").

---

## Worked example: writing `/build-podcast-pitch-sequence` from scratch

Suppose your agency wants to systematically pitch your operator clients to relevant podcasts. No shipped skill does this. Here is how you would author it.

**Define the artifact.** A podcast pitch sequence: a 3-touch outreach to a host, customized to the host's recent episodes, the client's expertise, and the value the appearance would bring. One artifact per pitched podcast.

**Decide scope.** Client-bound work — you're pitching on behalf of a specific client. Scope: client-only. Required parameters: `client_slug` and `podcast_target`.

**Set thresholds.** Load-bearing profile: voice. Set `brand_voice.client_voices[{client_slug}]: 75`. Supporting: `ideal_customer_profile.client_icps[{client_slug}]: 60` so the pitch frames the client's authority against their actual audience.

**Map routing.** Upstream: `/extract-founder-voice` (client-voice), `/build-icp` (client-icp). Optionally a new `/research-podcast-target` for the host research input — either build that first or take research as manual input for v1. Siblings: `/voice-drift-detector` (the pitch is voice-bound), `/build-call-prep` (takes over if the pitch lands an interview). Downstream: `/post-approval-tracker` for the client review queue.

**Write the build sequence.** Six steps: (1) Load context. (2) Read the host's last 10 episodes; identify 2-3 themes the client can speak to. (3) Find the unique angle the host's prior guests haven't covered. (4) Draft touch 1 (cold pitch). (5) Draft touch 2 (follow-up at day 7). (6) Draft touch 3 (break-up note with a concrete value-add). Each step time-boxed.

**Output format.** Markdown with three touch sections, a metadata block (`profiles_used`, `frameworks_used`), and a podcast dossier (host, show, why-this-fits).

**Validation gate.** Two checks: a voice-drift cosine check against the client's last 20 validated outputs (reuse the drift fabric from `/voice-drift-detector`), and a Blind Output Test with three reviewers calibrated to "podcast host who gets 50 pitches a week" — pass criteria per `docs/THE_BAR.md`.

**Failure modes.** At least three: generic flattery in touch 1 (fix by requiring a specific episode reference), client-show audience mismatch (fix upstream by tightening the ICP-to-show fit), three touches with no escalating value-add (fix by structuring each touch around a concrete new offering).

**Cross-references.** Link `INVARIANTS.md` (A-2 metadata, N-1 voice, N-13 no competitor naming), `ENCODING.md`, `docs/RECURSIVE_CONTEXT.md`, `docs/THE_BAR.md`, plus upstream and sibling skills.

**Build the folder.** `mkdir skills/build-podcast-pitch-sequence`. Add `SKILL.md` with frontmatter + nine body sections. Add 3 anonymized examples in `examples/`.

**Update `skills/_INDEX.md`.** Add an entry under the right department table with the agent and status.

**Run the verification checklist** above. Fix anything that fails. Then ship.

One focused afternoon for someone who knows the domain. Most of the time goes into the build sequence and the failure-modes table — the parts where your encoded operator judgement actually lives.

---

## Skill _INDEX.md update

When you ship a new skill, add it to `skills/_INDEX.md`. Find the right department table (Foundations, Marketing, Acquisition, Fulfillment, Program, Cross-cutting) and add a row:

```markdown
| `/build-podcast-pitch-sequence` | linkedin-ghostwriter | Cycle 3 |
```

If your skill is part of a new department or a new wedge tier, add a section. If your skill replaces or supersedes an existing one, mark the old one deprecated rather than deleting — operators may have artifacts that reference the old skill.

Also update the total skill count at the bottom of `_INDEX.md` and add the skill to the relevant CYCLE wedge if it belongs in the first-five-to-run list.

---

## Optional: `.claude/commands/{slug}.md` shim for runtime-specific routing

If you want operators using Claude Code (or any runtime that reads `.claude/commands/`) to invoke your skill as a top-level slash command, add a shim file at `.claude/commands/{skill-slug}.md`. Typically one or two lines pointing at the canonical SKILL.md:

```markdown
Run skill: skills/build-podcast-pitch-sequence/SKILL.md

Pass user-provided arguments through (especially `client_slug` and `podcast_target`).
```

The shim is runtime-specific — equivalent paths exist for other runtimes per `spec/RUNTIMES.md`. The canonical instruction set is always in `skills/{slug}/SKILL.md`; the shim just routes. Without a shim, operators can still invoke the skill by reading SKILL.md directly. Shim is convenience, not requirement.

---

## Cross-references

- `ENCODING.md` — the 6-profile schema your skill will hydrate from
- `INVARIANTS.md` — the 14 NEVERs + 14 ALWAYSes every skill must respect
- `spec/CONTEXT-THRESHOLDS.md` — threshold-setting logic + the master table
- `spec/BLIND-OUTPUT-TEST.md` — the publish-bar gate every client-bound skill ships through
- `docs/RECURSIVE_CONTEXT.md` — the scope decision your skill must make correctly
- `docs/THE_BAR.md` — what "good" means in this workspace, and how to test for it
- `skills/_INDEX.md` — the catalog you will update when you ship
- `skills/extract-founder-voice/SKILL.md` — reference example: a foundations skill, recursive scope, builds a profile
- `skills/build-content-engine/SKILL.md` — reference example: a marketing skill, recursive scope, builds a spec
- `skills/voice-drift-detector/SKILL.md` — reference example: a fulfillment skill, client-only, runs on cron, has tight cross-skill routing

---

*Doc version 1.0.0 — 2026-05-03*
