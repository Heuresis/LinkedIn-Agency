---
name: Bug report
about: Something in the template does not work as documented
title: "[bug] "
labels: bug
assignees: ''
---

## What happened

A clear, concrete description. What skill or file or runtime surface broke.

## What I expected

What the documented behavior says should happen.

## Repro

Minimal steps to reproduce. Include the skill slug, runtime, and any relevant commands.

1.
2.
3.

## Runtime

- [ ] Claude Code (slash-command)
- [ ] Claude Agent SDK
- [ ] ChatGPT
- [ ] Cursor
- [ ] Paperclip
- [ ] BusinessOS
- [ ] Generic LLM (specify below)

Runtime version / model:

## Workspace version

LinkedIn Marketing Agency version from `CHANGELOG.md` or the `README.md` header:

## Relevant files

Paths to the files involved (e.g. `skills/extract-founder-voice/SKILL.md:123`).

## Evidence

Logs, screenshots, `outputs/` artifacts, or `evidence/` entries that show the failure. Do not paste filled `company.yaml` values, `_private/` contents, real client names, or `.env` values — per [INV-11](../../INVARIANTS.md) operator and client data stays local.

## Severity

- [ ] Blocks first run
- [ ] Skill produces wrong output
- [ ] Quality gate misfires
- [ ] Voice-drift or recursive-context issue
- [ ] Documentation drift
- [ ] Other

## Additional context

Anything else relevant — related skills, recent changes, runtime quirks.
