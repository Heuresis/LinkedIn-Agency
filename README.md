<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="docs/assets/heuresis-logo-dark.png">
  <source media="(prefers-color-scheme: light)" srcset="docs/assets/heuresis-logo-light.png">
  <img alt="Heuresis" src="docs/assets/heuresis-logo-light.png" width="220">
</picture>

<br/>
<br/>

<h1>LinkedIn Agency</h1>

<p><strong>A team of agents running your LinkedIn agency.</strong></p>

<p>
  <a href="CHANGELOG.md"><img src="https://img.shields.io/badge/version-2.0.0-09090b?style=flat-square&labelColor=09090b&color=52525b" alt="Version"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-09090b?style=flat-square&labelColor=09090b&color=52525b" alt="License"></a>
</p>

</div>

<br/>

> **The agent is thin. The workspace is smart. The workspace IS the product.**

A team of AI agents running every department of a B2B LinkedIn agency — content, outbound DMs, sales, fulfillment, and a high-ticket education program. Each agent carries *your* judgement. Your ghostwriting voice. Your DM intent classification. Your client qualification bar.

Not workflow automation. Encoded intent. The agents don't just follow rules — they make decisions the way you would, read signals the way you read them, and get sharper every cycle.

You stay in the loop where your judgement counts. The rest runs while you sleep.

## Try it

1. Clone:

   ```bash
   git clone https://github.com/Heuresis/LinkedIn-Agency.git
   ```

2. Fill in `company.yaml` with your business context.

3. Ask for what you need:

   ```
   /extract-founder-voice         a voice profile (yours or a client's)
   /build-icp                     an ideal customer profile
   /design-retainer-offer         a DFY service offer document
   /design-program-offer          a high-ticket program offer document
   /build-content-engine          pillars, hook library, calendar
   /build-outbound-engine         DM intent taxonomy + per-class sequences
   /write-linkedin-post           a founder post or ghostwritten client post
   /write-dm-sequence             cold / warm / liked-post / comment-for-magnet
   /build-application             a qualifying application
   /build-call-prep               a discovery call prep doc
   /run-client-dm-ops             a daily client DM operations cycle
   /voice-drift-detector          a weekly per-client voice audit
   ```

Works with Claude, ChatGPT, Cursor, or any AI tool that reads files.

Full setup: **[Quickstart](docs/QUICKSTART.md)** · 30 minutes.

## What's inside the folder

```text
linkedin-agency/
│
├── README.md            ←  the pitch
├── SYSTEM.md            ←  boot file · any AI becomes a LinkedIn agency operator
├── INVARIANTS.md        ←  14 NEVERs + 14 ALWAYSes (sacred rules)
├── ENCODING.md          ←  6-profile schema (recursive: agency + per-client)
├── company.yaml         ←  YOUR business · fill once
│
├── agents/              ←  AI specialists, one per role
│      agency-director              top orchestrator
│      foundations-head             voice · ICP · positioning · offer
│      marketing-head               content engine · ghostwriting · virality
│      acquisition-head             outbound DMs · application gate · sales calls
│      fulfillment-head             DFY delivery · voice-drift audit · client reports
│      program-head                 curriculum · cohort cadence · student success
│
├── skills/              ←  ~30 capabilities · each produces one asset
│   │
│   │   FOUNDATIONS
│   ├── /extract-founder-voice       Voice profile (yours or a client's)
│   ├── /build-icp                   Ideal Customer Profile
│   ├── /design-retainer-offer       DFY service offer document
│   └── /design-program-offer        High-ticket program offer document
│   │
│   │   MARKETING
│   ├── /build-content-engine        Pillars, hook library, calendar
│   ├── /write-linkedin-post         Founder post (your own LinkedIn)
│   ├── /ghostwrite-client-post      Client-bound post (with voice-drift check)
│   ├── /build-hook-library          50+ hook templates per voice
│   ├── /build-posting-calendar      30-day editorial calendar
│   └── /score-hook                  Hook scorecard before publishing
│   │
│   │   ACQUISITION
│   ├── /build-outbound-engine       DM intent taxonomy + per-class sequence
│   ├── /write-dm-sequence           4-class DM sequences
│   ├── /classify-dm-intent          Auto-route inbound replies
│   ├── /build-qualification-thread  Multi-message qualification flow
│   ├── /build-application           Application with hard-DQ logic
│   ├── /triage-application          Score + sort applications
│   ├── /source-lead-list            ICP-precise, intent-scored prospect list
│   ├── /score-lead-intent           Pre-DM intent score per prospect
│   ├── /build-call-prep             Discovery call prep doc
│   ├── /summarize-discovery-call    Post-call summary + follow-up draft
│   └── /no-show-recovery            Re-engagement after a no-show
│   │
│   │   FULFILLMENT
│   ├── /onboard-client              First-30-days client onboarding
│   ├── /voice-drift-detector        Weekly per-client voice audit
│   ├── /post-approval-tracker       Client approval workflow tracker
│   ├── /run-client-dm-ops           Daily VA-driven client DM cycle
│   ├── /build-client-report         Monthly client report
│   └── /build-attribution-trace     Lead-to-revenue trace per client
│   │
│   │   PROGRAM
│   ├── /onboard-program-student     Student onboarding (cohort start)
│   ├── /program-cohort-pulse        Weekly cohort health check
│   ├── /build-curriculum            Curriculum module
│   ├── /build-first-win-trigger     Engineer the student's first measurable win
│   └── /build-retention-pulse       Retention check (program-side)
│
└── reference/           ←  the brain that makes skills smart
    ├── frameworks/             ICP, voice, content, DM, offer, fulfillment frameworks
    ├── operators/              Anonymized archetype playbooks
    ├── platforms/              LinkedIn, X, IG, YouTube, email playbooks
    ├── playbooks/              Multi-step playbook (24-month arc)
    ├── swipe-file/             Annotated high-performer LinkedIn posts
    ├── templates/              Output templates (proposal, onboarding, report)
    └── verticals/              Vertical adaptations (SaaS, services, e-com, info-product)
```

