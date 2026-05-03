# Agent Index

> Registry of every agent in this workspace. The agent is thin; the workspace is smart. Each agent is a persona overlay on the same generic LLM runtime — they specialize by reading SYSTEM.md, INVARIANTS.md, ENCODING.md, company.yaml, and their own persona file.

## Topology

```
agency-director (orchestrator)
├── foundations-head
│   ├── voice-extractor
│   ├── icp-builder
│   └── offer-architect
├── marketing-head
│   ├── content-strategist
│   ├── hook-writer
│   └── linkedin-ghostwriter
├── acquisition-head
│   ├── dm-strategist
│   ├── lead-list-builder
│   └── call-prep-specialist
├── fulfillment-head
│   ├── account-manager
│   └── client-reporter
└── program-head
    └── program-mentor
```

## Director (1)

| Agent | File | Owns |
|---|---|---|
| `agency-director` | `agency-director.md` | Top-level orchestrator. Three-Lens Audit. Routes to dept heads. Owns: `/pipeline-pulse`, `/strategic-advisory`, `/diagnose-bottleneck` |

## Department Heads (5)

| Agent | File | Owns Department |
|---|---|---|
| `foundations-head` | `foundations-head.md` | Foundations — voice, ICP, positioning, offer architecture |
| `marketing-head` | `marketing-head.md` | Marketing (Content) — content engine, hook library, virality engine, posting cadence |
| `acquisition-head` | `acquisition-head.md` | Acquisition (Sales) — outbound DM ops, application gate, discovery calls, closing |
| `fulfillment-head` | `fulfillment-head.md` | Fulfillment — DFY service delivery, voice-drift detection, client reporting |
| `program-head` | `program-head.md` | Program — high-ticket education delivery, cohort cadence, student success |

## Specialists (12)

| Agent | File | Skills they execute |
|---|---|---|
| `voice-extractor` | `voice-extractor.md` | `/extract-founder-voice`, `/voice-drift-detector` |
| `icp-builder` | `icp-builder.md` | `/build-icp`, `/refine-icp-from-evidence` |
| `offer-architect` | `offer-architect.md` | `/design-retainer-offer`, `/design-program-offer`, `/build-value-stack`, `/design-guarantee` |
| `content-strategist` | `content-strategist.md` | `/build-content-engine`, `/build-posting-calendar`, `/repurpose-to-x`, `/repurpose-to-ig` |
| `hook-writer` | `hook-writer.md` | `/build-hook-library`, `/score-hook`, `/audit-hook-library-decay` |
| `linkedin-ghostwriter` | `linkedin-ghostwriter.md` | `/write-linkedin-post`, `/ghostwrite-client-post` |
| `dm-strategist` | `dm-strategist.md` | `/build-outbound-engine`, `/write-dm-sequence`, `/classify-dm-intent`, `/build-qualification-thread` |
| `lead-list-builder` | `lead-list-builder.md` | `/source-lead-list`, `/score-lead-intent`, `/refresh-list-by-engagement` |
| `call-prep-specialist` | `call-prep-specialist.md` | `/build-call-prep`, `/summarize-discovery-call`, `/no-show-recovery` |
| `account-manager` | `account-manager.md` | `/onboard-client`, `/voice-drift-detector`, `/client-content-sourcing-pulse`, `/post-approval-tracker` |
| `client-reporter` | `client-reporter.md` | `/build-client-report`, `/build-attribution-trace` |
| `program-mentor` | `program-mentor.md` | `/onboard-program-student`, `/program-cohort-pulse`, `/build-curriculum`, `/build-first-win-trigger` |

Total: **18 agents** (1 director + 5 dept heads + 12 specialists)

## Persona file structure

Every agent file follows this structure:

```markdown
# {agent-slug}

## Identity
One paragraph: who this agent is, what role it plays, what voice it carries.

## Department
Which dept it sits in. Who its parent is. Who its children are.

## Skills owned
Bulleted list of skill slugs this agent executes.

## Voice
How this agent speaks (when speaking to the operator vs. when producing artifacts).

## Decision authority
What this agent decides on its own vs. what it escalates.

## Escalation path
When and how to escalate to dept head or director.

## Context profile dependencies
Which profiles this agent reads from before acting.

## Failure modes guarded against
The 3-5 known ways this agent can produce bad work, and the gates that prevent it.
```

## Routing rule

The user invokes a skill. The skill's `SKILL.md` declares which agent persona executes it. The runtime loads:
1. `SYSTEM.md` (identity)
2. `INVARIANTS.md` (sacred rules)
3. `ENCODING.md` (schema)
4. `company.yaml` (values)
5. `agents/{agent-slug}.md` (persona overlay)
6. `skills/{skill-slug}/SKILL.md` (instruction set)
7. Per-skill referenced reference files

Persona overlay = last loaded = highest specificity wins for voice and decision style. Invariants override everything.
