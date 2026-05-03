# Contributing — LinkedIn Marketing Agency

Contribution guide for this workspace template. The main line is authored by the maintainers; external contributions are accepted when they preserve the invariants below.

The `.github/` directory holds the GitHub-native config for this repository: this contribution guide, the security policy, code-owners, the pull-request template, and issue templates.

## Before you change anything

1. **Read `SYSTEM.md`, `INVARIANTS.md`, and `ENCODING.md` end to end.** These three files are load-bearing. An edit that contradicts them cascades through every skill output.
2. **Check `spec/BANNED-VOCABULARY.md`.** Anti-slop constraints are enforced at skill-execution time. New content has to pass the banned-vocab check before it ships.
3. **Check `spec/BLIND-OUTPUT-TEST.md`.** Every client-bound or program-bound artifact runs through the blind-output gate before ship. Understand the gate before adding skills that produce client work.
4. **Run the existing chain once.** If you have never executed `/extract-founder-voice → /build-icp → /design-retainer-offer → /build-content-engine → /build-outbound-engine` against a test agency context, do that first. The chain is the reality check for any architectural change.

## Where changes go

| Change type | File(s) to edit |
|---|---|
| A new skill | `skills/{new-slug}/SKILL.md` + `adapters/` + `evidence/` + `.claude/commands/{new-slug}.md` + `skills/_INDEX.md` entry |
| A new agent | `agents/{new-slug}.md` with frontmatter + reporting chain + `agents/_INDEX.md` entry |
| An invariant change | `INVARIANTS.md` — requires maintainer sign-off, because the change cascades through every skill |
| A new framework doc | `reference/frameworks/{category}/{name}.md` with source attribution |
| A new operator playbook | `reference/operators/{slug}.md` with anonymized methodology descriptors only |
| A new swipe-file post | `reference/swipe-file/posts-linkedin/{pillar}/{slug}.md` with annotated rationale |
| A schema change to `company.yaml` | `ENCODING.md` first, then `company.yaml`, then every skill that reads that compartment |
| A workflow / FSM | `workflows/divisions/{division}.md` |
| Spec gate | `spec/{name}.md` |

## Anonymization rule (non-negotiable)

This workspace publishes no real operator names, no real client names, no real agency names anywhere — not in skills, not in swipe-file annotations, not in operator playbooks, not in commits, not in PR descriptions.

When citing methodology that came from a real operator's work, use anonymized methodology descriptors: *"info-product operator,"* *"construction-vertical agency,"* *"long-form ghostwriting agency."* Never the operator's name. Never the company's name.

If you see a name leak, file a PR with the anonymization fix immediately.

## Attribution rule (non-negotiable)

No AI-tool attribution in any pushed artifact. Specifically:

- No `Co-Authored-By: Claude` (or any AI) trailer in commit messages.
- No `Generated with Claude Code` (or any AI) footer in PR bodies, issue bodies, file content, or release notes.
- No `🤖` AI-author markers in any pushed file.

If a violation already exists in history, rewrite the commit (`git commit --amend` or rebase) and `gh pr edit --body` to remove. Do not defer.

The workspace publishes under the operator's name and the Heuresis brand only.

## Commit etiquette

- Commits should be narrow — a single skill, a single framework, a single spec update. Avoid mixing structural and content changes in one commit.
- Use the imperative mood. Write *what changed*, not what you did not do.
- When a commit touches `SYSTEM.md`, `INVARIANTS.md`, or `ENCODING.md`, note it clearly in the message so reviewers can check for cascade.

## Quality gates before a PR

Run this before you ask for review:

1. **Banned vocabulary sweep.** `grep -i` the list in `spec/BANNED-VOCABULARY.md` against your changed files. Zero hits in active content.
2. **Operator-name sweep.** Confirm no real operator, agency, or client names appear anywhere in your diff.
3. **AI-attribution sweep.** Confirm no `Co-Authored-By: Claude`, `Generated with Claude Code`, or AI-author markers in your commits, PR description, or files.
4. **Hardcoded path sweep.** No absolute paths starting with `/Users/` or any other developer-specific root. Template paths are always workspace-relative.
5. **Link integrity.** Every new markdown link resolves to a real file in this workspace.
6. **Version stamp.** If you touched a top-level doc, bump or verify the footer version.
7. **Cross-reference check.** If you touched the file catalog in `SYSTEM.md`, confirm every referenced file exists.
8. **Private-data check.** No `company.yaml` values, no `_private/` content, no `.env` values, no client-identifying info anywhere.

## Style

- Markdown only. No HTML unless absolutely required.
- Fenced code blocks with a language hint.
- Tables for structured lists when rows repeat a schema.
- Use declarative prose. Heuresis voice is calm, specific, and does not over-explain.
- Do not write "we do not X" / "this is not Y" — declare positively instead.
- Do not cite competitors. Compare this workspace to itself at different versions, not to other vendors.

## Review checklist

Reviewers confirm:

- Counts in `README.md`, `SYSTEM.md`, `skills/_INDEX.md`, and `agents/_INDEX.md` agree.
- `.gitignore` still protects credentials and per-client encoded data.
- No forbidden content (banned vocabulary, real names, AI attribution, leaked paths) entered the repo.
- Every skill change has a corresponding `evidence/blind-output-test.md` update if the output contract changed.

## Roadmap changes

The canonical roadmap lives at the bottom of `skills/_INDEX.md` ("Roadmap") and in `CHANGELOG.md` under "Known next steps." Do not start a roadmap skill without updating the INDEX entry to **🟡 v1.1 target** or **⏳ v2.0 roadmap** first.

---

*LinkedIn Marketing Agency — a Heuresis workspace template.*
