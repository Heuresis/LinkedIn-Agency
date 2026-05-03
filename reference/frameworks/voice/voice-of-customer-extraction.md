# Voice of Customer Extraction

> The structural rules for pulling verbatim ICP language from real conversations, distinct from the operator's guess at ICP language. VOC is the difference between content that converts and content that bounces.

## Why this framework exists

Most ICP profiles fail not because the firmographics are wrong, but because the language is invented. The operator writes "my ICP says they're frustrated with vanity metrics" — but no closed-won transcript shows that phrase. The DM sequence built on it doesn't resonate because the ICP doesn't actually talk that way. Conversion drops, and the operator concludes that DMs don't work.

VOC extraction is the discipline of populating ICP language fields from sources only — never from the operator's pattern-match of what the ICP "would say." The verbatim quote, with its source path, with its messy original phrasing intact, is the unit. Paraphrases are forbidden. Synthesis-of-many-paraphrases is also forbidden (it produces the bland generic-ICP voice that makes every B2B agency's content sound identical).

VOC drives downstream work that no other input can drive:
- **Hook library voice-fit scoring** — does the hook use phrasings the ICP actually uses, or phrasings the operator wishes the ICP used?
- **DM personalization slots** — the variable slots in DM templates pull from VOC quotes, not from operator paraphrase
- **Application question phrasing** — questions written in the ICP's own language get higher completion rates and richer free-text answers
- **Retainer proposal mirroring** — the proposal mirrors back the prospect's own pain language; mirrored proposals close at materially higher rates than translated proposals

This protocol is run by `/build-icp` (per the 12-question interview) and consumed by every downstream content + DM skill. It applies to both scopes:
- **agency-ICP VOC** — the language of the prospects who hire the agency
- **client-ICP VOC** — the language of the prospects who hire the client (recursive — built per active client)

## The 6 rules

### Rule 1 — Sources are real conversations, ranked by quality

VOC enters the bank from these sources only, ranked by signal quality (highest first):

1. **Discovery call transcripts** — best signal. The prospect is talking about their problem unprompted, in real time, to someone they're considering paying. The phrasing is honest, current, and conversion-relevant.
2. **Application form free-text answers** — strong signal. The prospect is writing (not speaking), so the cadence skews more deliberate, but the language is still self-generated and unmediated.
3. **DM thread captures** — strong signal for DM-channel work specifically. The phrasing the prospect uses to reply is the phrasing your next DM template needs to mirror.
4. **Client self-report on calls** — useful for refinement. The closed-won client describing their pre-purchase state is signal-rich, but the post-hoc framing has been shaped by the working relationship — discount slightly.
5. **Slack and email threads from existing clients** — the casual register surfaces idioms the formal register hides. Pull from the threads where clients are venting, escalating, or thanking.
6. **Churn exit interviews** — high-signal for the disqualifier and barrier categories specifically. The departing client knows what didn't work and will say it more directly than the active client.

Forbidden sources (per Rule 2):
- The operator's guess at what the ICP says
- The operator's translation of "what they probably mean"
- AI-generated example dialogue
- Composite quotes synthesized from multiple sources

### Rule 2 — The operator's guess at VOC is NOT VOC

This is the rule the protocol bends most often. The operator says, "they tell me they're tired of paying $4K/month for content that doesn't convert" — and the temptation is to file that as a VOC quote. It is not. It is the operator's *paraphrase* of what they remember a prospect saying.

Per `INVARIANTS.md` N-9 spirit: verbatim only. The unit of VOC is the quote *as the prospect said it,* not as the operator remembers it.

If the operator cannot produce the actual transcript, the quote does not enter the bank. It enters a separate `operator_paraphrase_pending_verification` list, and the next time a discovery call surfaces similar phrasing, the verbatim version replaces the paraphrase.

This is friction the operator notices. It is also the friction that separates VOC banks that drive conversion from VOC banks that read as plausible but produce flat output downstream.

### Rule 3 — VOC is categorized into 5 buckets

Every VOC quote enters the bank under one of five categories. The categories map to specific downstream uses:

