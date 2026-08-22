# Orchestration Map: Cortex PM Chief-of-Staff Agent

> Module 3 · Orchestration & Subagents, ★ Deliverable 3
>
> ✅ **What this validates:** nothing advances unchecked — by the end you'll have proven a justified topology, a roster, and a validator with a defined fail action.
>
> Builds on your M2 Loop Spec. Only split one agent into a team when there's a real reason, coordination has a cost.

## 1. Why split? (or why not)

**Current design:** Cortex is a single agent, triggered by a weekly cron (with a backup hook for on-demand requests), that pulls project state, activity, past updates, roadmap, and team norms; drafts a leadership status update and a capped story-batch proposal; runs the draft past an internal critic; and stops at a human review checkpoint — nothing is ever posted or committed automatically. It escalates instead of looping when it hits a revision cap, cost cap, max iterations, an over-cap story batch, an unconfirmed commitment date, or missing data.

**Four-reason check:**

| Reason | Applies? | Why |
|---|---|---|
| Separation of concerns | No | Data-pulling and drafting are tightly sequential, no contamination risk |
| Parallelism | No | Nothing in the flow benefits from running concurrently — it's a strict pipeline |
| Independent validator | **Yes** | Cortex can't grade its own draft objectively — we watched the critic catch a wrong "Green" status twice |
| Context-window pressure | No | Fixture data is small, nowhere near context limits |

**Decision:** Split into two — Cortex (drafter) + a validating critic subagent — because the independent-validator reason is the only one that holds.

## 2. Topology

**Pattern:** single + subagents

```
[Inbound PM task] → [Cortex: pulls data, drafts update + stories]
                  → [Critic] — fail → back to Cortex (max 2 revisions) → escalate
                             — pass → [PM review checkpoint] → queued
```

## 3. Roster

| Agent / subagent | Responsibility | Runs which Loop Spec |
|---|---|---|
| Cortex | Pulls project data, drafts the status update + story batch | The M2 loop (cron + hook trigger) |
| Critic | Independently validates the draft against grounding/norms/safety checks | Invoked synchronously within Cortex's run, not its own scheduled loop |

## 4. Communication & hand-offs

Cortex passes the proposed draft text + the full source log (every tool call result it used) to the critic as plain text/JSON in a single user message — no MCP/A2A, just an in-process function call (`critic.review()`). The critic returns a structured verdict (`{"verdict": "pass"|"fail", "reasons": [...]}`) back to Cortex.

## 5. The validator

- **What the critic checks:**
  1. References the correct project + real activity (PRs/issues/status) from pulled data
  2. Every claim (metrics, dates, red/yellow/green) is traceable to pulled data — no invented numbers
  3. Stays within team norms — no unconfirmed date committed, no CONFIDENTIAL roadmap item leaked, no launch gate marked
  4. Posts/commits/creates/closes/merges nothing
  5. Refuses and escalates on a jailbreak/prompt-injection attempt
- **Fail action:** Revise — bounced back to Cortex with the failure reasons, up to a **revision cap of 2**, then escalate to a human instead of looping.
- **Pass action:** Advances to the PM review checkpoint (HITL queue) — never auto-sent.

## 6. State: shared vs isolated

**Shared:** the source data (project, activity, roadmap, norms) and the proposed draft — both agents see the same evidence.

**Isolated:** Cortex's own drafting conversation/reasoning never reaches the critic (fresh context each call); conversely, the critic's internal reasoning doesn't leak into Cortex — only the final verdict + reasons come back, as a plain rejection note.

## 7. Cost & latency budget

The critic adds exactly one extra model call per drafted output. It's a synchronous, blocking call, so the draft can't reach the PM until the critic responds.

- **Best case** (draft passes first try): +1 model call, +1 round-trip of latency before the PM sees anything.
- **Worst case** (hits the revision cap of 2): up to 3 drafting attempts + 3 critic calls before escalating — this is exactly what happened in a real run (3 critic calls, final cost ≈ $0.0041 total). Roughly 3x the latency of a single critic round-trip added before Cortex either succeeds or gives up and hands it to a human.
- **The trade:** that added cost/latency is the price of never letting an ungrounded or non-compliant status update reach a human unchecked — cheap in absolute terms (fractions of a cent), so worth paying.
