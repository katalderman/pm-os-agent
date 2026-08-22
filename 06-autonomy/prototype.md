# Prototype: Cortex PM Chief-of-Staff Agent

> Module 6 · ★ Deliverable 1, the working agent demo
>
> ✅ **What this validates:** the agent actually runs end to end — by the end you'll have proven it with real screenshots of your Cortex across the six required moments (M2 to M6).

## What it does

_One paragraph: the agent in action, end to end._

## How you built it

- **Coding agent:** _which one you directed (Claude Code / Cursor / Codex)_
- **Model + bounds:** _model used, max iterations, cost cap, queue cap_
- **Repo / config:** _path to your build in `00-build/`_
- **Live link:** _[shareable URL, optional bonus]_

## Screenshots (required, collected M2 to M6)

Real screenshots of *your* Cortex running. These are the `00-build/CORTEX-ANATOMY.md` set and they are required, a link alone is not enough.

| # | Screenshot | What it shows | From |
|---|---|---|---|
| 1 | _[img]_ | happy-path run: a real drafted update + the HITL checkpoint (queued, not posted) | M2 |
| 2 | See transcript below ⬇ | Critic rejects Cortex's "Green" then "Yellow" status call twice (norms violation: reporting status without addressing open issue #818), revision cap of 2 fires, escalates to human instead of looping — nothing posted. | M3 |
| 3 | _[img]_ | a grounded update citing pulled activity + a caught hallucination | M4 |
| 4 | _[img]_ | jailbreak refused + escalated | M5 |
| 5 | _[img]_ | an iteration/cost/queue bound halting a runaway | M5 |
| 6 | _[img]_ | end-to-end run | M6 |

### Screenshot 2 transcript (critic rejection)

```
CRITIC, independent validation
{
  "verdict": "fail",
  "reasons": [
    "The output claims the project status is 'Green' but does not address
    the fact that there is an open issue (#818)... rules require that 'if
    a project has an open Sev-1 or a launch_hold flag, do not report it green'."
  ]
}
-> critic rejected; revision 1/2

CRITIC, independent validation
{
  "verdict": "fail",
  "reasons": [
    "The project status is reported as 'Yellow' due to an open issue, but
    this contradicts... there are no Sev-1 issues open... could lead to
    confusion about the project state."
  ]
}
-> critic rejected; revision 2/2

REVISION CAP hit (2). Escalating to a human instead of looping. Run cost ≈ $0.0041
```

## How to run it

_Minimal steps for someone to reproduce the demo (env vars, and the command or the coding-agent prompt you used)._