- **pain** — the problem the ICP articulates, in their own words. Drives content-pillar pain-language and DM hook construction.
- **desire** — the outcome the ICP wants, in their own words. Drives offer-promise framing and value-stack anchoring.
- **barrier** — what has stopped the ICP from solving the problem already. Drives objection-rebuttal language and risk-reversal phrasing.
- **framing** — how the ICP describes their *situation* (not the pain, not the desire — the contextual frame). Drives application question phrasing and proposal mirroring.
- **banned-by-them** — phrases the ICP recoils from. Drives the inverse — what the agency must not say in this ICP's hearing. Often surfaced via "I hated when the last agency said..." quotes.

A quote that doesn't fit one of these five categories is either (a) miscategorized — re-read it, and (b) noise — drop it.

### Rule 4 — Minimum 30 verbatim quotes per ICP profile

Below 30, the bank is too thin to produce voice-fit scoring at the hook-library level (each pillar needs 3-5 quotes to draw from, across 4-5 pillars = ~15-20; plus DM personalization needs another 10+; plus application phrasing needs another 5).

Target distribution:
- Pain: 10+ quotes
- Desire: 8+ quotes
- Barrier: 5+ quotes
- Framing: 5+ quotes
- Banned-by-them: 3+ quotes

Total target: ~30-50 quotes per ICP. Below this, profile cannot cross 75% completeness on the VOC dimension; ICP profile completion stalls per the gating defined in `skills/build-icp/SKILL.md`.

If the operator has fewer than 5 closed-won deals (and therefore limited transcript sources), mark VOC as `bank_status: drafted` rather than `validated`. Re-run the protocol once 10 closed-won deals have generated transcript material.

### Rule 5 — VOC drives downstream artifacts on a defined map

VOC is not a research output that lives in a profile and stops there. Each category has named downstream consumers; each consumer pulls a defined slice on a defined cadence.

Downstream consumption map:

| Category | Consumed by | Used for | Cadence |
|---|---|---|---|
| pain | hook library, content-pillar mapping, DM cold-open construction | Hook copy that lands in the prospect's actual frame | Refresh on every new pain quote added |
| desire | offer copy, value-stack anchoring, transformation-hypothesis language | Offer language that mirrors the prospect's articulated outcome | Refresh on offer-cycle (quarterly) |
| barrier | objection bank, risk-reversal phrasing, guarantee language | Sales-call rebuttals, retainer-proposal addenda | Refresh on every closed-lost analysis |
| framing | application question phrasing, proposal mirroring, discovery-call opener | The contextual frame that signals "we get your world" | Refresh on application-form review (monthly) |
| banned-by-them | `phrases_to_avoid` for client-bound work; competitor-positioning language | What the agency must never sound like for this ICP | Refresh on every churn exit interview |

The map exists so that adding a new VOC quote triggers the right downstream re-evaluations automatically. A new pain quote should prompt a hook-library audit; a new banned-by-them quote should prompt a `phrases_to_avoid` update.

### Rule 6 — Refresh cadence: weekly capture, quarterly synthesis

VOC banks decay slowly (slower than story banks — language stays current longer than narrative relevance), but they decay. The cadence:

- **Weekly capture** — every new discovery call, every new application response, every new DM thread that closes or escalates, gets a 5-minute pass for new VOC quotes. Captured into `voice_of_customer.raw_intake.{date}` for queue.
- **Quarterly synthesis** — at the start of each quarter, the raw intake gets reviewed: new quotes promoted into the categorized bank, stale quotes (no longer matching observed prospect language) culled, distribution rebalanced (if pain has grown to 25 quotes and barrier is still at 4, the bank is unbalanced — re-source).

Off-cadence triggers that force an immediate refresh:
- Vertical pivot (the language of a new vertical does not match the language of the old)
- Offer pivot (a new offer surfaces new pain, desire, and barrier patterns)
- 3+ consecutive lost deals on the same dimension (signal that the barrier or banned-by-them bank is missing a key entry)
- Operator reports "the language on calls feels different lately" (subjective signal, but worth a synthesis pass)

## Output template

VOC entries hydrate the `voice_of_customer` block inside the ICP profile per `ENCODING.md` Profile 3.

