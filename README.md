<div align="center">

<h1>LinkedIn Marketing Agency</h1>

<p><strong>A team of agents running your LinkedIn agency.</strong></p>

<p>
  <a href="CHANGELOG.md"><img src="https://img.shields.io/badge/version-1.0.0-09090b?style=flat-square&labelColor=09090b&color=52525b" alt="Version"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-09090b?style=flat-square&labelColor=09090b&color=52525b" alt="License"></a>
</p>

</div>

<br/>

> **The agent is thin. The workspace is smart. The workspace IS the product.**

A team of AI agents running every department of a B2B LinkedIn agency — content, outbound DMs, sales, fulfillment, and a high-ticket education program. Each agent carries *your* judgement. Your pricing logic. Your client qualification instincts. Your ghostwriting voice. Your quality bar.

Not workflow automation. Encoded intent. The agents don't just follow rules — they make decisions the way you would, read intent signals the way you read them, and get sharper every cycle.

You stay in the loop where your judgement counts. The rest runs while you sleep.

## The bar

Every artifact this workspace produces — every LinkedIn post, every DM sequence, every call-prep doc, every client report, every retainer proposal — must be better than what a B2B founder would get from hiring a LinkedIn agency at $5K-$25K/month.

If it isn't, the workspace is wrong. We fix the workspace.

This is the gate. Every shipped skill includes a Blind Output Test result.

## Try it

1. Clone:

   ```bash
   git clone <this-repo-url>/linkedin-marketing-agency
   ```

2. Fill in `company.yaml` with your business context. The workspace interviews you for what's missing.

3. Ask for what you need:

   ```
   /extract-founder-voice         your voice profile (or a client's voice)
   /build-icp                     your agency's ICP (or a client's ICP)
   /design-retainer-offer         the DFY service offer (scope, value, guarantee)
   /design-program-offer          the high-ticket education program
   /build-content-engine          pillars, post types, hook library, calendar
   /build-outbound-engine         DM intent taxonomy + sequence per intent class
   /write-linkedin-post           a founder post or ghostwritten client post
   /write-dm-sequence             cold / warm / liked-post / comment-for-magnet
   /build-call-prep               discovery call deep-research deck
   /build-application             qualification application with hard-DQ logic
   /ghostwrite-client-post        client post with voice-drift safety check
   /run-client-dm-ops             a client DM operations cycle
   /source-lead-list              ICP-precise, intent-scored lead list
   /build-client-report           a monthly client report
   /voice-drift-detector          weekly drift audit per client
   /plan-launch                   a cohort or program launch plan
   ```

Works with Claude, ChatGPT, Cursor, or any AI tool that reads files.

Full setup: **[Quickstart](docs/QUICKSTART.md)** · 30 minutes.

## What's inside the folder

