# Cortex, a PM Chief-of-Staff Agent

> A chief-of-staff, an orchestrated swarm of agents that triages a PM task, pulls internal state, and preps a status update plus a story batch, so the team approves instead of assembling from scratch.

_Kat Alderman · Agentic Workflows & Loops Cohort · August 2026_

Repo: https://github.com/katalderman/pm-os-agent

This repo is my final project for the Agentic Loops for PMs Certification, **Cortex**. Each module’s artifact lives in its own folder; this README is the dashboard and the pitch.

---

## Module artifacts

### M1 · The Agent Line
- **Agent-line map**: [`01-agent-line/agent-line-map.md`](01-agent-line/agent-line-map.md)

### M2 · Loop Engineering
- **Loop spec**: [`02-loop-design/loop-spec.md`](02-loop-design/loop-spec.md)

### M3 · Orchestration &amp; Subagents
- **Orchestration map**: [`03-orchestration/orchestration-map.md`](03-orchestration/orchestration-map.md)

### M4 · Context Engineering &amp; Memory
- **Memory &amp; context plan**: [`04-memory-context/memory-and-context.md`](04-memory-context/memory-and-context.md)

### M5 · Bounds &amp; Evals
- **Bounds &amp; evals**: [`05-bounds-evals/bounds-and-evals.md`](05-bounds-evals/bounds-and-evals.md)

### M6 · Autonomy &amp; Production
- **Production &amp; autonomy plan**: [`06-autonomy/production-and-autonomy.md`](06-autonomy/production-and-autonomy.md)
- **Prototype write-up**: [`06-autonomy/prototype.md`](06-autonomy/prototype.md)

---

## Ship plan

### Autonomy dial (per segment)
- Cautious/first-time PM → Supervised; reviews every draft before anything moves, no delegation beyond drafting.
- Experienced PM (own team) → Bounded-autonomous; trusts the drafting quality, Cortex assembles and queues the update/stories on its own, still stops at the HITL checkpoint before anything posts.
- Exec/leadership stakeholder → Shadow; never operates Cortex directly, only sees the final human-approved artifact.

### Trust Ladder rung + eval gate
Current rung: Supervised (every run stops at the HITL checkpoint; nothing posts or commits without a human).
Eval gate to climb to Bounded-autonomous: all 5 EV cases (EV-1 to EV-5) passing on every offline replay, AND 0 safety/bound violations (EV-1 jailbreak, EV-2 auto-queue cap) AND ≥95% task-completion pass (EV-4) over 4 weeks of supervised production runs.
Clean incident record: zero jailbreak/injection successes, zero confidential-data leaks, zero unsupported/invented claims reaching the HITL checkpoint undetected.

### Deployment plan
- Runtime: serverless/scheduled function, weekly cron trigger (M2 loop type: Cron primary + Hook backup); no always-on service needed.
- On-call owner: Kat Alderman (PM), with escalation to the eng lead/manager if unavailable.
- Rollback: disable a specific tool (e.g. propose_stories), or drop the dial a rung, or full revert to the last known-good prompt/version in git, smallest blast radius first.
- Monitoring: eval pass % (M5 suite), escalation rate, cost-to-serve (~$0.003-0.004/run), trust incidents.

### ROI metrics + widen-autonomy rule
- Outcome: time saved per weekly update cycle, self-reported "would've taken me X hours" at HITL approval, vs. manual baseline.
- Cost-to-serve: fully-loaded $ per run, rolled up monthly (~$0.003-0.004/run x weekly cadence).
- Trust incidents: near-misses caught per month, critic rejections that were invented-claim or confidential-leak near-misses, not just any rejection.

### Governance &amp; strategy
- Compliance: roadmap items flagged CONFIDENTIAL (e.g. Pulsar, Orbit) never enter an external or company-wide update; no PII is handled today.
- Safety: the M1 above-the-line list stays above the line for everyone regardless of segment or dial position; kill switch is revoking the credential that issues new JIT tokens.
- Reliability: per-run cost + iteration caps (8 iterations, $0.50/run + $20/day, 2 revisions, 90s timeout, 5-item queue cap); escalate-on-stuck on any bound trip; model-down fallback logs the cron miss and notifies the PM to draft manually.
- Strategy: widen one segment at a time; next bet is letting the Experienced PM segment's routine, no-risk updates skip straight to bounded-autonomous once the eval gate holds clean for 4 weeks.

---

## Build insights

- **Friction point.** The validator (critic) was the most active source of friction, not the loop mechanics. Several runs saw the drafter and critic disagree repeatedly over a fuzzy status label ("Green" vs. "Yellow" vs. "At Risk") with no clean resolution, burning through both the revision cap and, once, the iteration cap before escalating. The bounds themselves never failed, they did exactly their job, but tuning which bound should catch which kind of stall took real thought (e.g. realizing the queue cap and the revision cap are catching two entirely different failure modes).
- **Key learning.** (1) A bound is only real if it's enforced outside the model, a prompt telling the agent to behave is not a bound. (2) An independent critic catches things a self-grading drafter never would, including subtle norms violations, not just overt jailbreaks. (3) Different failure modes need different bounds, a runaway reasoning loop, a stuck critic/drafter disagreement, and an oversized commitment batch are three separate risks, not one generic "cap."
- **Aha moment.** Watching the critic and drafter disagree for multiple rounds over a reasonable-sounding status label, and realizing that's not a bug, it's exactly the case the revision cap and HITL checkpoint exist for. The agent doesn't need to be right, it needs to know when to stop and hand off.

---

_Certification submission, Agentic Loops for PMs Certification._
