# Recursive Context — How Per-Client Subprofiles Work

> **The 1-of-1 primitive of this workspace.** The thing that prevents your ghostwriter from accidentally writing this week's post for one client in last week's client's voice. If you read only one doc to understand why this workspace beats a paid agency, read this one.

---

## The problem this solves

Every LinkedIn agency that signs more than three clients eventually mixes them up. A ghostwriter who's been in the SaaS founder's voice all morning sits down at 2pm to write for the executive coach and a phrase from the morning bleeds into the afternoon. The coach's audience reads it and something feels off. They can't articulate it. They just stop engaging. Six weeks later the client says "this doesn't sound like me anymore" and churns.

This is not a discipline problem. It is a structural problem. Human ghostwriters carry voices in their head, and heads leak. The same applies to AI agents — if a single voice profile is loaded across every client, every client eventually sounds like the same agency-mean voice. Recursive context is the structural fix: every client gets their own complete profile, and every skill that produces client-bound work loads only that client's profile — never the agency's, never another client's, never a generic blend. The leak is engineered out of the system.

Most agencies try to solve this with checklists, ghostwriter discipline, and post-publish review. None of those scale past 5-7 clients. Recursive context scales as far as you can declare client slugs, because the contamination prevention is at the architecture layer, not at the human-attention layer.

---

## The mental model

Imagine your filing cabinet. The agency itself has one drawer — your voice, your ICP, your content engine, your offers. That drawer is full and complete.

Then for every client you sign, you add a new drawer. The new drawer has the same shape as the agency drawer — voice, ICP, content engine — but every file inside is the client's, not yours. The Acme drawer holds Acme's voice. The Globex drawer holds Globex's voice. None of the drawers share files. None of the contents leak across drawers.

When the ghostwriter sits down to write a post for Acme, the workspace opens the Acme drawer. Only the Acme drawer. The agency drawer stays shut. The Globex drawer stays shut. The ghostwriter writes from inside one drawer at a time.

That is recursive context. Same shape, repeated per client, never mixed.

The technical word for this is "recursive" because the same schema applies at multiple levels — once at the agency level, once per client. The everyday word for it is "your filing cabinet has the right number of drawers, and the drawers are correctly labeled."

---

## The 6 profiles, recursively

