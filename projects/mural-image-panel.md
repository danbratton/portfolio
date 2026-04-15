---
layout: case-study
title: "From Forced Migration to Doubling Engagement: Rebuilding Mural's Image Panel in 60 Days"
description: "How behavioral analysis turned a forced API migration and skeptical stakeholders into a product upgrade that doubled weekly active image adders."
role: "Senior Product Manager"
company: "Mural"
timeline: "Q1 2026"
og_type: article
permalink: /projects/mural-image-panel.html
---

<div class="tldr" markdown="1">
## TL;DR
Shortly before the Thanksgiving holiday, Mural's image search provider (Bing Image Search) gave us 60 days before API shutdown. Given the short timeline, there was a strong sentiment that we should do a replace-in-kind approach. Stakeholders were worried that deviating from an web image search provider would break long-tail use cases. Such as named-entity searches like "Taylor Swift" or album art needed for niche icebreaker templates. 

At the same time, legal had flagged any copyright-returning library as a material risk. In fact, some customers had already toggled the image panel off because of this risk.

I ran a behavioral analysis of the existing panel and found that **90% of image searches were simple emotional or contextual terms** ("happy," "christmas," "cat"), and that savvy users were following an image search with a second query appended with "GIF" or "emoji" — a workaround for a usability gap that only power users had figured out.

I used that data to reframe the conversation: the long tail searches such as "Taylor Swift" wasn't where the value lived. Behavioral analysis showed that we had an opportunity to maximize the impact of this change by improving how the image panel served the most important use cases for the most users. For example, using emojis to react to sprint retrospective topics or GIFs to express your mood in an icebreaker. On top of that, a copyright-safe library would mitigate business risk and unblock customers who had toggled off this critical feature.

We replaced Bing with Unsplash, added the GIPHY GIF library and a native emoji library, and unified them into a single search experience. This supported the primary use cases for users while product-izing the power user workarounds discovered during my analysis.

We phased the rollout in this way: internal employees first, then Free/Team+ users, then broad release. All the while, we kept a backup plan of the Brave image search API in reserve.

- The **internal release** let us release an unpolished version to get early feedback and find bugs.
- The **Free/Team+ release** gave us 1 week of data to show the release did no harm to leading retention metrics. This was important for stakeholder buy-in.
- The **broad release** showed that the new image panel design was far superior. Post-launch dashboards showed task completion, keyword-search success, and weekly active image adders all moved in the right direction. **Weekly active users increased >100% and frequency of use increasing >25%**.
</div>

## Background
Mural is a digital whiteboard for the enterprise. The canvas acts as a place where distributed teams can come together to meet and work. Common use cases include agile rituals, such as daily stand ups and retrospectives, product planning, such as roadmaps and strategy, marketing campaign planning, and architecture diagramming. Imagery is a critical part of this experience as it plays an important role in visual communication and in giving users ways to express themselves in virtual meetings. 

## Situation

Shortly before the Thanksgiving holiday in 2025, Bing Image Search announced it was deprecating its API with **60 days' notice**. It was the backbone of Mural's image panel, which lets users search for images and drop them onto the collaborative canvas during workshops, brainstorms, and retros. No replacement was pre-scoped. No migration plan existed. My team was tapped to scope and launch the replacement.

Stakeholder anxiety concentrated in three places:

- **Customer Support** was worried about a wave of tickets from users who could no longer find specific images they were used to finding.
- **Design leadership** was concerned that changing or narrowing the image library would break familiar workflows, particularly for workshop runners and power users.
- **The Templates PM** flagged a specific concern: a small number of templates (e.g., "favorite music album") depended on users being able to search for niche copyrighted imagery like album art — something only a web image search library like Bing could provide.

There was also an important piece of context that surfaced during stakeholder conversations: **Mural's legal team flagged any image library that returned copyrighted results as a material risk**. The consequence wasn't hypothetical — **some customers had actually toggled the image panel off entirely at the account level** rather than expose their users to potentially copyrighted imagery. A baseline collaboration feature was being disabled by the customers we most wanted using it, because we couldn't give them an image library that didn't create risk.

