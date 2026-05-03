# Hook Anatomy

> The first 7-12 words of a LinkedIn post determine whether the next 12 readers see the next 12K. Hooks are the highest-leverage block of content the workspace produces.

## What a hook does

A LinkedIn hook performs three jobs in <12 words:

1. **Earns the click on "...see more"** — without this, dwell time is 1.5 seconds and the algorithm stops surfacing
2. **Sets the implicit promise** — what payoff the reader gets if they read on
3. **Filters for the right reader** — irrelevant readers should bounce; relevant readers should lock in

A hook that earns clicks but mispromises produces high-impressions/no-conversion content. A hook that filters too narrowly produces high-resonance/low-impressions content. The balance is calibrated per pillar in `/build-content-engine`.

## The 9 hook formulas (anatomy reference)

### 1. Contrarian-reframe hook

**Formula:** "Most {role}s do {common practice}. The ones who win do {contrarian practice}."

**Why it works:** Triggers System-1 pattern-break. Reader's expectation: "another LinkedIn tip." Reality: a stake about who's wrong. Click rate elevated for the same reason a contrarian podcast title outperforms a how-to title.

**When to use:** Authority pillar. When the post will deliver an actually-contrarian take with evidence. Misuse (faking contrarian for engagement) burns trust quickly.

**Example structures (anonymized):**
- "Most B2B founders who write LinkedIn content focus on what their product does. The ones who get inbound calls focus on what their buyer is afraid of."
- "Most {role}s think their bottleneck is {visible thing}. It's almost always {invisible thing}."

### 2. Specific-stakes hook

**Formula:** "I {lost / spent / wasted} {specific number} on {specific thing} because {specific mistake}."

**Why it works:** Specificity overrides skepticism. "Lost $40K" is processed by the brain differently than "lost a lot of money." Specific numbers are credibility currency.

**When to use:** Personality + Proof pillar. Failure stories. Lesson-from-loss content.

**Example structures (anonymized):**
- "I spent $87,000 on Facebook ads in 2023 before I figured out what 90% of B2B agencies still get wrong about LinkedIn."
- "We lost a $40K client in week 6 because of a single line in our onboarding email."

### 3. Named-mechanism hook

**Formula:** "The {named mechanism} that {specific outcome}."

**Why it works:** Names create proprietary feel. "The 40/40/20 framework" reads as IP; "a framework I use" reads as commodity. Names compound — once a name is established, every reference reinforces authority.

**When to use:** Authority + Proof pillar. When the operator owns or is willing to own the name.

**Example structures (anonymized):**
- "The Recursive ICP Method that doubled our agency's close rate in 60 days."
- "The Intent Taxonomy that took our cold DM reply rate from 4% to 11%."

### 4. Before-after hook

**Formula:** "{Time period} ago, {bad state}. Today, {good state}. Here's the thing that changed."

**Why it works:** Transformation arc activates Pride emotion (in the writer) and Aspiration (in the reader). Time-stamp creates concreteness. The "thing that changed" is a payoff promise.

**When to use:** Proof pillar. Personal transformations. Client transformations (with permission).

**Example structures (anonymized):**
- "18 months ago I was charging $1,500/month for the same work I now charge $8,500 for. The pricing didn't change because I got better. It changed because I found one positioning lever."
- "6 months ago this client was getting 3 inbound calls per quarter. Last month: 14 calls in one week. Here's what we did differently."

### 5. List-promise hook

**Formula:** "{Number} {things} I learned from {credible source/experience}."

**Why it works:** Lists create implicit contract — "if I read on, I get N things in finite time." Credibility anchor (the source) lifts perceived value.

**When to use:** Authority + Proof pillar. When the list is genuinely 3-7 items deep, not stretched.

**Example structures (anonymized):**
- "5 things I learned about B2B sales from 1,200 discovery calls."
- "7 patterns I noticed running LinkedIn DM ops for 23 SaaS founders."

