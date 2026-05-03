# Quickstart — 30 Minutes to a Working LinkedIn Agency

> **What this is.** A LinkedIn agency that runs on AI agents — content, DMs, sales, fulfillment, and a high-ticket program. The agents carry your judgement, not someone else's playbook. **Who it's for.** B2B founders, agency operators, and consultants who want a working LinkedIn machine without hiring six people. **What you'll have in 30 minutes.** A workspace your AI tool reads, a voice profile in flight, an ICP draft, and a first post in your queue. The deeper compounding (drift detection, client subprofiles, the program tier) builds from there.

---

## Prerequisites — the 4 things you need

Before you start, gather these. Most you already have.

1. **A LinkedIn account.** Personal profile. Posting access. That's it.
2. **An AI runtime.** One of:
   - **Claude** (claude.ai or the Claude desktop app) — recommended
   - **ChatGPT** (chat.openai.com or the desktop app)
   - **Cursor** (cursor.sh — for those who want it inside an editor)
   - Any other AI tool that can read files in a folder
3. **A markdown editor.** Anything that opens `.md` and `.yaml` files:
   - Free options: **VS Code**, **Sublime Text**, **Notepad** (Windows), **TextEdit** (Mac)
   - Slightly nicer: **Cursor**, **Obsidian**, **Notion** (paste-into)
4. **A folder on your computer** where you can keep this workspace. Anywhere is fine. `~/Documents/linkedin-agency/` works.

**Optional (you can add these later):**
- A CRM (HubSpot, GoHighLevel, Close, Notion — anything)
- A DM tool (LinkedHelper, Heyreach, ManyChat — for when you scale outbound)
- A scheduler (Calendly, Savvycal — for discovery calls)

You do **not** need: a developer, an API key, a server, code skills, or anything that runs in the terminal beyond one optional command.

---

## Step 1 — Get the workspace onto your computer (5 min)

Three ways. Pick the one that matches you.

### Way A — Just download the ZIP (easiest, no tools)

1. Go to the GitHub page: `https://github.com/Heuresis/LinkedIn-Agency`
2. Click the green **`Code`** button (top right of the file list).
3. Click **`Download ZIP`**.
4. Unzip it. Move the folder to wherever you want it (e.g. `~/Documents/linkedin-agency/`).

Done. Skip to Step 2.

### Way B — GitHub Desktop (visual, no terminal)

1. Install **GitHub Desktop**: `https://desktop.github.com`
2. Open it. **File → Clone Repository → URL** tab.
3. Paste: `https://github.com/Heuresis/LinkedIn-Agency`
4. Pick a local folder. Click **Clone**.

Done.

### Way C — Terminal (for engineers)

```bash
git clone https://github.com/Heuresis/LinkedIn-Agency.git
cd LinkedIn-Agency
```

Done.

---

## Step 2 — Open the workspace inside your AI tool (3 min)

The workspace is a folder of markdown and YAML files. Your AI reads them as context. How you do that depends on the tool.

### If you use Claude (desktop or web)

**Claude Desktop (recommended):**
1. Open **Claude Desktop**.
2. Click the **`+`** (attach) button under the chat box.
3. Select **`Add from folder`** and pick the workspace folder.
4. Type: `Read SYSTEM.md, INVARIANTS.md, ENCODING.md, and company.yaml. You are now the LinkedIn Agency operator. Confirm boot complete.`

**Claude on the web:**
1. Open `claude.ai`.
2. Drag and drop the key files into the chat: `SYSTEM.md`, `INVARIANTS.md`, `ENCODING.md`, `company.yaml`, plus the `agents/_INDEX.md` and `skills/_INDEX.md`.
3. Type the same boot prompt as above.

### If you use ChatGPT

1. Open `chat.openai.com`.
2. Click the **paperclip** under the message box. Upload the same five core files.
3. Type the boot prompt.
4. **Note:** ChatGPT has tighter file limits than Claude. If it complains, upload only `SYSTEM.md`, `INVARIANTS.md`, `ENCODING.md`, `company.yaml`, and reference the rest as needed.

### If you use Cursor