The workspace has a single 6-profile schema (defined in `ENCODING.md`). It applies twice: once at the agency level (the operator's own context), and again at the client level (one full subprofile per active client).

| # | Profile | Agency-level path | Per-client path | Recursive? |
|---|---|---|---|---|
| 1 | Business Context | `business_context` | n/a (clients don't get their own agency-context) | No |
| 2 | Brand Voice | `brand_voice.agency_voice` | `brand_voice.client_voices[{slug}]` | Yes |
| 3 | Ideal Customer Profile | `ideal_customer_profile.agency_icp` | `ideal_customer_profile.client_icps[{slug}]` | Yes |
| 4 | Content Strategy | `content_strategy.agency_content` | `content_strategy.client_content[{slug}]` | Yes |
| 5 | Case Studies | `case_studies.agency_case_studies` | `case_studies.client_case_studies[{slug}]` | Yes |
| 6 | Product Strategy | `product_strategy.service_tier` | n/a (clients don't sell offers; we sell to them) | No |

So when you sign Acme as a client, the workspace creates four new branches under `brand_voice`, `ideal_customer_profile`, `content_strategy`, and `case_studies`, each keyed by `acme`. Acme's voice profile is a complete voice profile in its own right — story bank, phrases-to-use, phrases-to-avoid, drift threshold, all of it. Not a delta from the agency profile. A complete, standalone profile.

The two non-recursive profiles (Business Context, Product Strategy) live at the agency level only. Acme is your client; Acme does not have your agency's product ladder, and you do not have Acme's internal business context. That's correct. Recursion applies only where it matters — to the four profiles that drive client-bound creative work.

---

## How the workspace knows which scope to load

Every skill that produces work in this workspace declares an `allowed_scopes` field in its frontmatter. Most look like this:

```yaml
allowed_scopes:
  - agency-voice
  - client-voice
```

When you invoke the skill, you pass the scope as a parameter:

```
/extract-founder-voice scope=agency-voice
/extract-founder-voice scope=client-voice client_slug=acme
```

The first invocation tells the skill to extract your own voice and write it to `brand_voice.agency_voice`. The second invocation tells the skill to extract Acme's voice and write it to `brand_voice.client_voices[acme]`.

The same pattern applies across `/build-icp`, `/build-content-engine`, and every other recursive skill. The scope parameter is the routing key. The `client_slug` is the address inside the per-client branch.

Skills that are inherently client-only (like `/ghostwrite-client-post` or `/voice-drift-detector`) do not have an agency scope at all — they refuse to run unless you provide a `client_slug`. Skills that are inherently agency-only (like `/design-retainer-offer`) only accept the agency scope. The skill itself enforces the boundary; you cannot accidentally invoke a client skill in agency scope.

---

## A worked example: ghostwriting a client post

You run:

```
/ghostwrite-client-post client_slug=acme
```

Here is what happens, in order:

**1. Scope lock.** The skill reads `client_slug=acme` from the request and binds it to the working context. From this moment, every profile path in the skill body is rewritten as `<profile>.<branch>[acme]` — never the agency-level path.

**2. Profile loads.** The workspace loads:
- `brand_voice.client_voices[acme]` — Acme's voice
- `ideal_customer_profile.client_icps[acme]` — Acme's ICP (who Acme sells to, not who you sell to)
- `content_strategy.client_content[acme]` — Acme's content engine, pillars, calendar
- `case_studies` — only entries tagged `client_slug=acme` and `use_in_content: true`
- `reference/verticals/{acme.vertical}.md` — vertical knowledge for Acme's space

**3. What does NOT load:** `brand_voice.agency_voice`. `ideal_customer_profile.agency_icp`. Any other client's profile. The agency case studies. The agency content engine. None of it. The drawer for Acme is open; every other drawer is shut.

**4. Threshold check.** Per `spec/CONTEXT-THRESHOLDS.md`, `/ghostwrite-client-post` requires Acme's voice profile at ≥85% completeness. If Acme's voice is at 60%, the skill refuses to execute and routes you to `/extract-founder-voice scope=client-voice client_slug=acme` to fill the gap. No exceptions.

**5. The post is drafted** using only the loaded context. Phrases from Acme's `phrases_to_use`. Hook formulas from Acme's hook library. Topics from Acme's calendar slot. The mechanism described matches Acme's mechanism, not yours.

**6. Voice-drift gate.** Before the draft is queued for review, the workspace runs a cosine-similarity check against Acme's last 20 validated posts. If the draft sounds more like the agency than Acme, it fails the gate and gets rewritten.

**7. Blind Output Test.** The draft is read by three simulated reviewers calibrated to Acme's audience (not yours). Pass = queued for client review. Fail = back to step 5 with the failure mode flagged.

**Now imagine the wrong scope was loaded.** The skill grabs `brand_voice.agency_voice` by mistake. The post comes out in your voice — your phrases, your mechanism framing, your story arc — published under Acme's name. Acme's audience reads it. They don't know why, but the post feels like a different person from Acme's last 20. Engagement drops. Three weeks later, after four more posts in the wrong voice, Acme says "this doesn't sound like me" and the engagement is over.

The recursive scope lock in step 1 is what prevents this. It is a 3-minute check that protects the entire client engagement.

---

## The voice-drift detection layer

Recursive context is necessary but not sufficient. The scope can be loaded correctly and the ghostwriter can still drift over time — picking up generic LinkedIn cadences, leaning on phrases that feel right but aren't in the client's profile, slowly converging toward the agency-mean voice.

That's what `/voice-drift-detector` is for. Every Monday at 09:00, the workspace runs this skill across every active client. It pulls the client's last 20 validated posts and the last 20 ghostwritten posts, computes pairwise cosine similarity, and breaks the result down across five axes (signature-phrase usage, sentence-structure variance, vocabulary do/don'ts, decision-frame consistency, story-shape consistency).

If the score crosses the client's drift threshold, the audit halts new ghostwriting for that client until the gap is closed — typically by re-running `/extract-founder-voice` to refresh a stale profile, or by recalibrating the ghostwriter against the reference set.

The scope load (per skill invocation) is the architectural defense. The drift detector (weekly per client) is the operational confirmation that the architecture is actually working in practice. Without the detector, the workspace assumes the scope load worked. With the detector, the workspace measures it.

See `skills/voice-drift-detector/SKILL.md` for the full audit protocol.

---

## Adding a new client

When a client signs, you do three things:

**1. Declare the slug.** Pick a kebab-case alias for the client (`acme`, `globex-saas`, `north-coast-coaching`). Use this slug in every internal path forever. The slug is anonymized — never a real name in any pushed artifact.

**2. Run `/onboard-client` with the slug.** This skill creates the empty per-client subprofiles and runs the four-skill foundations chain at client-scope across the first 30 days:
- Day 4-7: `/extract-founder-voice scope=client-voice client_slug=<slug>`
- Day 8-14: `/build-icp scope=client-icp client_slug=<slug>`
- Day 15-21: `/build-content-engine scope=client-content client_slug=<slug>`
- Day 22-30: `/build-outbound-engine scope=client-outbound client_slug=<slug>`

**3. Done.** By day 30, all four recursive subprofiles for the new client are at ≥70% completeness. Every client-bound skill (`/ghostwrite-client-post`, `/run-client-dm-ops`, `/build-client-report`) can now execute against this slug. Weekly drift audits start running automatically.

The recursive subprofiles auto-create from the schema in `ENCODING.md`. You do not write them by hand. You sit in interviews; the workspace populates the YAML.

A note on slug discipline. The slug is the only identifier the workspace uses internally for the client. It shows up in file paths (`outputs/2026-05-03/posts/clients/acme/...`), in skill invocations (`client_slug=acme`), and in the YAML keys for every per-client subprofile. Pick the slug carefully — once it propagates across artifacts and outputs, renaming is painful. Anonymized slugs are required (per `INVARIANTS.md` A-11), so do not use the client's real company name as the slug.

---

## Why this is the workspace's "1-of-1 primitive"

Most LinkedIn agency software treats voice as a global setting — one tone, one set of phrases, applied to every account the agency runs. That works for one client. It silently breaks at three. By seven, the agency's clients have all converged toward the same agency-mean voice, and the value the agency was selling — being a unique voice on LinkedIn — has been undone by the tooling that was supposed to scale it.

This workspace's recursion is the opposite. Voice is per-client by default. Cross-contamination is impossible without explicitly bypassing the scope rule (which the invariants block). The agency-mean voice doesn't form because no skill ever loads multiple voices at once.

That property — voice integrity per client at scale — is the workspace's core moat. Every other capability in the agency (DM ops, lead lists, reporting, retainer pitches) is something a competent agency can already do. None of them can do it across 10 clients without their voices drifting toward one shared center. This workspace can. That is what makes it a 1-of-1 primitive.

---

## Common confusion points + how the workspace catches them

| Confusion | What goes wrong | How the workspace catches it |
|---|---|---|
| "Can I ghostwrite for a client without setting up the subprofile?" | The skill would draft from `agency_voice` and ship a post in the wrong voice | `/ghostwrite-client-post` requires `brand_voice.client_voices[slug] ≥ 85`. Below threshold = halt. |
| "Can I share one voice profile across two similar clients?" | Both clients get a blended voice that fits neither; both eventually churn | The schema is keyed by `client_slug`. Two clients = two separate profiles. The workspace will not let you point one slug at another's profile. |
| "I forgot the client_slug in the request" | The skill might silently default to agency scope and leak the agency voice into client work | Skills with client-only scope refuse to execute without `client_slug`. The error message names the missing parameter. |
| "Can the agency case studies show up in a client post?" | You'd be using your wins to write the client's content, which is structurally wrong | `/ghostwrite-client-post` filters case studies by `client_slug=<slug>` AND `use_in_content: true`. Agency case studies do not match the filter. |
| "What if I want a hook from the agency hook library inside a client post?" | The hook would carry the agency's voice cadence and contaminate the client post | Each client has their own hook library inside their content engine. The agency hook library is not in scope when client-content is loaded. |
| "Can I edit a client's voice profile to make it 'more like the agency' for consistency?" | This is the drift failure mode dressed up as a feature. It produces a workspace where every client sounds like the agency | Don't. The whole point of recursive context is that the client sounds like the client. The drift detector exists to flag exactly this kind of contamination if it happens by accident. |

---

## Cross-references

- `ENCODING.md` — the 6-profile schema (the source of recursion)
- `SYSTEM.md` Sections 4 + 10 — the Recursive ICP Principle and the three-voice architecture
- `INVARIANTS.md` N-1, N-2, N-6, A-6 — the sacred rules that enforce scope discipline
- `spec/CONTEXT-THRESHOLDS.md` — per-skill, per-scope completeness gates
- `skills/extract-founder-voice/SKILL.md` — the skill that builds voice profiles at either scope
- `skills/build-content-engine/SKILL.md` — the skill that builds content engines at either scope
- `skills/ghostwrite-client-post/SKILL.md` — the canonical client-only skill (scope-lock + drift gate + BOT)
- `skills/voice-drift-detector/SKILL.md` — the weekly audit that confirms recursion is working
- `skills/onboard-client/SKILL.md` — the skill that creates per-client subprofiles
- `docs/THE_BAR.md` — the publish-bar gate that runs after recursive context loads correctly

---

*Doc version 1.0.0 — 2026-05-03*