Each file is plain text. Each folder is owned by you. Nothing is locked behind an app.

## Each client ships with

- **Voice profile** — extracted in week 1, ship-grade
- **ICP** — per-client with verbatim voice-of-customer language
- **Content engine** — pillars, hook library, posting calendar
- **DM engine** — 4-class intent taxonomy, per-intent sequences, booking-gate qualification
- **Lead list** — ICP-precise, intent-scored, prioritized
- **Discovery call prep** — research-deep, objection pre-framed
- **Retainer proposal** — transformation hypothesis, named mechanism, value stack
- **Monthly report** — signal-dense, attribution-clean
- **Voice-drift audit** — weekly per client

Every action leaves a receipt. Every variant tracks its own conversion. Every winner gets promoted. Every paused variant tells you why.

## Runs while you sleep

Wire the workspace into a runtime with **cron, webhook, and event triggers**. The agents keep working without you in the room.

- **Monday 09:00 (cron)** — `voice-drift-detector` runs across every active client
- **On `lead.applied` (webhook)** — application response triaged, DQ logic runs, qualified leads booked
- **On `call.ended` (event)** — call recording transcribed, summary generated, follow-up DM drafted, CRM updated
- **Daily 06:00 (cron)** — content engine queues today's agency post + today's per-client posts
- **Continuous (event)** — DM ops run intent classification on inbound replies, route to the right sequence

Triggers are declared in [`paperclip.manifest.yaml`](paperclip.manifest.yaml). Wire them to your scheduler and the workspace operates as a 24/7 LinkedIn agency.

## What you get

**5 agent-first departments. Specialist agents. ~30 skills.**

- **Foundations** — voice extraction · ICP build · positioning · offer architecture
- **Marketing** — content engine · hook library · per-client ghostwriting · cross-platform repurposing
- **Acquisition** — outbound DM engine · 4-class intent taxonomy · application gate · call prep · closing · follow-up
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

Every founder-led LinkedIn agency hits the same wall: the founder IS every department. The ghostwriter quits at month 6. The DM operator drifts off-script by month 3. Client-of-the-month posts read as last month's client.

Encoding changes the shape of the week. Your judgement — voice extraction, ICP build, DM intent classification, ghostwriting standards — gets written into agents that run each department on your judgement. The founder stays. The judgement scales. The agency compounds.

Every cycle, each department runs it gets sharper. The gap between your agency and every competitor operating off memory widens.

This is one template in the library. More shipping, vertical by vertical. Every outcome claim we publish traces to a real deployment with a real operator. Thesis, method, and source go public on ideas. Receipts wait their turn.

## Documentation

- [Quickstart](docs/QUICKSTART.md) — setup in 30 minutes
- [Architecture](docs/ARCHITECTURE.md) — how the folder is built
- [Skill Authoring](docs/SKILL_AUTHORING.md) — write your own agents and skills

## License

MIT. Free forever.

Built by [Syed Hussain](https://heuresis.ai) at [Heuresis](https://heuresis.ai).
