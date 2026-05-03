# Architecture — How the Workspace Is Built

> **For the operator who wants to understand the system before customizing it.** No PhD-CS language. No frameworks-on-frameworks. Just a clear explanation of how the folder works, why it's built this way, and what happens when you type a slash command.

---

## The big picture

The **workspace IS the product.** The AI agent on top of it is thin — it just reads files and follows instructions. The smart part lives in the markdown and YAML files in this folder. Your judgement (voice, ICP, qualification standards, ghostwriting rules, scope guardrails) gets encoded into those files. Any AI that reads them becomes a LinkedIn agency operator with your judgement. Swap the AI tool — Claude for ChatGPT for Cursor for whatever ships next year — and the workspace still runs. **The workspace is the asset. The runtime is replaceable.**

---

## The 4-layer cognitive architecture

Every agent that boots into this workspace is built on four stacked layers. They're declared in `SYSTEM.md`. Knowing what lives in each layer is how you know where to make a change.

### Layer 1 — Identity & Cognitive Architecture
This is where the agent's *role* is defined: "You are a LinkedIn Marketing Agency operator." It also activates the right knowledge in the underlying AI by naming the frameworks the agent should think in (Signal Theory, Awareness Levels, Loss Aversion, Recursive ICP, etc.). Specificity here = depth of activation. Don't water this down.

### Layer 2 — Intelligence System
This is *how the agent thinks*. The 6-Profile Operator Context (your business, your voice, your ICP, your content, your case studies, your offers) — all stored in `company.yaml`. The Three-Lens Audit (Pipeline → Channel → Layer) that runs before any deliverable. The persuasion primitives every output is built on.

### Layer 3 — Operational System
This is *how work gets routed*. Eighteen agents organized into five departments. Skills mapped to agents. Interaction modes (strategic advisory, skill execution, context-building interview, performance analysis, refinement, client onboarding). When you type a slash command, this layer decides which agent picks it up.

### Layer 4 — Constraints & Calibration
This is *what the agent will never do*. Fourteen NEVERs and fourteen ALWAYSes (in `INVARIANTS.md`). The publish-bar gate (Blind Output Test). Confidence calibration on every output. Honest gap flags. This layer overrides everything else — even if a skill or you tell the agent to do something different, the invariants win.

---

## The boot sequence — what happens when an AI loads this workspace

When you point any AI tool at this folder, it reads files in this order:

1. **`SYSTEM.md`** — identity layer. The AI learns it is now a LinkedIn agency operator.
2. **`INVARIANTS.md`** — sacred rules. The 14 NEVERs and 14 ALWAYSes load before anything else can override them.
3. **`ENCODING.md`** — the schema. The AI learns what shape the operator's context takes (the 6 profiles).
4. **`company.yaml`** — your actual values. The AI now knows YOUR agency, YOUR voice, YOUR ICP.
5. **`agents/*.md`** — agent personas. The AI scans which specialist personas it can overlay.
6. **`skills/*/SKILL.md`** — skill instructions. The AI scans the catalog of capabilities.
7. **`spec/*.md`** — quality gates. The AI loads the publish bar.
8. **Ready.** The AI now waits for your slash command.

This sequence is non-negotiable. If the AI skips `INVARIANTS.md`, it can ghostwrite without a voice profile. If it skips `ENCODING.md`, it doesn't know what your context looks like. The boot order *is* the safety system.

---

## The 6-profile recursive schema

Your context lives in six profiles. Each one is weighted by how much it matters for output quality. The total adds to 100%.

### Profile 1 — Business Context (15%)
Your agency's basic facts: name, model, mission, USP, offer ladder, team, channels, tech stack, current challenges, near and long-term goals. The "who you are" layer.

### Profile 2 — Brand Voice (15%)
How you talk. Tone framework, signature phrases, banned phrases, persuasion style, story bank. **This profile is recursive** — there's one for the agency operator (you) AND one per active client. Voice extraction happens with each new client.

### Profile 3 — Ideal Customer Profile (20% — the heaviest)
Who you sell to. Demographics, firmographics, the pains in their actual language, the objections that kill deals, the buying triggers. **Also recursive** — agency-level ICP (who you sell to) AND per-client ICP (who each of your clients sells to).

### Profile 4 — Content Strategy (15%)
Your content engine. Pillars, post types, hook library, posting calendar, virality patterns. **Recursive** — agency content + per-client content.

### Profile 5 — Case Studies & Client Results (15%)
Your proof inventory. Anonymized client transformations, before/after metrics, transformation language, screenshots. The proof you cite when the agent writes anything client-bound or sales-bound.