That changed the framing of the Bing deprecation. It wasn't just a forced migration — it was an opportunity to solve a standing business problem that had been quietly costing us adoption. The default instinct in the room was to do a like-for-like swap: find another general-purpose image API and plug it in. That path was technically viable but bad on two fronts — most alternatives had worse content quality, and, more importantly, a general-purpose replacement would have kept us inside the same copyright risk envelope that had caused customers to disable the feature in the first place. It also didn't address any of the usability issues we'd been quietly tracking.

## Task

My job was to pick a replacement strategy that balanced three things under a hard deadline:

1. **Coverage** — did the new experience meet real user needs?
2. **Quality** — were we making the product better, or just not-worse?
3. **Speed to delivery** — could engineering ship it in under 60 days with room for QA?

And critically, I had to get stakeholders aligned on that strategy fast enough to leave engineering time to build.

## Analysis

Before I could answer *what users were actually doing in the image panel*, I had to make sure we could measure it properly. The existing instrumentation was built for bulk aggregation — good enough for "how many searches happened this week," not good enough for "what does an individual user actually do between opening the panel and adding (or not adding) an asset."

**Partnering with engineering on instrumentation.** I worked directly with the engineering team to spec and land a new event schema before the analysis phase started. Luckily, we already had much of the event tracking we needed in place; however, there was one flaw: We couldn't know what users were doing in a *single tab opened session*. Meaning, we could only measure aggregate data but had no way to know what happened in a single tab opened session. **I could not measure the funnel**.

We decided to add a **`tabSessionId` that was regenerated every time a user opened the image panel**. That single design choice was what made the rest of the analysis possible: instead of aggregating events across a user's whole day, I could isolate a single panel-open → search → add (or abandon) session and reason about it as a discrete unit. It's the difference between "this user searched 14 times today" and "on this specific panel open, the user searched 'happy,' got nothing useful, searched 'happy GIF,' and added a GIF." The second version is what reveals retry behavior and usability gaps; the first version hides them.

With the instrumentation in place, the funnel became clean: users **open the panel**, **run a keyword search**, and then **add an asset** (image, GIF, or emoji) to the mural. Scoping by `tabSessionId` let me track an individual user's journey from open to add — including search refinements, tab switches, and abandonment.

I pulled the last several months of events and looked at four things:

1. **Task completion rate** — of users who opened the panel, what share added an asset? Of users who ran a search, what share added an asset after?
2. **Search term distribution** — what are people actually typing? Are searches concentrated on certain topics or widely varied? Are there trends across types of users?
3. **Follow-up search behavior** — what do users do *after* their first query?
4. **Abandonment patterns** — where in the funnel do people drop out?

Three findings changed the conversation:

**Finding 1: The long tail barely existed.** Roughly **90% of image searches were simple emotional or contextual terms** — "happy," "christmas," "emoji," "cat," "arrow," "star," "team," "gif." The top queries were dominated by everyday expressive concepts, not named entities or copyrighted material. The "Taylor Swift" hypothetical was a vivid example, but it wasn't representative of real usage.

**Finding 2: A hidden usability gap.** A meaningful share of users ran a second query within the same panel session, and the most common follow-up pattern was *adding "GIF" or "emoji" to their previous term*. That's not a content problem — that's a discoverability problem. Users wanted expressive assets, discovered (eventually) that Mural had a GIF/emoji workaround, and retried their search to find it. Only power users ever got there.

**Finding 3: Task completion was leakier than we thought.** Overall task completion sat around 30% from panel-open to asset-added, with ~65% of *searchers* converting. The gap between "opened the panel" and "ran a search" was surprisingly large, and the retry behavior confirmed that a chunk of users were searching into a dead end and bouncing.

## The Reframe (and the stakeholder moment)

I wrote a single data memo and shared it ahead of the decision meeting. The headline was:

> *The long tail search terms accounts for a small fraction of real usage. Just 10% of searches account for 90% of search volume. Nearly all users are actually searching for the same emotional or contextual terms. On top of that, we can replace our own power-user workaround with a first-class experience.*

