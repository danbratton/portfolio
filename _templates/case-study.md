---
# -----------------------------------------------------------------------------
# Case study template.
#
# To use: copy this file to `projects/<slug>.md`, update the front matter
# below, and replace the body sections with your content. Then add a card
# on `index.html` that links to `/projects/<slug>.html`.
#
# The `description` field below is the single source of truth for:
#   - <title> tag
#   - <meta name="description">
#   - <meta property="og:description">
#   - <meta name="twitter:description">
# Edit it in ONE place and all four update on the next build.
# -----------------------------------------------------------------------------

layout: case-study

# Full case study title. Include the hook and the outcome if possible —
# this becomes the <h1> and also appears in link previews.
title: "Case study title — include the hook and the outcome"

# One-sentence summary. This is what social platforms show in link preview
# cards, so make it do work: what happened, what you did, and what moved.
description: "One-sentence summary that powers all four description meta tags."

# Front-matter fields that feed the `.meta` line under the h1.
role: "Your role (with scope qualifier if useful, e.g. 'with embedded analytics ownership')"
company: "Company name"
timeline: "What was the time constraint or duration? e.g. '60 days: API deprecation deadline'"

og_type: article

# Replace <slug> with a kebab-case identifier matching the filename.
permalink: /projects/<slug>.html
---

<div class="tldr" markdown="1">
## TL;DR

One paragraph on the **situation** — what happened, what was at stake, what the forcing function was.

One paragraph on the **insight or reframe** — the thing you noticed that changed the shape of the decision.

One paragraph on the **outcome** — what you shipped and how it landed. **Bold the headline metric.**
</div>

## Situation

Set the stage. What was happening before you got involved? What was broken, at risk, or opportunity-shaped? Who cared, and why?

Useful elements to cover:

- Who the stakeholders were and what they were worried about
- The forcing function (deadline, deprecation, budget window, leadership ask)
- Hidden context that becomes important later in the story

## Task

What specifically were you trying to do? What constraints were you balancing? What would "done well" look like?

Keep this section tight — it's mostly framing for the analysis that follows.

## Analysis

The meat of the case study. What did you actually do to make the decision?

Topics that usually earn their keep here:

- **Instrumentation and measurement approach** — how did you know what was real versus what people *thought* was real?
- **What the data actually said** — specific numbers, specific segments, specific behaviors.
- **The reframe or insight it unlocked** — the move from "this is what we assumed" to "this is what the data showed."

Bolded paragraph leads (**Finding 1:** ...) read well for multi-part findings. Bulleted lists of findings are easier to scan than wall-of-paragraphs.

## Recommendation

What you decided to do, and why. A tight numbered list usually works:

1. **Decision 1** — rationale tied back to the data.
2. **Decision 2** — rationale tied back to the data.
3. **Decision 3** — rationale tied back to the data.

## Rollout

How you actually shipped it. Phased rollout design, guardrail metrics, fallback plans you kept in your back pocket, and the conversations you had to win to keep moving.

If there was a hard stakeholder moment — "the data is noisy, why are we shipping?" — this is the section where you walk through how you framed the risk and what evidence carried the room.

## Results

What moved. Lead with numbers if you have them. A bullet list reads cleanly:

- **Primary metric:** What it was, what it became, and the *shape* of the change (step function? gradual? delayed?).
- **Secondary metric:** Same.
- **Guardrail metrics:** What you watched to make sure you weren't breaking something upstream.
- **Delivery:** On time? Any regressions? What did the post-launch review catch?

## Reflection

What you learned. What you'd do differently. The posture or lesson you carry forward from this project into the next one. This is where judgment shows, separate from execution — it's often the section a hiring manager reads most closely.

<div class="skills" markdown="1">
**Skills demonstrated:** comma-separated list of the skills this case study actually exercised — e.g., product discovery, SQL analysis, stakeholder management under pressure, phased rollout design, guardrail-metric selection, decision-making under noisy data, contingency planning.
</div>