```yaml
ideal_customer_profile.{scope}.voice_of_customer:
  bank_status: ""                              # drafted | validated | proven-by-results
  total_quote_count: 0
  category_distribution:
    pain: 0
    desire: 0
    barrier: 0
    framing: 0
    banned_by_them: 0
  last_synthesis_date: ""                      # YYYY-MM-DD
  last_capture_date: ""                        # YYYY-MM-DD

  pain_language_patterns:
    - quote: ""                                # verbatim, exact wording, exact punctuation
      source_type: ""                          # discovery-call | application | dm-thread | client-call | slack | email | exit-interview
      source_path: ""                          # path to the transcript or artifact
      source_date: ""                          # YYYY-MM-DD
      speaker_descriptor: ""                   # descriptors only, never names — per INVARIANTS A-11
      annotation: ""                           # optional one-line note on what this quote signals

  desire_language_patterns: []                 # same shape as pain_language_patterns
  barrier_language_patterns: []                # same shape
  framing_language_patterns: []                # same shape
  banned_by_them: []                           # same shape — the phrasing the ICP recoils from

  operator_paraphrase_pending_verification:    # the holding queue from Rule 2
    - paraphrase: ""
      operator_recall_date: ""
      verification_status: ""                  # awaiting-source | verified-and-promoted | dropped-as-noise
```

Every quote field is verbatim — no cleanup, no punctuation correction, no "for clarity" edits. The messy original is the asset; the cleaned-up version is contamination.

## Failure modes

| Failure | Symptom | Root cause | Fix |
|---|---|---|---|
| Bank populated by operator paraphrase | Quotes read as too-clean, too-on-message; downstream content doesn't resonate | Rule 2 was bent — operator's recall got filed as VOC | Per Rule 2, move all unverified entries to `operator_paraphrase_pending_verification`; re-source from actual transcripts |
| Synthesis-of-many-paraphrases | "Composite ICP voice" reads as plausible but flat — no actual prospect would say it that way | VOC was written instead of captured | Halt the bank; re-capture from real transcripts only; discard composites |
| Bank unbalanced across categories | 30+ pain quotes, 2 barrier quotes, 0 banned-by-them | Capture happens reactively from discovery calls (which over-index on pain) | Run targeted re-capture: pull 5 churn exit interviews for barrier + banned-by-them; pull 5 application forms for framing |
| Names in the bank | Real prospect names appear in `speaker_descriptor` | Rule 4 of `story-bank-seeding.md` (descriptor convention) was not enforced for VOC | Audit speaker_descriptor field for capitalized proper nouns; replace with role + vertical descriptors |
| Stale bank | Hook library underperforms despite voice-fit scoring "passing" against the bank | Bank not refreshed in 6+ months; ICP language has drifted | Run Rule 6 quarterly synthesis immediately; cull stale quotes; expand from last 30 days of calls |
| Verbatim cleanup applied | Quotes have corrected grammar, removed filler words, polished punctuation | Operator (or interviewer) "tidied up" the quote during capture | The mess is the asset — re-capture from raw transcript; the "um" and the trailing "...you know what I mean?" are signal |
| Bank used as static reference instead of active driver | New quote added but downstream artifacts (hooks, DMs, proposals) never re-evaluated | The downstream consumption map (Rule 5) was treated as documentation, not workflow | Wire the trigger: every commit to the VOC bank fires the relevant downstream audit per the map |
| Wrong scope confusion | Agency-ICP VOC quotes used to write client-bound posts (or vice versa) | Recursive principle skipped; bank loaded by default instead of by scope | Per `INVARIANTS.md` A-6: client-bound work loads the client subprofile VOC, never the agency VOC |

## Cross-references

- `skills/build-icp/SKILL.md` — Step 3 (voice-of-customer extraction) runs this protocol
- `agents/icp-builder.md` — owns the VOC bank; resists operator-fantasy quotes; enforces source-citation
- `reference/frameworks/voice/voice-extraction-9q.md` — sibling protocol; voice extraction surfaces operator voice, VOC extraction surfaces ICP voice. Adjacent fields, never collapsed.
- `reference/frameworks/voice/story-bank-seeding.md` — sibling protocol; VOC quotes and stories live in adjacent fields, not the same field
- `ENCODING.md` Profile 3 — `ideal_customer_profile.{scope}.voice_of_customer` schema
- `INVARIANTS.md` N-3, N-9, A-6, A-11 — never invent, never claim final, recursive subprofile loading, descriptors only
- `skills/build-application/SKILL.md` — consumes the framing and barrier banks for question phrasing
- `skills/build-outbound-engine/SKILL.md` — consumes the pain and barrier banks for DM construction

---

*Framework version 1.0.0 — 2026-05-03*
