# Loop Spec: Cortex PM Chief-of-Staff Agent

> Module 2 · Loop Engineering, ★ Deliverable 2
>
> ✅ **What this validates:** the agent knows when to run and when to stop — by the end you'll have proven a one-page Loop Spec with a trigger, a definition of "done," and explicit stop conditions.
>
> Your one-page blueprint for how the work you handed to the agent (M1) actually *runs*.
> An agent is just a prompt that fires itself, this spec says when it fires, what "done" means, and what it needs to do the job. Living document; refine as the course progresses.

## 1. Trigger & loop type

**Chosen type:** Cron (primary) + Hook (backup)

**Why this type?** The weekly leadership status update is tied to a recurring scheduled meeting, so it needs a fixed weekly cadence rather than something continuous or purely reactive — that's cron. A hook is added as backup so someone can message Cortex directly for an early/out-of-cycle draft; heartbeat and hook-only/goal were ruled out as primary because they either run constantly or require an external action to fire, neither of which matches a calendar-driven weekly need.

**Idempotency:** When the backup hook fires and produces a draft for the current week, it suppresses that week's scheduled cron run — so the same week never gets two separate update drafts.

## 2. Goal / definition of done

A status update draft (and proposed story batch) is produced and queued for human approval; Cortex never posts anything itself.

## 3. Stop conditions

| Condition | What it looks like | What happens |
|---|---|---|
| **Success** | Critic approves the draft (or it hits the HITL queue) within the revision cap | Run ends, nothing auto-posted |
| **Stuck / give up** | Data can't be pulled after N attempts, OR the critic rejects the draft twice in a row (revision cap) | Stop and log, don't loop forever |
| **Escalate to human** | Run touches an embargoed/confidential project, a public commitment date, a story batch over the queue cap, OR hits the revision cap | Escalate instead of guessing (HITL checkpoint, from agent-line-map) |

## 4. State

Persists across runs: the roadmap, prior commitments/dates already communicated, and team tone/escalation norms. Purged each cycle: raw pulled activity dumps and draft scratch text, so stale data never leaks into a future update as if it were current.

## 5. The five things a loop can lean on

_`state` is always-on. `connectors` only if you already have one wired (e.g. a Jira key or Google MCP) — otherwise just note it as a plan. `skills`, `subagents`, `work tree` scale with autonomy; "not needed yet, because…" is a valid answer._

| Component | For Cortex |
|---|---|
| **Work tree** (isolated workspace per run, a git worktree) | Not needed yet, because Cortex only reads/drafts — it never touches a code or file workspace that needs isolation. |
| **Skills** (reusable capabilities) | Not needed yet, because the workflow is a single fixed pipeline, no reusable sub-capability yet. |
| **Plugins / connectors** (tools & access, optional if you don't have one yet) | Not wired yet — currently reads local fixtures; would need a real Jira/GitHub API key (or MCP) to pull live activity. |
| **Subagents** (independent check when the loop can't grade itself) | _placeholder → M3 orchestration-map.md_ |
| **State tracking** | Roadmap, prior commitments/dates, and team norms persist across runs (see §4). |

> Context plan (M4) and the hand-off to bounds & evals (M5) come in later modules — you'll add them to their own deliverables then, not here.

## Link to live loop

`00-build/agent.py` (loop + bounds), `00-build/prompts.py` (CORTEX_SYSTEM + CRITIC_SYSTEM), `00-build/tools.py` (tool registry, queue cap enforcement).
