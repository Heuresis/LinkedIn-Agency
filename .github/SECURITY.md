# Security

## Data contract — INV-11

LinkedIn Marketing Agency is structured around a hard split between **template structure** (shippable) and **encoded operator + client content** (never shippable).

| Layer | What lives there | Safe to commit? |
|---|---|---|
| `SYSTEM.md`, `INVARIANTS.md`, `ENCODING.md`, `spec/`, `skills/*/SKILL.md`, `agents/`, `reference/` | Heuresis-authored template structure | Yes |
| `company.yaml` (template form, all fields empty) | Empty scaffold | Yes |
| `company.yaml` (filled with operator + client values) | Operator + client IP | **No** |
| `_private/*` (auto-created on first use) | Operator research, draft posts, voice transcripts | **No** |
| `outputs/*` | Generated artifacts per operator + per client | **No** |
| `.env` / `.env.local` | Credentials | **No** |
| `.env.template` | Variable names only, no values | Yes |

The `.gitignore` enforces this split automatically. Do not remove the `.gitignore` entries under "Operator-encoded private data," "Per-client encoded data," or "Credentials" without explicit approval.

## Credentials

- Copy `.env.template` to `.env.local` before filling values.
- Never commit a file that contains a real API key, access token, OAuth secret, LinkedIn cookie, Sales Navigator session token, or client password.
- Rotate any credential that touches this repository if it is ever mistakenly committed, even after the commit is deleted — Git history retains removed values.
- Store long-lived secrets in the operator's preferred secret manager (1Password, Doppler, Infisical, AWS Secrets Manager). The `.env.local` file is a developer-ergonomic local copy, not the source of truth.

## Per-client data

This workspace runs on **recursive context** — there is one context for the agency itself AND a separate context for every active client. Per-client subprofiles live under `company.yaml` paths like `brand_voice.client_voices[slug]` and `ideal_customer_profile.client_icps[slug]`.

When a new client is added:

1. The client_slug is generated from a non-PII alias (never the client's full company name in identifiers used in commits, file names, or shared artifacts).
2. The client's voice transcripts, post drafts, application responses, and call recordings live under `_private/clients/{slug}/` and never commit.
3. The client's monthly reports, generated artifacts, and lead lists live under `outputs/clients/{slug}/` and never commit.
4. The client's signed contract, invoice records, and payment data live in the operator's CRM / billing system, not in this repo.

When a client offboards:

1. Archive `_private/clients/{slug}/` and `outputs/clients/{slug}/` to the operator's long-term storage.
2. Set `company.yaml: clients[slug].status: offboarded` and `voice_drift_audit_active: false`.
3. Do not delete the encoded subprofile — it is a case-study source for content + program tier curriculum.

## When forking the template for a new operator

1. Reset `company.yaml` to its empty-template form before initializing the new workspace.
2. Start `_private/` empty (only `.gitkeep`).
3. Start `outputs/` empty (only `.gitkeep`).
4. Verify `.gitignore` is present and unchanged.
5. Run `git log` against the new workspace to confirm no operator data has been accidentally pulled in from a prior fork.

## Reporting a vulnerability

If a security issue is discovered in this template — for example, a skill that leaks context it should not, an adapter that exposes a credential, a `.gitignore` gap, or a real client name accidentally committed — write to **security@heuresis.ai** with a reproduction and the severity you believe applies.

Do not open a public issue for security reports. Internal triage happens first.

## Supply chain

- This template has no runtime dependencies. It is markdown and YAML.
- When a runtime (slash-command runtime, the workspace manifest, agent SDK, custom HTTP adapter) loads the workspace, the runtime's own security posture applies on top.
- Third-party DM-automation tools, Sales Navigator scrapers, and ManyChat integrations should be audited before production use. Per LinkedIn's terms, some automation tools carry account-suspension risk — the operator chooses the risk tolerance, not this repo.
- MCP servers and external integrations referenced in any skill adapter should be audited before production use. Heuresis does not own or guarantee third-party MCPs.

---

*LinkedIn Marketing Agency — a Heuresis workspace template.*
