---
platform_name: X (formerly Twitter)
primary_use: Secondary distribution surface. Cross-post compounding from LinkedIn for top-decile content; native primary surface for the subset of operators whose ICP lives on X (technical founders, dev-tools buyers, AI-adjacent operators).
integration_with_workspace: Optional but supported. The workspace ships `/repurpose-to-x` as a first-class skill in the marketing cluster. When an operator declares X as a tracked platform in `company.yaml::content_strategy.platforms`, the agency-content engine produces X-aware artifacts in addition to LinkedIn-native ones. Removing X support degrades cross-platform compounding for ~20-30% of operators but does not invalidate the workspace.
owned_by_agent: marketing-head (cross-post strategy, repurpose triage), content-strategist (per-artifact X repurpose execution)
---

# X — Platform Pack

> The secondary platform for cross-post compounding. The workspace's default position: LinkedIn is primary; X is amplifier OR native channel for a specific ICP subset. This pack documents what X rewards in 2026, why a B2B LinkedIn agency invests in X selectively, and how the workspace's repurpose layer adapts LinkedIn-native artifacts into X-native ones without LinkedIn-cadence contamination.

## Why this platform matters (for a B2B LinkedIn agency)

X is not the primary acquisition channel for a B2B LinkedIn agency. It is, however, a structural accelerant for three operator types and a baseline cross-post surface for the rest.

**1. Audience overlap is partial, not total.** ~60-70% of an operator's LinkedIn audience does not have an active X presence; the inverse also holds. Cross-posting from LinkedIn to X reaches a net-new audience segment of ~30-40% — meaningful enough to justify the marginal effort on top-decile LinkedIn posts, not enough to justify X-as-primary for most B2B agency operators.

**2. Some ICPs are X-native.** Technical founders, dev-tools buyers, AI-adjacent operators, and the venture-tech adjacent slice of B2B SaaS live more on X than on LinkedIn. If the agency's ICP intersects this subset, X becomes a primary platform for that ICP and the cross-post arrow can flip — X drives discovery, LinkedIn closes. Operators serving this ICP run a Year-1 dual-primary strategy (LinkedIn + X) rather than the default LinkedIn-primary strategy. Per `/build-icp`, the X-nativity check is a profile signal flagged in the operator's ICP file.

**3. Thread format compounds differently.** LinkedIn long-form posts compound via dwell time + comment depth in the first 60 minutes. X threads compound via bookmark velocity, quote-tweet engagement, and the algorithmic preference for completed-thread reads. The mechanisms are different enough that the same idea, formatted for each platform, produces non-redundant audience exposure. This is the structural argument for cross-post compounding even when ICP overlap is low.

**4. Faster news cycle, harder pattern-break.** X moves on a multi-hour cycle; LinkedIn on a multi-day cycle. X is where the operator catches and rides news-cycle moments (industry shifts, product launches, contrarian takes on viral threads) within 2-4 hours of the news. LinkedIn's same-news-cycle response window is 24-48 hours. For operators whose Authority pillar leans on real-time commentary, X is the faster surface; LinkedIn is the canonical archive.

The workspace's default posture: cross-post the top ~25% of LinkedIn posts to X via `/repurpose-to-x`. Operators with X-native ICPs (or X presence ≥10K followers) escalate to a true X-primary strategy with daily X-original content. Per `INVARIANTS.md` A-1, the Three-Lens Audit determines which posture applies before the operator invests effort.

## Platform mechanics

X's algorithm + UI + audience norms differ from LinkedIn on six specific dimensions.

**Algorithm primary lever: engagement velocity in first 30 minutes.** X measures engagement velocity (replies + retweets + bookmarks + likes per minute) in the first ~30 minutes as the dominant ranking signal. A tweet that draws 20 engagements in the first 5 minutes locks a stronger trajectory than a tweet that draws 80 engagements over the first 4 hours. This is the inverse of LinkedIn (which rewards dwell time accumulated over 60+ minutes). Posting time discipline matters even more on X — publishing into a window where the operator's audience is active drives 3-5x the engagement velocity of off-window posting.

**Retweet vs. quote-tweet weighting.** Retweets (no commentary) count moderately. Quote-tweets (with commentary from the sharer) count heavily — the algorithm reads quote-tweets as conversation-extension signal and surfaces the original to the quoter's network with implicit endorsement. Posts that draw quote-tweets compound 3-5x more than posts that draw equivalent retweet volume. This is why contrarian-thesis tweets and specific-number tweets outperform on X — both invite quote-tweet response.