The 90% number did most of the work. It was specific enough to cut through the hypotheticals, and it directly answered the CS team's concern: the searches they were imagining weren't the searches that were most valuable. For Design, the follow-up-query data reframed the conversation from "are we removing a feature?" to "we're promoting a hidden workaround into a first-class experience."

The Templates PM needed a different conversation. Their concern was specific and legitimate — a handful of templates *did* depend on copyrighted imagery. I handled that one with **both data and a mitigation**:

- **Data:** I pulled usage on the affected templates. The "favorite music album" class of templates sat in the very low-usage tier. This was a meaningful concern for the owners of those templates, but should not be a blocker for the broader decision. *Mural's core use case templates had >10,000 times more usage than the templates in question.*
- **User interviews:** I met with Mural's LUMA instructors, an arm of the company focused on teaching our customers design thinking, to understand what impact the change would have on the templates they use for workshops. The feedback boiled down to "this doesn't change my work" and "this is a huge improvement". *This was a powerful piece of evidence from the most important power users of templates.*
- **Workaround:** I noted that these templates were not wholly broken with this change. Users committed to using this template could still use them by search the internet for the specific, copyright image they needed. Legal had cleared this specific use case. *This was an acceptable tradeoff given the low volume of usage these templates had.*

By the time we walked into the decision meeting, the hardest conversations had already happened in 1:1s, and the meeting itself was mostly about sequencing the build.

## Recommendation

Based on the data, I recommended:

1. **Replace Bing with Unsplash** — a smaller but higher-quality library, with a clean content-licensing posture, a fast API, and content that matched the emotional/contextual search patterns we were seeing. Crucially, the copyright-safe stance meant customers who had previously disabled the image panel at the account level could safely turn it back on.
2. **Add GIPHY GIFs and a native emoji library** as first-class sources — no more second queries, no more power-user workarounds.
3. **Unify all three sources into a single search experience** — one search box, results surfaced from all three providers, with tabs to filter. This directly addressed the retry behavior we'd observed.

This was a lower-risk, faster-to-ship plan grounded in actual behavior rather than hypothetical edge cases — and it turned a forced infrastructure migration into an opportunity to unlock a baseline feature for customers who had been unable to use it.

## Rollout

Given the stakes — a baseline feature touching every collaborative workflow on the platform — I didn't want to cut over in one step. I designed a phased rollout that let us measure impact on a small, lower-risk population before going broad, and I paired the feature-level GTM metrics with a set of **guardrail metrics** that would tell us if the change was hurting anything upstream:

- **1-day and 7-day retention** (% of new users who returned to the product 1 and 7 days after signup)
- **7-day mural creation conversion** (% of new users who created their first mural within 7 days of signup).

If the new panel was somehow degrading activation or stickiness, those metrics were where we'd see it first.

**Phase 1 — Internal company users.** Because the 60-day clock was unforgiving, we shipped a **stripped-down but functional version** of the new panel to Mural's own employees first — a large and technically sophisticated population of power users, and exactly the audience most likely to notice missing polish. The internal rollout gave us three things:

- **A real engagement signal:** active usage of the image panel went up among internal users, even with the rough edges.
- **Qualitative validation:** internal user interviews were consistently positive. The feedback I paid the most attention to was specific and surprising — users told us the new panel was *more useful for collaborative work* because they could react quickly to each other's content with emojis and GIFs. That was Finding 2 (the hidden usability gap) playing out in practice: once GIFs and emojis became first-class, they improved the collaboration experience, not just the image-selection flow.
- **A bug surface.** We caught a set of bugs in the internal phase and fixed them before any external users touched the feature.

**Phase 2 — Free and Team+ users.** Next we rolled out to Free and Team+ users — roughly 10% of the customer base, and the lowest-risk external segment. The goal for this phase wasn't to prove a lift; it was to prove the new panel **did no harm** on the guardrail metrics before going broad.

The measurement window was tight — **one week** — and the rollout landed in **January, as users were returning from the holidays**. The data was noisy. I had expected to see movement on engagement metrics because I believed the panel was a net positive, but a one-week post-holiday window wasn't enough signal to separate the feature from seasonality. What we *could* say with confidence was that retention and mural-creation conversion were stable — the new panel was doing no harm to these leading indicators.