### Profile 6 — Product Strategy (20%)
Your offers. Service-tier (DFY retainers) and program-tier (high-ticket education), value stacks, bonuses, guarantees, payment terms, scope guardrails. The "what you sell" layer.

**Why "recursive"?** Because the agency has a voice, an ICP, and content — AND each client has their own voice, ICP, and content. Both must be encoded. When the workspace ghostwrites for a client, it loads the *client's* subprofile, not yours.

---

## Departments and agents

**Five departments. Eighteen agents.**

| Department | Department Head | Specialists |
|---|---|---|
| **Foundations** | `foundations-head` | `voice-extractor`, `icp-builder`, `offer-architect` |
| **Marketing** | `marketing-head` | `content-strategist`, `hook-writer`, `linkedin-ghostwriter` |
| **Acquisition** | `acquisition-head` | `dm-strategist`, `lead-list-builder`, `call-prep-specialist` |
| **Fulfillment** | `fulfillment-head` | `account-manager`, `client-reporter` |
| **Program** | `program-head` | `program-mentor` |
| **Top-level** | `agency-director` | (orchestrator across all five departments) |

Each agent is a thin persona overlay on the same underlying AI model. They don't have separate intelligence — they share `SYSTEM.md`, `INVARIANTS.md`, `ENCODING.md`, and `company.yaml`. What makes them specialists is their persona file (`agents/{agent-slug}.md`), which sets their voice, decision authority, escalation path, and the failure modes they guard against.

Full registry: `agents/_INDEX.md`.

---

## Skills

**~40 skills.** A skill is one capability that produces one asset. Examples: `/extract-founder-voice` produces a voice profile. `/build-icp` produces an ICP document. `/write-linkedin-post` produces a post.

Each skill lives in its own folder:

```
skills/{skill-slug}/
├── SKILL.md           ← the instruction set the agent reads
├── examples/          ← 3-5 golden outputs (anonymized)
└── adapters/          ← optional runtime-specific bindings
```

**`SKILL.md` declares its requirements** at the top:

```yaml
required_profiles:
  ideal_customer_profile.agency_icp: 70
  brand_voice.agency_voice: 60
```

If your context profiles aren't at the threshold, the skill **refuses to run** and starts an interview to fill the gap. This is the unlock-gate system.

Full skill catalog: `skills/_INDEX.md`. The wedge (run these first) is `/extract-founder-voice` → `/build-icp` → `/design-retainer-offer` → `/build-content-engine` → `/build-outbound-engine`.

---

## Reference — the brain that makes skills smart

The skills are short. They lean on the deeper knowledge in `reference/`. This is where the encoded thinking lives.

| Folder | What's inside | What it's for |
|---|---|---|
| `reference/frameworks/` | Methodology docs (Recursive ICP, Intent Taxonomy, Hook Anatomy, Voice-Drift Detection, Retainer Architecture, Virality Engine) | Skills cite these for the underlying logic |
| `reference/operators/internal/` | Encoded internal roles (account manager, ghostwriter, DM operator, lead-list builder, program mentor) | The "how this role thinks" docs your team can train on |
| `reference/operators/external/` | Anonymized external operator archetypes (the inbound-content authority, the outbound DM operator, the hybrid full-stack operator) | Pattern library — "which archetype is this client?" |
| `reference/platforms/linkedin/` | LinkedIn-specific algorithm behavior, format constraints, hook library, virality patterns, banned-by-LinkedIn behaviors | Deepest folder. The platform layer the workspace optimizes for |
| `reference/platforms/{youtube,x,instagram,email}/` | Secondary channel playbooks | For repurposing a LinkedIn post into other channels |
| `reference/swipe-file/` | Anonymized real high-performer posts, hooks, DM threads, lead magnets, application questions, with annotated post-mortems | The "what's working right now" library |
| `reference/templates/` | Output document structures (call-prep, retainer proposal, client report, voice extraction interview) | Reusable scaffolds |
| `reference/playbooks/` | Multi-step compound playbooks (the hybrid full-stack growth arc, 60-day roadmaps, 12-month program launch) | The big-picture operator journeys |
| `reference/verticals/` | Per-vertical adaptations (B2B SaaS, B2B services, agencies-selling-to-agencies, coaches, consultants) | Per-niche language and conversion specifics |
| `reference/canonical/` | The Heuresis canonical specs (architecture bible, signal theory, integrations contracts) | The cross-template invariants |

---

## Workflows — the 5 finite state machines