1. Open Cursor. **File → Open Folder** → pick the workspace.
2. Open the AI chat panel (Cmd-L on Mac, Ctrl-L on Windows).
3. Type: `@SYSTEM.md @INVARIANTS.md @ENCODING.md @company.yaml — boot the workspace. You are the LinkedIn Agency operator now.`

Cursor reads files automatically when you `@`-mention them.

### If you use something else

Any AI tool that reads files works. Tell it to read `SYSTEM.md` first — that's the boot file. Everything else cascades from there.

---

## Step 3 — Fill in the bare minimum of `company.yaml` (5 min)

`company.yaml` is where YOUR business context lives. The full file has dozens of fields — you do **not** need to fill them all today. Just the bootstrap five.

Open `company.yaml` in your markdown editor. Find these fields and fill them in:

```yaml
business_context:
  basic_info:
    agency_name: "Your Brand Name"
    operator_name: "Your Name"
    industry_focus: "B2B Marketing"          # or Lead Gen / Personal Brand / your vertical
    business_model: "DFY-only"                # or Hybrid (DFY + Program), or Program-only
  positioning:
    unique_selling_proposition: "One sentence on what only you can claim."

product_strategy:
  service_tier:
    offers:
      - offer_name: "Your Primary Offer"
        offer_type: "DFY content + DM"        # or whatever you actually sell
        price_point_monthly: 5000              # rough number is fine
```

That's it for now. Save the file.

The other 90% of the file gets built **by running the skills** — interview-mode fills it in for you. You don't need to know what any of those fields mean yet.

---

## Step 4 — Run your first skill: `/extract-founder-voice` (90 min, splittable)

This is the most important skill in the workspace. It builds your voice profile — the thing every future post, DM, and email pulls from.

In your AI chat, type:

```
/extract-founder-voice
```

The agent (`voice-extractor`) will ask you for two things:

1. **20-50 of your own posts.** LinkedIn posts you've written. Paste them in (or upload as a text file). Most-engaged 20 + most-recent 20 + a few that flopped.
2. **3-5 transcripts of you talking unguarded.** Sales call recordings, podcast clips, voice notes, internal Looms. Anything where you talk like yourself.

Then it runs a **9-question interview**. Answer in your own words. Don't perform. The interview takes ~45 minutes if you go straight through.

**You can split this across two sessions.** Type `pause /extract-founder-voice` to save progress. Resume by typing `resume /extract-founder-voice`.

Output: a voice profile written into `company.yaml` under `brand_voice.agency_voice`. It's now load-bearing — every future post pulls from it.

---

## Step 5 — Run `/build-icp` (60 min)

Now your ideal customer profile.

```
/build-icp
```

The agent (`icp-builder`) will interview you on:

- Who you sell to (role, company size, vertical)
- The pains they actually say out loud (not the polite version)
- The objections that kill deals
- The signals that mean someone is about to buy
- The competing solutions they've tried and abandoned

If you've worked with even **one** good-fit client, this goes faster — pull in their messages, sales call notes, anything. The skill prefers verbatim language over your own summarization. That's the whole point.

Output: `ideal_customer_profile.agency_icp` populated in `company.yaml`.

---

## Step 6 — Run `/design-retainer-offer` (45 min)

You now have voice + ICP. The offer skill uses both.

```
/design-retainer-offer
```

The agent (`offer-architect`) will walk you through:

- Core promise (what specifically changes for this client in N months)
- Unique mechanism (the "why this works" — not generic, named)
- Deliverables per month (with quality bars, not just volume)
- Scope guardrails (what's out of scope, in writing)
- Value stack (each piece anchored to a dollar number)
- Bonuses (each one defeats a specific objection)
- Guarantee (real risk reversal, not weasel words)

Output: a complete retainer offer document in `outputs/{date}/design-retainer-offer/`. You can use it as a sales asset day one.

---

## Step 7 — Run `/build-content-engine` for your own LinkedIn (60 min)

Time to build the engine that drives your own LinkedIn presence.

```
/build-content-engine
```

The agent (`content-strategist`) will produce:

- **Content pillars** (3-5 themes you'll post on, each tied to your offer)
- **Hook library** (50+ hook templates calibrated to your voice)
- **Post types** (authority post, personal story, mechanism breakdown, etc.)
- **Posting calendar** (frequency + time blocks + what goes where)

Output: a full content engine in `outputs/{date}/build-content-engine/`.

---

## Step 8 — Ship your first post (15 min)

```
/write-linkedin-post
```

The agent (`linkedin-ghostwriter`) will:

1. Ask what you want to post about (or pull from your content engine).
2. Pick a hook from your hook library.
3. Write a draft in **your voice**, citing your phrases-to-use and avoiding your phrases-to-avoid.
4. Run the **Blind Output Test** — would three people in your ICP say "this came from a real operator," not "this is AI"?
5. Show you the draft with confidence + gap flags.

Edit. Approve. Post it.

That's your first shipped post. The workspace logs it, watches what hits, and gets sharper from your acceptance/rejection signal.

---

## What's next

You now have the wedge — voice, ICP, offer, content engine, first post. From here, the workspace expands in three directions.

- **Understand the system.** Read [`docs/ARCHITECTURE.md`](ARCHITECTURE.md) — how all the pieces fit. Useful before you customize anything.
- **Add a client.** When you sign your first DFY client, the workspace builds a per-client subprofile (their voice, their ICP, their content). See the **Recursive Context** section of `ENCODING.md` and run `/onboard-client`.
- **Understand the publish bar.** Read [`spec/BLIND-OUTPUT-TEST.md`](../spec/BLIND-OUTPUT-TEST.md) — the gate every client-bound artifact must clear. This is what keeps the work from drifting toward "good enough commodity agency" output.

When you're ready to add outbound:

```
/build-outbound-engine
```

When you're ready to systematize discovery calls:

```
/build-call-prep
```

When you're ready to add a high-ticket program for other operators:

```
/design-program-offer
```

Full skill list: `skills/_INDEX.md`.

---

## Common stuck points (and how to unstick)

**1. "The AI keeps producing generic LinkedIn content."**
Your voice profile isn't built yet, or it's at low completeness. The agent should refuse to write client-bound posts when voice is below 80%. If it's writing anyway, paste this: `Audit my brand_voice.agency_voice completeness and refuse to ghostwrite if below 80%.`

**2. "It's asking me to upload 50 posts but I only have 8."**
Fine. Use what you have. The skill produces a v0.5 draft profile. You can sharpen it later as you publish more. Tell the agent: `I only have 8 posts. Build a v0.5 draft, mark it for upgrade.`

**3. "I don't have any client work yet — can I still do this?"**
Yes. Skip the per-client steps. Run the agency-side wedge (Steps 4–8). Add client subprofiles when you actually sign clients.

**4. "My AI tool isn't following the skill — it's just freestyling."**
Re-anchor. Type: `Re-read SYSTEM.md and INVARIANTS.md. You must follow the skill instructions in skills/{skill-slug}/SKILL.md exactly. Do not invent steps. Do not skip steps.`

**5. "It's using phrases like 'game-changer' and 'let's dive in.'"**
That's what `spec/BANNED-VOCABULARY.md` exists to prevent. Tell the agent: `You're violating BANNED-VOCABULARY. Re-read spec/BANNED-VOCABULARY.md and rewrite without any banned phrases.`

**6. "I filled in `company.yaml` and now nothing recognizes the new values."**
Your AI chat is on cached state. Open a new chat. Re-read `company.yaml` first. (In Cursor, just `@company.yaml` again.)

**7. "I'm overwhelmed — there are too many skills."**
Run only the wedge: `/extract-founder-voice` → `/build-icp` → `/design-retainer-offer` → `/build-content-engine` → `/write-linkedin-post`. Ignore everything else for the first 30 days. Compound on the wedge before expanding.

---

*If you stay stuck for more than 20 minutes on any step, you're doing it the hard way. Skip ahead, mark the step as `pending`, and come back. The workspace is forgiving — it gets sharper from every cycle, not from any one perfect run.*

*Built by Heuresis. heuresis.ai*