Stakeholders were wary. "Stable" wasn't the triumphant signal they were hoping for, and the instinct in the room was to delay the broad rollout until we had cleaner data. I pushed back with four arguments:

1. **We had to move.** The Bing deprecation clock was still running. Waiting for cleaner data meant running out of runway on the migration we were forced into in the first place.
2. **At worst, this did no harm.** The guardrail metrics were the right place to look for damage, and they were flat. The downside scenario was "things stay the same" — not "we break the product."
3. **I had strong directional confidence** that metrics would improve once the rollout hit a broader, non-holiday user base. The internal qualitative signal and the underlying behavioral analysis both pointed the same direction.
4. **We had a backup plan.** If the broad rollout went sideways, we had a like-for-like fallback ready to deploy: swap in the Brave web image search API. It would have been a worse product experience, but it was a real parachute, and having it in hand made the risk of moving forward much more defensible.

That combination — guardrail metrics as a safety floor, qualitative signal as a directional ceiling, and a concrete fallback if things broke — was enough to get alignment on proceeding with the broad rollout.

## Results

We shipped the redesigned image panel **inside the 60-day window** with no critical regressions, and the broad rollout landed after the phased validation described above. Post-launch, I tracked the same funnel I'd used to build the case in the first place — and once the rollout expanded beyond the noisy post-holiday window, the directional bet paid off. The metrics moved in the right direction across the board:

- **Task completion (panel opened → asset added):** Climbed from ~30% to ~35% and kept gradually rising. The gradual shape matters — it reflects users learning the new panel *and* a memory feature that surfaced previously-used emojis on open, which meant repeat users often didn't need to search at all.
- **Keyword search success (search → asset added):** Improved from ~65% to ~70%, which was the cleanest signal that users were finding what they were actually looking for on the first try rather than retrying or bouncing.
- **Weekly active image adders:** Roughly **doubled**, jumping from ~7k to ~15k distinct users per week. Engagement expanded well beyond the existing power-user base.
- **GIF and emoji adoption:** Meaningful, sustained usage of the native libraries post-launch confirmed Finding 2 — the demand had been there all along, just blocked by a usability wall.
- **Delivery:** On time, zero critical regressions, and the small set of affected templates was updated before launch so no downstream breakage made it to users.

## Reflection

The thing I keep coming back to on this project is how easily we could have spent 60 days on a safer, worse answer. "Swap Bing for another general-purpose API" was the path of least stakeholder resistance — it preserved the existing mental model and didn't require anyone to change their assumptions. It also would have shipped a weaker product and left the retry-behavior problem unsolved.

What unblocked a better answer wasn't cleverness — it was **data specific enough to change minds**. A single number (90%) made the abstract fear concrete and falsifiable. Pairing it with a mitigation plan for the one stakeholder whose concern *was* valid turned a potentially contentious decision into an easy one.

If I were doing it again, I'd run the template-usage analysis earlier — I went looking for it only after the Templates PM raised the concern, and I think I could have pre-empted the objection by walking in with it already in hand. Lesson learned: when you know which stakeholders are going to push back, pre-build the answer to their question before they ask it.

The Phase 2 rollout also taught me something about arguing with imperfect data. My instinct in that room was to keep measuring until the signal was clean, and I had to consciously push against that instinct because the deadline didn't allow for it. What made the "ship anyway" argument defensible wasn't confidence in the data — the data was genuinely noisy — it was the *structure* of the bet: guardrail metrics on the downside, qualitative signal plus analytical grounding on the directional upside, and a pre-built fallback if we were wrong. When you can't get certainty, the best you can do is make the shape of the risk legible to the room. That's a posture I've carried into every high-stakes launch since.

<div class="skills" markdown="1">
**Skills demonstrated:** product discovery, partnering with engineering to spec event instrumentation (including session-grain design), SQL analysis, funnel definition, behavioral segmentation, stakeholder management across CS/Design/PM/Legal, scope negotiation under a hard deadline, phased rollout design, guardrail-metric selection, decision-making under noisy data, contingency planning, post-launch measurement.
</div>