Each department has a state machine in `workflows/divisions/{department}.md`. Think of these as a flowchart of every state a piece of work can be in, and what triggers a transition between states.

| FSM | What it models |
|---|---|
| `foundations.md` | The lifecycle of every voice/ICP/offer profile from `initialized` → `interview-scheduled` → `building` → `at-threshold` → `elite` → `stale` → `re-extracted` |
| `marketing.md` | The content lifecycle from idea → draft → voice-drift check → operator approval → published → measured → fed back into hook library |
| `acquisition.md` | The pipeline from lead-list → DM sent → DM intent classified → qualification thread → application → discovery call → close (or DQ at any stage) |
| `fulfillment.md` | The DFY client lifecycle from signed → onboarded → first deliverable shipped → monthly reporting → voice-drift audit → renewal (or churn brief) |
| `program.md` | The student lifecycle from enrolled → cohort start → first-win triggered → mid-program pulse → graduation → community retention |

Why these matter: they're the **agreement on what state work is in**. When the agency-director runs a `/pipeline-pulse`, it queries each FSM and reports where work is stuck.

---

## Specs — the publish bar

Three small files in `spec/` enforce the standards every output must meet.

### `BANNED-VOCABULARY.md`
The anti-slop filter. A list of phrases that pattern-match generic LinkedIn-guru content — `"Let's dive in"`, `"game-changer"`, `"🚀 Excited to share"`, `"In this post, I'll share"`, etc. The workspace will not produce them in any client-bound or program-bound artifact. Hard block. Refresh quarterly as the LinkedIn writing landscape shifts.

### `BLIND-OUTPUT-TEST.md`
The publish gate. Every client-bound or program-bound artifact runs through one question: *would three readers in the artifact's intended audience say "this came from a real operator who knows my world," or "this is AI-generated content"?* At least 2 of 3 must say human-operator. Below that, the artifact iterates — or the encoding gets sharpened.

### `CONTEXT-THRESHOLDS.md`
The unlock gates. A master table of "what profile completeness is required before each skill can run." `/ghostwrite-client-post` requires the client's voice profile at ≥80%. `/design-retainer-offer` requires `agency_icp ≥ 70%`. Below the threshold, the skill blocks and triggers interview-mode to fill the gap. **Quality of input is enforced upstream so quality of output is guaranteed downstream.**

---

## The runtime layer — how this runs in different tools

The workspace is just files. Files run anywhere. The runtime is the AI tool that reads them.

| Runtime | How the workspace runs |
|---|---|
| **Claude (desktop or web)** | Attach the workspace folder. Type a slash command. Claude reads files in the boot order, executes the skill. Works for solo operator sessions. |
| **ChatGPT** | Upload the core files (`SYSTEM.md`, `INVARIANTS.md`, `ENCODING.md`, `company.yaml`) at session start. Same boot. File limits are tighter, so not everything loads at once. |
| **Cursor** | Open the workspace folder in Cursor. `@`-mention files in chat. Cursor handles cross-references natively. Best for operators who like seeing the files while they work. |
| **Paperclip** (open-source agentic runtime) | Reads `paperclip.manifest.yaml` at the workspace root. Wires cron, webhook, and event triggers. The workspace runs **autonomously** on a schedule — no operator in the room. |
| **Claude Agent SDK** | API-driven. The workspace becomes a programmable agent. Other systems can call skills via REST. |
| **BusinessOS** | Desktop operating environment for operator teams. Each agent gets a window. Multiplayer-ready. |
| **Any HTTP orchestrator** | The OpenAPI adapter contract makes this workspace callable from anything that speaks HTTP. |

**The core principle:** the workspace doesn't depend on any one runtime. If the runtime gets deprecated, you swap it. The workspace stays.

---

## The 14 NEVERs and 14 ALWAYSes — why they're sacred

Read the full list in `INVARIANTS.md`. The short version: each rule represents a failure mode that has burned a real LinkedIn agency. Skipping the voice-drift check causes silent ghostwriting drift and 4-month client churn. Skipping the Three-Lens Audit causes the agency to push more cold DMs when the actual bottleneck is a leaky discovery call. Inventing a client result number causes legal exposure. Each invariant is a scar.

The cost of holding all 28 rules is friction the operator notices once a week. The cost of relaxing one is a category of failure the agency suffers for months. **The trade is correct.**

When an invariant is violated, the workspace **blocks** — halt the operation, surface the violation, present the operator the explicit choice: fix upstream, or override (logged + flagged). No silent breaches.

---

## How a request flows through — worked example

