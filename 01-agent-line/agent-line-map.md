# Agent Line Map: Cortex PM Chief-of-Staff Agent

> Module 1 · The Agent Line
>
> ✅ **What this validates:** every risky action has a clear owner — by the end you'll have proven an above/below-the-line map with HITL checkpoints, scored on reversibility, blast radius, and measurability.

## The workflow, decision by decision

List every discrete decision or action in your agent's workflow, then score each one and place it **above** the line (a human owns it) or **below** (the agent owns it). Borderline calls get an HITL checkpoint.

| Decision / action | Reversibility (H/M/L) | Blast radius (H/M/L) | Measurability (H/M/L) | Above / Below | HITL? |
|---|---|---|---|---|---|
| Pull project state + recent GitHub/Jira activity | H | L | H | Below | No |
| Decide what context is relevant | M | M | L | Above | No |
| Draft the weekly leadership status update | H | L | M | Below | No |
| Decide tone | H | L | L | Below | Yes |
| Decide commitment level / risk rating (red/yellow/green, dates) | L | H | M | Above | No |
| Flag at-risk / escalation | H | L | H | Below | No |
| Choose what to escalate | L | M | M | Below | Yes |
| Propose next sprint's stories from the PRD (within cap) | H | L | M | Below | No |
| Post the update / approve a company-wide one | L | H | M | Above | No |

## Agent anatomy (sketch)

- **Model:** Default to a fast/cheap model for routine tasks (pulling activity, drafting, flagging at-risk signals). Escalate to a frontier model whenever the output touches tone, commitment level, or a company-wide audience — the stakes are higher and the model needs to reason about how the message lands, not just summarize facts.
- **Tools:** Project + activity lookup (read) · past-update search · roadmap · team norms · story proposal (capped) · an escalation directory (who owns what, so "choose what to escalate" has real routing info instead of a guess).
- **Memory:** Persists across runs — the roadmap, prior commitments/dates already communicated (so it never contradicts a promise it made last week), and team tone/escalation norms. Purged after each cycle — the raw pulled activity dump and draft scratch text, since keeping stale raw data around risks it leaking into a future update as if it were current.
- **Loop:** _placeholder, defined in M2 loop-spec.md_
- **Bounds:** _placeholder, defined in M5 bounds-and-evals.md_
- **Evals:** _placeholder, defined in M5 bounds-and-evals.md_

## The golden rule, applied

- **Decide relevant context** sits above the line because it's medium to reverse, has a medium blast radius, and is low to verify — deciding factor: measurability.
- **Decide tone** sits at a HITL checkpoint because it's high to reverse and has a low blast radius, but is low to verify — deciding factor: measurability.
- **Decide commitment level / risk rating** sits above the line because it's low to reverse and has a high blast radius, and is medium to verify — deciding factor: reversibility and blast radius.
- **Choose what to escalate** sits at a HITL checkpoint because it's low to reverse, and has a medium blast radius and medium measurability — deciding factor: reversibility.
- **Post the update / approve a company-wide one** sits above the line because it's low to reverse and has a high blast radius, and is medium to verify — deciding factor: reversibility and blast radius.

## Hardest call

Choose what to escalate (#7) — the toughest call to place. I don't fully trust agents or LLMs to know all the details behind why something needs to be escalated in every case, which is what pushed it to a HITL checkpoint rather than fully below the line.