**Misuse warning:** "10 things every entrepreneur should know" is generic-list theater. Specificity is required (number AND source).

### 6. Pattern-break hook

**Formula:** "Stop doing {common practice}. Do {contrarian practice} instead."

**Why it works:** Imperative voice cuts through scroll. Negative-then-positive structure mirrors how the brain processes change recommendations.

**When to use:** Authority + Pier pillar. Provocative when warranted; tedious when overused. Limit to ~10% of pillar mix.

**Example structures (anonymized):**
- "Stop ending your LinkedIn posts with 'thoughts?' Replace it with a specific binary question."
- "Stop sending 50 cold DMs a day. Send 12 with the next-message scripted."

### 7. Loop-open hook

**Formula:** "{Event happened}. {Tension/question raised}. {Implicit promise of resolution}."

**Why it works:** Open-loop creates the same psychological pull as a TV show cold-open. Reader has to keep reading to close the loop.

**When to use:** Personality + Proof pillar. Stories. Case studies. Anything narrative-shaped.

**Example structures (anonymized):**
- "A client texted me at 11 PM Friday: 'I'm pulling the program. Refund my last $4K.' Here's what happened next."
- "Yesterday a B2B founder told me my entire playbook was wrong. He was 60% right."

### 8. Question-stake hook

**Formula:** "{Specific question that the right reader has been asking themselves}?"

**Why it works:** Direct mirror to the reader's internal monologue. Hits like a slap when targeted; bounces when generic.

**When to use:** Authority + Personality pillar. When the question is specific enough that the wrong reader doesn't recognize themselves.

**Example structures (anonymized):**
- "Why is your discovery call closing at 18% when your reply rate is 12%?"
- "What do you do when your best client says 'I love what you do but it's too expensive'?"

### 9. Insider-secret hook

**Formula:** "Here's something {credibility group} won't tell you about {topic the reader cares about}."

**Why it works:** Curiosity gap + insider positioning. Reader self-identifies as on the inside by reading on.

**When to use:** Authority pillar. Sparingly. Loses power when overused (becomes the "I'll tell you the secret" template).

**Example structures (anonymized):**
- "Here's something LinkedIn ghostwriting agencies won't tell their clients: the first 3 posts you ship will get below-average engagement, and that's by design."

## Hook scoring rubric

Every hook the workspace generates is scored on five axes (1-5):

| Axis | What it measures | Pass |
|---|---|---|
| **Stop-the-scroll** | Would the reader stop on this in their feed? | ≥4 |
| **Specificity** | Are the numbers, names, and references specific? | ≥4 |
| **Voice match** | Does this sound like the subject? | ≥4 |
| **Promise alignment** | Does the body deliver on what the hook implies? | ≥4 |
| **Filter strength** | Does this hook attract the right reader and repel the wrong one? | ≥3 |

`/score-hook` runs the rubric. Below pass on any axis = rewrite.

## The decay problem

Hooks decay. A hook formula that generated 80K impressions in Q1 generates 12K in Q3 because the audience has pattern-matched it. Two responses:

1. **Quarterly hook library refresh** via `/audit-hook-library-decay`. Sunset hooks below historical baseline by ≥40%. Promote new hooks above baseline by ≥30%.
2. **Hook-formula rotation.** Don't ship the same formula 3 weeks in a row. Calendar template enforces variety.

## Cross-references

- `skills/build-content-engine/SKILL.md` — populates hook library per subject
- `skills/build-hook-library/SKILL.md` — dedicated hook-build skill (Cycle 2)
- `skills/score-hook/SKILL.md` — scoring runtime
- `reference/swipe-file/hooks/` — annotated example hooks by formula
- `reference/platforms/linkedin/algorithm-notes.md` — quarterly LinkedIn behavior updates
- `INVARIANTS.md` N-4 — banned vocabulary

---

*Framework version 1.0.0 — 2026-05-03*