You type `/ghostwrite-client-post` and pass `client_slug=acme`. Here's what happens.

### Step 1 — Routing
The runtime parses the slash command. Looks up `/ghostwrite-client-post` in `skills/_INDEX.md`. Finds the agent: `linkedin-ghostwriter`.

### Step 2 — Context load
The runtime loads, in order:
1. `SYSTEM.md` (identity)
2. `INVARIANTS.md` (sacred rules — N-1 will fire if it tries to ghostwrite without a client voice profile)
3. `ENCODING.md` (the schema)
4. `company.yaml` (operator's actual values, including `brand_voice.client_voices[acme]`, `ideal_customer_profile.client_icps[acme]`, `content_strategy.client_content[acme]`)
5. `agents/linkedin-ghostwriter.md` (the persona overlay)
6. `skills/ghostwrite-client-post/SKILL.md` (the instructions)

### Step 3 — Threshold gate
The skill declares it needs:
- `brand_voice.client_voices[acme]` ≥ 80%
- `ideal_customer_profile.client_icps[acme]` ≥ 75%
- `content_strategy.client_content[acme]` ≥ 60%

The runtime computes the scores. If `acme`'s voice profile is at 55%, the skill **blocks**: "SKILL_BLOCKED — context_threshold_unmet. Run `/extract-founder-voice` with `scope=client-voice client_slug=acme` first." The operator either runs the prerequisite or explicitly overrides (logged).

### Step 4 — Three-Lens Audit
Before generating, the agent audits Pipeline → Channel → Layer. Quick diagnostic: "Pipeline: Acquisition (acme needs more inbound calls). Channel: Inbound underbuilt. Layer: Mechanism — acme's content has been pillar-only, no mechanism breakdown posts. Producing a mechanism-breakdown post will close the layer gap." Diagnostic gets attached to artifact metadata.

### Step 5 — Generation
The agent writes the post:
- Pulls hooks from `content_strategy.client_content[acme].hook_library.proven_hooks`
- Writes in `brand_voice.client_voices[acme]` voice — using their `phrases_to_use`, avoiding their `phrases_to_avoid`
- References `ideal_customer_profile.client_icps[acme].voice_of_customer.pain_language_patterns` to make pain specific
- Cites `case_studies.agency_case_studies` for proof (only cleared for use)

### Step 6 — Banned vocabulary lint
The output gets scanned against `spec/BANNED-VOCABULARY.md`. Any banned phrase = halt + rewrite.

### Step 7 — Voice-drift check
Mandatory per N-2. The post gets compared (cosine similarity) against the last 20 client-validated posts for `acme`. Below threshold = halt + flag for re-extraction.

### Step 8 — Blind Output Test
Per N-10 + A-4. The agent simulates 3 readers in the artifact's audience. At least 2 of 3 must say "this came from a human operator who knows my world." Below pass = iterate.

### Step 9 — Confidence + gap flags
The artifact ships with metadata: confidence tier, profile completeness, gap flags. No silent uncertainty.

### Step 10 — Operator review
The artifact is **never "final."** Per N-9, every output is a draft until the operator marks it shipped. The operator reviews, edits, approves (or rejects with reason).

### Step 11 — Log + feedback loop
Per A-10, the artifact logs to `outputs/{date}/ghostwrite-client-post/{post-id}.md` with metadata, the diagnostic, the confidence, and the operator's accept/reject signal. If rejected with reason, per A-9, the relevant client subprofile updates (e.g., a new `phrase_to_avoid` gets appended, the tone framework adjusts).

The next time the operator runs `/ghostwrite-client-post acme`, the agent is sharper than this run. **That's the encoding flywheel.**

---

## Where to go from here

- **Just starting?** [`docs/QUICKSTART.md`](QUICKSTART.md) — 30 minutes to a working setup.
- **Adding a client?** Read the **Recursive Context** section of [`ENCODING.md`](../ENCODING.md) and run `/onboard-client`.
- **Wiring autonomous runs?** Read [`paperclip.manifest.yaml`](../paperclip.manifest.yaml) — cron, webhook, and event triggers.
- **Understanding the publish bar?** Read [`spec/BLIND-OUTPUT-TEST.md`](../spec/BLIND-OUTPUT-TEST.md).
- **Customizing a skill?** Read the skill's `SKILL.md` and the skill-authoring guide (`docs/SKILL_AUTHORING.md` once published).

---

*The workspace is the asset. The runtime is replaceable. Your judgement compounds.*

*Built by Heuresis. heuresis.ai*
