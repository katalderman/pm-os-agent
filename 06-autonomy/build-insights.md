# Build Insights: Cortex PM Chief-of-Staff Agent

> Module 6 · ★ Deliverable 4, what you learned building it
>
> ✅ **What this validates:** you can reflect on what building it taught you — by the end you'll have proven the friction, the learning, and the aha that changes how you'd design your next agent.

## Friction

The validator (critic) was the most active source of friction, not the loop mechanics. Several runs saw the drafter and critic disagree repeatedly over a fuzzy status label ("Green" vs. "Yellow" vs. "At Risk") with no clean resolution, burning through both the revision cap and, once, the iteration cap before escalating. The bounds themselves never failed, they did exactly their job, but tuning which bound should catch which kind of stall took real thought (e.g. realizing the queue cap and the revision cap are catching two entirely different failure modes).

## Learning

(1) A bound is only real if it's enforced outside the model, a prompt telling the agent to behave is not a bound. (2) An independent critic catches things a self-grading drafter never would, including subtle norms violations, not just overt jailbreaks. (3) Different failure modes need different bounds, a runaway reasoning loop, a stuck critic/drafter disagreement, and an oversized commitment batch are three separate risks, not one generic "cap."

## Aha moment

Watching the critic and drafter disagree for multiple rounds over a reasonable-sounding status label, and realizing that's not a bug, it's exactly the case the revision cap and HITL checkpoint exist for. The agent doesn't need to be right, it needs to know when to stop and hand off.

## What you'd do differently

Add a "max revisions" row to the bounds table from the start rather than discovering it live, and give the critic a narrower, more literal norms reference (e.g. an explicit red/yellow/green decision table) so status-labeling disagreements resolve faster instead of eating the revision budget.
