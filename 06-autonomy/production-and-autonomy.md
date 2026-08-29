# Production & Autonomy: Cortex PM Chief-of-Staff Agent

> Module 6 · ★ Deliverable 5, how you'd ship it, govern it, and widen trust over time
>
> ✅ **What this validates:** you can ship it, govern it, and widen trust deliberately — by the end you'll have proven an autonomy dial, a Trust Ladder rung with its eval gate, and a governance plan.

## Autonomy Dial by segment

_Autonomy is a product decision per user, not one global setting._

| Segment | Desired autonomy | Why |
|---|---|---|
| Cautious/first-time PM | Supervised | Low trust in agent judgment yet, wants to review every draft before anything moves, no delegation beyond drafting. |
| Experienced PM on your own team | Bounded-autonomous | Trusts the drafting quality; fine letting Cortex assemble and queue the update and stories on its own, still stops at the HITL checkpoint before anything posts, but doesn't need to babysit each intermediate step. |
| Exec/leadership stakeholder | Shadow | Never operates Cortex directly, only ever sees the final human-approved artifact; from their seat, Cortex might as well not exist yet, they see outcomes, not the agent's process. |

## Trust Ladder

- **Current rung:** Supervised — Cortex completes the full task end to end (drafting, grounding, proposing), but every run stops at the HITL checkpoint; nothing is posted or committed without a human.
- **Eval gate to reach the next rung (bounded-autonomous):** All 5 EV cases (EV-1 to EV-5, `05-bounds-evals/bounds-and-evals.md` §3) passing on every offline replay, plus 0 safety/bound violations (EV-1 jailbreak, EV-2 auto-queue cap) and ≥95% task-completion pass (EV-4) over 4 weeks of supervised production runs.
- **Incident record so far:** Clean for this window means zero jailbreak/injection successes, zero confidential-data leaks, and zero unsupported/invented claims (metrics, dates, statuses) reaching the HITL checkpoint undetected by the critic.

## Deployment plan

- **Runtime:** Serverless / scheduled function (weekly cron trigger, matching the M2 loop type: Cron primary + Hook backup), rather than an always-on service, since nothing about this workload runs continuously.
- **Operator / on-call owner:** Kat Alderman (PM), with escalation to the eng lead/manager if unavailable.
- **Rollback:** Tiered, smallest blast radius first: (1) disable a specific tool (e.g. pull `propose_stories`) without killing the whole agent, (2) drop the autonomy dial a rung (e.g. force everyone back to Supervised), (3) full revert to the last known-good prompt/version in git.
- **Monitoring:** Eval pass % (M5 suite), escalation rate (HITL hits vs. clean completions), cost-to-serve (run cost, currently ~$0.003-0.004/run), and trust incidents (any near-miss the critic barely caught).

## ROI metrics (beyond adoption & tokens)

| Metric | Target |
|---|---|
| **Outcome:** time saved per weekly update cycle | Self-reported "would've taken me X hours" at HITL approval time, tracked against the manual baseline |
| **Cost-to-serve:** $ per run, rolled up monthly | ~$0.003-0.004/run (from the agent's own cost estimate) x weekly cadence, tracked from run logs |
| **Trust incidents:** near-misses caught per month | Count of critic rejections that were invented-claim or confidential-leak near-misses, not just any rejection, sourced from critic rejection reasons in run logs |

## Widen-autonomy decision rule

The dial widens one rung only when the current rung's eval gate has held clean for its full window (per the Trust Ladder), with zero safety/bound violations, zero confidential leaks, and zero invented claims reaching a human undetected, evidence decides, not a good demo.

## Governance & forward strategy

- **Compliance:** Roadmap items flagged `CONFIDENTIAL` (e.g. Pulsar, Orbit) must never appear in an external or company-wide update, enforced by the norms Cortex is given plus critic review. No PII is handled by this agent today since it only touches project/engineering data, not personal data.
- **Safety:** Everything on the M1 above-the-line list stays above the line for everyone regardless of segment or dial position (deciding relevant context, commitment level/status, posting/approving a company-wide send). Kill switch is revoking the service credential that issues new JIT tokens (per M5).
- **Reliability:** The M5 bounds cap this: max 8 iterations, $0.50/run + $20/day, max 2 revisions, 90s timeout, 5-item queue cap. Escalate-on-stuck fires whenever a bound trips rather than looping. Model-down fallback: if the scheduled run fails to complete (API down, etc.), the cron miss is logged and the PM is notified to draft manually that week rather than silently skipping the update.
- **Strategy:** Once the Trust Ladder gate holds clean for 4 weeks, the natural next widen isn't a new segment, it's letting the Experienced PM segment's routine, no-risk updates (no open Sev-1, no confidential content, no invented claims flagged) skip straight to bounded-autonomous behavior in practice, gated by the same eval suite holding clean, not a new one.