**Thread vs. single-tweet decision.** X's algorithm reads threads as a composite unit. Completion rate (% of readers who reach the final tweet) is a strong ranking signal. A tightly-paced 6-tweet thread that 70% complete will outdistribute a meandering 12-tweet thread that 25% complete, even with identical content quality. The decision tree in `/repurpose-to-x` Step 2 is engineered against this — default to the more constrained format.

**Bookmark as the dark-social signal.** X surfaces bookmark counts now (since 2023 redesign). High bookmark count is the dark-social signal that the post produced perceived value but didn't earn in-feed engagement. Bookmark-heavy posts get re-surfaced to followers of the bookmarker — the algorithm reads "this earned save-for-later, surface to similar profiles." Bookmark-bait CTAs (per `/repurpose-to-x` Step 5) lean into this mechanism.

**No professional-context buffer.** The reader on X arrives in entertainment + news + community mindset, not professional-content mindset. The hook bar is harder — the reader is not pre-leaning-in. This is why hook anatomy on X requires 4-9 word hooks vs. LinkedIn's 7-12, and why role-name prefixes ("As a B2B founder...") get cut entirely. The buffer LinkedIn provides for free, X requires the operator to earn in the first 4 words.

**Mobile-vs-desktop reading split.** ~85-90% of X engagement happens on mobile (higher than LinkedIn's 75-80%). The character cutoff is harder — 280 chars max per tweet, no see-more expansion within the tweet itself (the entire tweet renders inline). Threads are the longer-form workaround, but each tweet stands as a self-complete unit. This is why every body tweet in a thread does ONE thing (per `/repurpose-to-x` Step 4) — multi-purpose tweets dilute and the algorithm reads diluted tweets as low-quality.

## Hook anatomy on X

The 9 hook formulas in `reference/frameworks/content/hook-anatomy.md` are LinkedIn-calibrated. X requires per-formula tightening per the rules below. The formula identity is preserved; the language tightens.

**Tightening rules (per `/repurpose-to-x` Step 3):**

- Word count: LinkedIn 7-12 words → X 4-9 words. Same formula; tighter language.
- Strip role-name prefixes ("As a B2B founder..." → cut). X needs no credential framing.
- Drop adjective layers ("absolutely critical realization" → "realization"). X rewards bare nouns and verbs.
- Lead with a number, name, or verb — never a connective adverb or hedge.
- First 5 words must contain the pattern-break. Read just those 5 — does the reader know whether to keep reading?
- Convert rhetorical question hooks to flat declaratives when possible — declaratives outperform questions on X by ~30%.

**Per-formula X calibrations:**

| Formula | X calibration |
|---|---|
| Contrarian-reframe | Strong on X — quote-tweet ecology amplifies contrarian theses 3-5x. Tighten to 5-7 words. The contrarian must be specific (not "everyone is wrong about X"). |
| Specific-stakes | Works on X when the number is stark and the loss is recent. Tighten the number-first ("$87K on Facebook ads. Here's what I'd do differently."). |
| Named-mechanism | X-acceptable when the name is short (2-4 words) and the post defines it in T2. Long named mechanisms ("The Recursive Buyer-Aware ICP Method") collapse on X — tighten or rebrand for the platform. |
| Before-after | Time-stamps work on X (concrete = credible). Drop transition language ("18 months ago I was X. Today, Y. Here's the lever."). |
| List-promise | Use only for threads (each list item ≈ one tweet). Cap at 5-7 items. List-promise hooks on single tweets without the thread payoff = engagement-bait signal. |
| Pattern-break | "Stop X. Do Y." works on X — fits the imperative-voice norm of the platform. Tighten to 6-8 words total. |
| Loop-open | Loops work on X but resolve faster — X readers won't wait through 4 tweets for a payoff. Resolve the loop in T3 or T4 of a thread; never beyond. |
| Question-stake | Questions underperform declaratives on X by ~30%. Convert to declarative when possible: "Why does your discovery call close at 18% when reply rate is 12%?" → "Your discovery call closes at 18% but reply rate is 12%. The gap is the qualification thread." |
| Insider-secret | Curiosity-gap hooks work on X but decay even faster than on LinkedIn — by post 3 in a quarter, the audience pattern-matches. Limit to ~5% of mix. |

## Format library

X supports four post types the workspace ships against. Format choice is downstream of source-post pillar (per `/repurpose-to-x` Step 2 decision tree) AND the operator's X strategy (cross-post amplifier vs. X-primary).

**1. Single tweet (~50-70% of X mix).** ≤280 chars. The default format for: pier posts, short Personality moments, Offer-bridge with bookmark-bait close, contrarian one-liners. Single tweets outperform threads when the payoff is one insight or one specific number — threading dilutes the punch. Anatomy: `[Hook clause: 4-9 words, verb-led]` → `[Specific: number/name/mechanism]` → `[Takeaway OR bookmark-bait]`. Length 180-260 chars; hard cap 280.

**2. Thread (~25-40% of X mix).** 5-15 tweets. The format for: mechanism breakdowns, case studies with multi-step proof, list-promise hooks with 4+ items, named-mechanism content where the name needs a definition tweet. Thread anatomy: T1 hook (tight, declarative when possible) → T2 pickup (the hook's implicit promise made explicit) → T3-T(N-2) beats (one per tweet, ONE specific per tweet, 80-260 chars each) → T(N-1) named mechanism or synthesis → T(N) close + the single chosen engagement primitive.

**3. Reply-as-content (~5-10% of X mix).** A substantive reply to a high-engagement tweet from another account, formatted as standalone content rather than as conversational reply. Reply-as-content is X's analog of LinkedIn's pier mechanism — the reply pulls profile-views from the original tweet's audience, AND the reply often gets surfaced separately in the operator's followers' feeds. Use for: contrarian extensions of viral takes, specific evidence that complicates a popular thesis, named-mechanism replies that define IP in someone else's conversation.

**4. Quote-tweet-as-content (~5-10% of X mix).** Quote-tweet of another account's post, with the operator's own commentary as the primary content. Quote-tweet-as-content compounds harder than reply-as-content because the original post serves as proof/evidence/foil and the operator's commentary stands as standalone tweet. Use for: contrarian thesis on a viral tweet, specific-number rebuttal of a popular claim, "this is the conversation you should actually be having" reframes.

**Optional T(N+1) quote-tweet-bait reply.** A separate tweet posted as reply to a thread's final tweet, offering a contrarian extension or specific number that invites quote-tweets. Used sparingly — works best on Authority + Pier source pillars per `/repurpose-to-x` Step 6.

## Posting cadence

Cadence calibrates to X's role in the operator's strategy.

**X-secondary (cross-post amplifier; default for most agency operators):** 1-3 X posts/day, 3-5 days/week. Most posts are LinkedIn-repurposed via `/repurpose-to-x`. Original X-only content (reply-as-content, quote-tweet-as-content, pier posts) supplements 1-2x/week. Sustainable with 30-60 minutes/day of X attention; below this, the X presence becomes a tombstone.

**X-primary (X-native ICP or operator with established X audience >10K):** 3-7 X posts/day, 5-7 days/week. Mix shifts to majority X-original content; LinkedIn becomes the secondary surface. Required engagement budget: 90-180 minutes/day of X attention (replies, quote-tweets, conversation participation). Operators running X-primary typically have a content-pipeline split: LinkedIn for cornerstone weekly content + X for daily engagement and faster news-cycle response.

**Posting time discipline.** X engagement windows are operator-local-tied: weekday mornings (8-11am) and evenings (7-10pm) for B2B audiences. Tuesday-Thursday outperforms Mon/Fri by ~20-30%. Saturday-Sunday outperforms LinkedIn weekend posting because the X audience is consumer-overlap and engages on weekends. Threads should publish in morning windows (allow full 12-hour engagement velocity arc); single tweets can publish in either window.

**Engagement velocity discipline.** The first 30 minutes after publish are the trajectory window. Operators who publish and walk away forfeit the algorithmic compounding. Workspace pattern: 30 minutes immediately post-publish for reply-engagement on the published post + scanning the operator's reply queue for inbound conversation. This is X's analog of LinkedIn's comment-reply harvest, but the window is shorter and the engagement-per-action ratio is higher (X replies are faster than LinkedIn comments).

**Cadence drift warning.** X cadence is a sustainability question more than LinkedIn cadence. The platform's faster cycle creates pressure to post more — operators who ramp X cadence beyond their actual material pipeline burn out or drift into low-quality content. Workspace default: hold cadence to what the source material + repurpose pipeline can sustain at quality bar. Volume without quality on X collapses the operator's account positioning faster than on LinkedIn.

## Engagement mechanics

X engagement happens through five primitives. The conversion path is shorter than LinkedIn's but the conversion rate per primitive is also lower.

**Replies (the conversation surface).** Replies are X's primary engagement primitive — most engagement happens in replies and reply-chains under the original tweet. Reply quality > reply quantity for the algorithm. A tweet that draws 5 substantive replies (each 50+ chars with substance) outperforms a tweet that draws 50 single-emoji replies. This is why reply-as-content (a substantive reply to a viral tweet) is a workspace tactic — the reply itself becomes content and pulls profile traffic.

**Retweets and quote-tweets (the audience-multiplier).** Retweets surface the original to the retweeter's followers without commentary. Quote-tweets surface with the quoter's commentary as endorsement. Quote-tweets compound 3-5x harder per algorithmic weight. Authority + Pier pillar content optimized for quote-tweet response (contrarian thesis, specific-number stake, named-mechanism definition) outperforms by ~2-4x on X.

**Bookmarks (the dark-social signal).** Bookmarks indicate "save for later" — the reader extracted perceived value but didn't engage publicly. X surfaces bookmark counts and weights bookmark-heavy posts for re-surfacing to similar audiences. Bookmark-bait CTAs ("Save this for when you ship your next launch") are the workspace's primary X CTA primitive when the source post warrants bookmark-driven compounding.

**DMs (rarer than LinkedIn DMs).** X DMs convert at lower volume and lower rate than LinkedIn DMs for B2B services. The professional-context buffer that LinkedIn provides — DMs in a "we are both here for business" frame — is absent on X. X DMs read as more intrusive, the reader is more guarded, and conversion-to-call rate runs ~30-50% of equivalent LinkedIn DM. Workspace stance: X DMs are NOT a primary acquisition surface for the B2B LinkedIn agency. Operators redirect inbound X interest to LinkedIn DM (via bio-link or pinned-tweet redirect) for the actual sales conversation.

**Profile visits + bio-link redirect (the conversion path).** The workspace's primary X conversion path: X content → profile visit → bio link → LinkedIn (or directly to application form). The bio link is the conversion infrastructure; the pinned tweet is the secondary conversion infrastructure. Operators should optimize both quarterly. Per `/repurpose-to-x` Step 5, X CTAs route through bio-link / pinned-tweet rather than raw URLs in tweet bodies (which X deprioritizes).

## Cross-platform repurpose strategy

X is almost always the destination platform for repurposing FROM LinkedIn — rarely the source.

**FROM LinkedIn to X (the primary direction):** Per `/repurpose-to-x`, top-decile LinkedIn posts (>2x median impressions for the subject) get repurposed to X. The ~25% of LinkedIn posts that warrant the repurpose are: Authority pillar posts with named mechanisms (X audience indexes hard on named-IP), Proof pillar case studies with multi-beat structure (justifies thread architecture), Pier pillar contrarian theses (quote-tweet ecology amplifies), Personality moments with sharp punchlines (single-tweet adaptation).

**What carries from LinkedIn to X:** the named mechanism, the specific numbers, the contrarian thesis, the operator's voice signature (with X-platform-tone calibration per `/repurpose-to-x` Step 7), the hook formula (with word-count tightening), the open-loop structure (with faster resolution).

**What does NOT carry:** LinkedIn paragraph cadence (X is line-cadenced), LinkedIn line-break rhythm (X uses tweet-per-beat), LinkedIn comment-trigger CTAs ("Comment 'X' for the doc" → "Reply 'X' and I'll send the doc" OR bookmark-bait), LinkedIn-context jokes ("the LinkedIn algorithm just changed..."), professional-context throat-clearing ("I want to share with you today..."), role-name prefixes ("As a B2B founder...").

**What to compress:** body word count (LinkedIn 800-1500 chars → X 180-260 chars single tweet OR distributed across 5-15 tweets). Multi-purpose paragraphs become single-purpose tweets. The compression is not deletion — it's restructuring.

**What to thread:** mechanism breakdowns with 3+ distinct beats, case studies with before/after/mechanism segments, list-promise hooks with 4+ items, named mechanisms that need a definition tweet.

**What to drop:** posts whose punch is entirely LinkedIn-context-dependent (e.g., "the LinkedIn algorithm just changed"), purely visual LinkedIn carousels (different format pipeline — see `/repurpose-to-ig` instead), low-engagement LinkedIn posts (repurposing a flop wastes the X audience — per `/repurpose-to-x` Failure Mode #5).

**FROM X to LinkedIn (rare):** X threads that landed >5x baseline get re-shaped into LinkedIn posts. Full re-engineering required: paragraph-cadence restored, 800-1500 char body, full hook formula applied, no thread-numbering carry-over. This direction is the exception, not the rule — most X content is too compressed to justify a LinkedIn re-expansion.

## Account safety

X enforces rate limits and automation policies that are looser than LinkedIn but still consequential.

**API rate limits.** Free-tier X API: 1,500 tweets/month posted, 10K reads/month. Paid-tier (Basic, Pro, Enterprise) raises limits substantially. Most agency operators stay on the free tier and post manually — API limits do not bind manual operation. If an operator runs an X scheduling tool (Buffer, Typefully, Hypefury), the tool typically operates within the operator's own posting rate (no automation suspicion) but does need to respect the daily-tweet cap (~2,400/day for verified, ~140/day for unverified — the unverified cap binds new accounts).

**DM rate limits.** ~500 DMs/day soft cap; above this triggers temporary lock. ~1,000 DMs/day triggers account flag. Workspace default for X DM ops (rare — see Engagement Mechanics): ≤50 DMs/day, manually sent, only to followers or confirmed mutuals. X DMs to non-followers often get filtered to the recipient's "Message Requests" inbox where they get ~5-15% read rate — wasted send.

**Automation tool restrictions.** X's API TOS permits scheduling and analytics tools; prohibits engagement automation (auto-reply, auto-DM, auto-follow, auto-unfollow). Tools like Hypefury, Typefully, Buffer for scheduling are TOS-compliant. Tools that automate replies, DMs, or follows trigger account suspension when detected. Workspace stance: scheduling tools acceptable for batched X posting; engagement (replies, DMs, quote-tweets) stays manual.

**Rate-limit shadow patterns.** X has unofficial "shadow" rate limits that don't trigger explicit warnings but degrade reach silently. Patterns that trigger shadow-suppression: posting >12 tweets/hour (engagement velocity outlier), replying to >50 tweets/hour (spam-like behavior), following/unfollowing >100 accounts/day (follow-churn signal), posting identical content multiple times (duplicate-content filter), posting external links in tweet body (X deprioritizes link-tweets — route to first reply or bio link instead).

**Account suspension recovery.** X suspensions can sometimes recover via appeals (3-21 day turnaround typical). Recovery rate is lower than LinkedIn's. Workspace defensive posture: maintain a backup X handle, keep tweet drafts in `outputs/` (not platform-only), treat X as rented infrastructure even when it's the secondary acquisition channel.

**Verified vs. unverified status.** X Premium (paid verification) raises tweet-character limit to ~25K, raises DM caps, raises algorithm weighting modestly, and unlocks edit-tweet feature. Most agency operators serving B2B ICPs benefit from Premium ($8-16/mo) — the algorithm bump alone justifies the cost when X is a tracked platform. Premium is NOT required for cross-post amplifier strategy; IS recommended for X-primary strategy.

## Workspace integration

X is touched by a smaller skill cluster than LinkedIn but the integration is first-class.

**Marketing (content) skills:**
- `/repurpose-to-x` — the primary X-bound skill. LinkedIn → X repurpose for top-decile posts. Owns the platform-tone calibration, hook tightening, body restructuring, CTA translation.
- `/build-content-engine` — when X is declared in `company.yaml::content_strategy.platforms`, the engine produces X-aware pillar mix and post-type assignments. For X-primary operators, the engine builds a parallel X content engine spec.
- `/build-hook-library` — produces per-platform hook libraries when X is tracked. The X hook library uses tightened word counts (4-9 words) and X-specific formula calibrations.
- `/build-posting-calendar` — schedules X posts in the operator-local windows (weekday mornings + evenings, Tue-Thu emphasis).
- `/score-hook` — scores X hooks against the same rubric (Stop-the-scroll, Specificity, Voice match, Promise alignment, Filter strength) with X-platform calibrations applied.

**Acquisition (sales) skills:**
- X DM ops are NOT integrated as a primary acquisition path (per Engagement Mechanics — X DMs underperform LinkedIn DMs). If an operator runs X-primary acquisition, the dm-strategist agent loads the X-specific DM playbook (TBD — Cycle 4 if demand surfaces).

**Cross-cutting:**
- `/diagnose-bottleneck` — when X is tracked, the diagnostic includes X engagement volume, X-to-LinkedIn redirect rate, X-sourced application-form completions.
- `/pipeline-pulse` — pipeline pulse reports X-sourced inbound separately from LinkedIn-sourced inbound when both are tracked.

**Agents who own X-bound work:**
- `marketing-head` — owns the cross-platform strategy, decides which LinkedIn posts warrant X repurpose
- `content-strategist` — executes per-artifact `/repurpose-to-x` and produces X-original content when X is declared as a primary surface
- `hook-writer` — produces per-platform hook libraries (X hook library is a separate artifact from LinkedIn hook library when both are tracked)

## Common failure modes

Six failure patterns the workspace has guards against. Each maps to a `/repurpose-to-x` step or a workspace skill gate.

**1. Copy-paste from LinkedIn (hook doesn't fit).** The most common X failure: operator pastes the LinkedIn post into X with line breaks deleted. The hook reads as filler (LinkedIn's professional-context buffer is missing), the body reads as wall-of-text (X is line-cadenced), the CTA collapses (LinkedIn comment-trigger has no X equivalent without translation). Guard: `/repurpose-to-x` enforces hook re-tightening (Step 3), body restructuring (Step 4), and CTA translation (Step 5) — copy-paste fails the validation gate.

**2. Thread without strong T1 hook.** Operator threads content that should have been a single tweet, OR threads with a soft T1 hook that fails to lock the reader for the remaining tweets. Thread completion rate collapses, the algorithm reads "low-quality thread," distribution caps. Guard: `/repurpose-to-x` Step 2 decision tree defaults to single-tweet when the source body is <250 words; Step 3 hook re-tightening enforces declarative-when-possible and 4-9 word target.

**3. Treating X like long-form.** Operator writes 12+ tweet threads where T2-T7 are filler and the actual payoff lands in T8. X reader bookmarks T1-T2, never reads to T8, completion rate dies. Guard: `/repurpose-to-x` Step 4 body-beat rules — every body tweet does ONE thing; if any thread tweet would survive deletion without the reader noticing, the thread should have been shorter.

**4. CTA double-up.** Operator includes bookmark-bait AND reply-trigger AND bio-link mention in the same X artifact. Reader doesn't know the next action, none of them fire. Guard: `/repurpose-to-x` Step 5 enforces exactly one engagement primitive per artifact.

**5. LinkedIn cadence in X cadence's clothing.** Tweet looks short but reads with LinkedIn paragraph rhythm — soft transitions, hedging language ("I want to share with you..."), 12+ word sentences throughout. Reader scrolls past because the cadence signals "long-form content I'd need to commit to read." Guard: `/repurpose-to-x` Step 7 voice-fit re-check; banned-vocab regex with X-specific stack on top of `spec/BANNED-VOCABULARY.md`.

**6. Repurposing a LinkedIn flop.** Source LinkedIn post hit 50% of subject median impressions, operator repurposes anyway, X version inherits the weakness, the operator's X account gets associated with low-quality content. Guard: `/repurpose-to-x` Step 1 engagement check; below threshold = recommend skip OR run `/diagnose-bottleneck` on the source post first.

## Cross-references

- `reference/frameworks/content/hook-anatomy.md` — the 9 hook formulas (LinkedIn-calibrated; X tightening rules in this pack)
- `reference/frameworks/content/virality-engine.md` — LinkedIn-specific mechanisms; X has its own analog (engagement velocity in first 30 minutes vs. dwell + comment depth in first 60)
- `reference/frameworks/content/pillar-architecture.md` — pillar mix that defines content strategy across platforms
- `reference/platforms/linkedin.md` — the primary platform; LinkedIn → X repurpose is the dominant cross-post direction
- `skills/repurpose-to-x/SKILL.md` — the skill that operationalizes LinkedIn → X repurpose end-to-end
- `skills/build-content-engine/SKILL.md` — produces X-aware content specs when X is declared in `company.yaml::content_strategy.platforms`
- `skills/build-hook-library/SKILL.md` — produces per-platform X hook library when X is tracked
- `skills/score-hook/SKILL.md` — scores X hooks with X-platform calibrations
- `spec/BANNED-VOCABULARY.md` — base banned-vocab list; X-specific additions stack per `/repurpose-to-x` Step 7
- `INVARIANTS.md` N-1, N-4, N-6, N-9 — voice loading, banned vocab, voice contamination, no "final" claims (apply equally to X artifacts)

---

*Platform pack version 1.0.0 — 2026-05-03*