```text
linkedin-marketing-agency/
│
├── README.md            ←  the pitch
├── SYSTEM.md            ←  boot file · any AI becomes a LinkedIn agency operator
├── INVARIANTS.md        ←  14 NEVERs + 14 ALWAYSes (sacred rules)
├── ENCODING.md          ←  6-profile schema (recursive: agency + per-client)
├── company.yaml         ←  YOUR business · fill once, reuse forever
│
├── agents/              ←  AI specialists, one per role
│      agency-director           top orchestrator (runs the Three-Lens Audit)
│      foundations-head           voice · ICP · positioning · offer
│      marketing-head             content engine · ghostwriting · virality
│      acquisition-head           outbound DMs · application gate · sales calls
│      fulfillment-head           DFY delivery · voice-drift audit · client reports
│      program-head               curriculum · cohort cadence · student success
│
├── skills/              ←  capabilities · each produces one asset
│   │
│   │   FOUNDATIONS
│   ├── /extract-founder-voice    Voice Profile (yours or a client's)
│   ├── /build-icp                Ideal Customer Profile
│   ├── /design-retainer-offer    DFY service offer document
│   └── /design-program-offer     High-ticket program offer document
│   │
│   │   MARKETING
│   ├── /build-content-engine     Pillars, hook library, calendar
│   ├── /write-linkedin-post      Founder post (your own LinkedIn)
│   ├── /ghostwrite-client-post   Client-bound post (with voice-drift check)
│   ├── /build-hook-library       50+ hook templates per voice
│   ├── /build-posting-calendar   30-day editorial calendar
│   └── /score-hook               Hook scorecard before publishing
│   │
│   │   ACQUISITION
│   ├── /build-outbound-engine    DM intent taxonomy + per-class sequence
│   ├── /write-dm-sequence        Cold / Warm / Liked-post / Comment-for-magnet
│   ├── /classify-dm-intent       Auto-route inbound replies to the right thread
│   ├── /build-qualification-thread  Multi-message qualification flow
│   ├── /build-application        Application form with hard-DQ logic
│   ├── /triage-application       Score + sort incoming applications
│   ├── /source-lead-list         ICP-precise, intent-scored prospect list
│   ├── /score-lead-intent        Pre-DM intent score per prospect
│   ├── /build-call-prep          Discovery call prep doc
│   ├── /summarize-discovery-call  Post-call summary + follow-up draft
│   └── /no-show-recovery         Re-engagement sequence after a no-show
│   │
│   │   FULFILLMENT
│   ├── /onboard-client           First-30-days client onboarding
│   ├── /voice-drift-detector     Weekly per-client voice audit
│   ├── /post-approval-tracker    Client approval workflow tracker
│   ├── /run-client-dm-ops        Daily VA-driven client DM cycle
│   ├── /build-client-report      Monthly client report
│   └── /build-attribution-trace  Lead-to-revenue trace per client
│   │
│   │   PROGRAM
│   ├── /onboard-program-student  Student onboarding (cohort start)
│   ├── /program-cohort-pulse     Weekly cohort health check
│   ├── /build-curriculum         Curriculum module (built from real case studies)
│   ├── /build-first-win-trigger  Engineer the student's first measurable win
│   └── /build-retention-pulse    Retention check (program-side)
│   │
│   │   CROSS-CUTTING
│   ├── /pipeline-pulse           Whole-pipeline diagnostic
│   ├── /strategic-advisory       Open-ended strategic question
│   └── /diagnose-bottleneck      Symptom → root-cause map
│
├── reference/           ←  the brain that makes the skills smart
│   ├── frameworks/             ICP, voice, content, DM, offer, fulfillment frameworks
│   ├── operators/              Anonymized archetype playbooks (inbound, outbound, hybrid)
│   ├── platforms/              LinkedIn, X, IG, YouTube, email playbooks
│   ├── playbooks/              The Hybrid Full-Stack Growth playbook (24-month arc)
│   ├── swipe-file/             Annotated real high-performer LinkedIn posts
│   ├── templates/              Output templates (proposal, onboarding, report)
│   └── verticals/              Vertical adaptations (SaaS, services, e-com, info-product)
│
├── workflows/           ←  multi-step automations
│   └── divisions/             Per-department FSMs (acquisition, marketing, fulfillment...)
│
├── spec/                ←  quality gates
│   ├── BANNED-VOCABULARY.md    Hooks/phrases that get auto-flagged
│   ├── BLIND-OUTPUT-TEST.md    The publish-bar gate
│   └── CONTEXT-THRESHOLDS.md   Profile-completeness gates per skill
│
└── outputs/             ←  every artifact the workspace produces, logged for compounding
```

Each file is plain text. Each folder is owned by you. Nothing is locked behind an app.

## Two businesses, one workspace

This template encodes the **hybrid LinkedIn agency** — the kind of agency that runs both:

**Service tier (DFY).** Done-for-you LinkedIn presence + DM ops for B2B clients. Monthly retainer, $5K-$25K. The work that validates the methodology with real client transformations.

**Program tier.** A high-ticket education program ($7K-$15K) that teaches the same playbook to other agency operators. Higher margin, lower delivery cost, creates the network effect.

Skills are tagged with which tier they apply to. The DFY agency can run alone. The program cannot run without the DFY agency first (no proof, no case studies, no playbook). Order matters and the workspace enforces it.

## Five departments

- **Foundations** — ICP, voice, positioning, mission, offer architecture
- **Marketing** — content engine for your own LinkedIn presence + per-client ghostwriting
- **Acquisition** — outbound DMs, inbound DM gate, application qualification, discovery calls, closing, follow-up
- **Fulfillment** — DFY service delivery (account management, ghostwriting protocol, DM ops for clients, lead-list sourcing, monthly reports)
- **Program** — curriculum, mentorship cadence, community rituals, student-success engineering

Each is rebuilt as agent-first — agents that carry your intent, read signals, and make decisions the way you would.

## What makes this workspace 1-of-1

Most LinkedIn-agency templates give you a pile of post templates and a DM script. This one is built around four primitives that no off-the-shelf template encodes:

### 1. Recursive context

Every other template treats your agency as one context. This workspace treats it as **recursive** — one full context for your agency, AND a separate full context for *every active client*.

A ghostwritten post for client `acme` loads `acme`'s voice, `acme`'s ICP, `acme`'s content strategy. A post for your own LinkedIn loads your agency-level profiles. They never confuse.

This is the encoding mechanism that makes ghostwriting at scale survive past month 4 — when most agencies hit the "this doesn't sound like me" churn wall.

### 2. DM intent taxonomy (4 classes)

Most outbound systems treat every prospect the same. This one classifies every DM thread into one of four intent classes — **Cold**, **Warm**, **Liked-post**, **Comment-for-magnet** — and runs a different sequence per class. The mistake of running a cold sequence on a warm prospect (and vice versa) is structurally impossible.

### 3. Voice-drift detection

Every active client gets a weekly `/voice-drift-detector` audit. The workspace measures whether the ghostwritten posts are still sounding like the client. Below the client's threshold, the audit surfaces a brief and queues a re-extraction interview.

This is the silent quality engine. Most ghostwriting churn happens because drift goes unmeasured for 8-12 weeks. The workspace measures it weekly.

### 4. The Blind Output Test gate

Every client-bound or program-bound artifact runs through three simulated reviewer profiles before ship. The test answers one question: *"Would a B2B founder pay an agency for this?"* Pass = ship. Fail = iterate the artifact AND iterate the encoding (often both).

This prevents drift toward the commodity-agency mean. The workspace ships work that outperforms the agency the client could otherwise hire — or it doesn't ship.

## Every layer, every cycle compounds

Each engagement ships with:

- **Voice extraction interview** — voice profile to ship-grade in week 1
- **ICP build** — per-client ICP with verbatim voice-of-customer language
- **Content engine** — pillars, hook library, posting calendar with virality-engine targets
- **DM engine** — intent taxonomy + per-intent sequences + booking-gate qualification
- **Lead-list operation** — ICP-precise, intent-scored, prioritized
- **Discovery call prep** — account-research-deep, objection-pre-framed, mechanism-rehearsed
- **Retainer proposal** — per-client transformation hypothesis, named mechanism, value stack
- **Monthly report** — signal-dense, attribution-clean, decision-ready
- **Voice-drift audit** — weekly per client

Every action leaves a receipt. Every variant tracks its own conversion. Every winner gets promoted. Every paused variant tells you why.

## Runs while you sleep

Wire the workspace into a runtime with **cron, webhook, and event triggers**. The agents keep working without you in the room.

- **Monday 09:00 (cron)** — `voice-drift-detector` runs across every active client
- **On `lead.applied` (webhook)** — application response triaged, DQ logic runs, qualified leads booked, hard-DQs surfaced to you
- **On `call.ended` (event)** — call recording transcribed, summary generated, follow-up DM drafted, CRM updated
- **Daily 06:00 (cron)** — content engine queues today's agency post + today's per-client posts
- **Continuous (event)** — DM ops run intent classification on inbound replies, route to the right sequence

Triggers are declared in [`paperclip.manifest.yaml`](paperclip.manifest.yaml). Wire them to your scheduler and the workspace operates as a 24/7 LinkedIn agency.

## What you get

**5 agent-first departments. Specialist agents. ~30 skills.**

- **Foundations** — voice extraction · ICP build · positioning · offer architecture · mission framing
- **Marketing** — content engine · hook library · virality engine · per-client ghostwriting · cross-platform repurposing
- **Acquisition** — outbound DM engine · 4-class intent taxonomy · application gate · call prep · closing · follow-up · no-show recovery
- **Fulfillment** — account management · ghostwriting protocol · client DM ops · lead-list sourcing · monthly reporting · voice-drift detection
- **Program** — curriculum design · mentorship cadence · community rituals · first-win engineering · retention pulse

Each department is not a set of rules. Each is a full department rebuilt as agent-first — agents that carry your intent, read signals, and make decisions.

## Runs in any compatible agent runtime

The workspace is files. Files run anywhere that reads files.

- **[Paperclip](https://github.com/paperclip-dev/paperclip)** — open-source agentic runtime with cron, webhook, and event triggers
- **Claude** · **ChatGPT** · **Cursor** — interactive sessions and slash-command execution
- **Claude Agent SDK** — API-driven orchestration
- **BusinessOS** — desktop operating environment for operator teams
- Any HTTP orchestrator that speaks OpenAPI

Runtime-swappable. Your workspace is the asset. The runtime is replaceable.

## Why this matters

Every founder-led LinkedIn agency hits the same wall: the founder IS every department. The ghostwriter quits at month 6. The DM operator drifts off-script by month 3. Client-of-the-month posts read as the same content as last month's client.

Encoding changes the shape of the week. Your expertise — voice calibration, ICP qualification, DM intent stratification, fulfillment quality bars — gets written into agents that run each department on your judgement. You stay. The judgement scales. The agency compounds.

Every cycle the workspace runs, it gets sharper. The gap between this agency and every competitor running on memory widens monthly.

## Repository layout

| Folder | What it is |
|---|---|
| [`.claude/commands/`](.claude/commands/) | Claude Code slash-command shims — one `.md` per skill, wires `/skill-name` to its runtime |
| [`.github/`](.github/) | GitHub-native config: issue templates, PR template, contribution guide, security policy |
| [`agents/`](agents/) | Agent role definitions — director, 5 department heads, specialists |
| [`docs/`](docs/) | User documentation: quickstart, architecture, recursive-context guide, the bar |
| [`prompts/`](prompts/) | Domain-indexed prompt library: voice, ICP, content, DM, sales, fulfillment |
| [`reference/`](reference/) | Methodology library: frameworks, operator playbooks, swipe files, templates, vertical playbooks |
| [`skills/`](skills/) | Skill packages — each with `SKILL.md`, variants, examples, evidence, runtime adapters |
| [`spec/`](spec/) | Workspace specifications: banned vocabulary, blind-output test, context thresholds |
| [`workflows/`](workflows/) | Operational workflows: per-division FSMs (acquisition, marketing, fulfillment, program) |
| [`outputs/`](outputs/) | Every artifact the workspace produces, logged for compounding |

## Documentation

- [Quickstart](docs/QUICKSTART.md) — setup in 30 minutes
- [Architecture](docs/ARCHITECTURE.md) — how the folder is built
- [Recursive Context](docs/RECURSIVE_CONTEXT.md) — how per-client subprofiles work
- [Skill Authoring](docs/SKILL_AUTHORING.md) — write your own agents and skills
- [The Bar](docs/THE_BAR.md) — the Blind Output Test and why every shipped artifact runs through it

## License

MIT. Free forever.

A Heuresis workspace template. [heuresis.ai](https://heuresis.ai)
