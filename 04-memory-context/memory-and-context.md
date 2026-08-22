# Context Engineering & Memory: Cortex PM Chief-of-Staff Agent

> Module 4 · Context Engineering & Memory
>
> ✅ **What this validates:** the agent reasons on the right, safe inputs — by the end you'll have proven a context budget, per-source retrieve-vs-long-context decisions, and a memory map with risk mitigations.
>
> 🗂️ **How the lab maps to this file:** In **Part A** (before the lecture) you don't edit this file — you rough-draft on scratch, focused on the per-source calls in **section 2** plus a quick remember/forget + "how it rots" sketch. In **Part B** (after the lecture) you complete **all five sections**; the Lab Guide's guided builder writes this file for you to copy in and commit.

## 1. Context budget

Priority order when context space is tight, highest first: 1) the task brief, 2) team norms, 3) current project + activity, 4) roadmap (filtered), 5) past-updates search results — truncated first, since it's precedent/nice-to-have, not required evidence.

## 2. Retrieve vs. long-context: per source

For each data source, decide: **retrieve** (narrow a large/changing corpus to the relevant slice) or **long-context** (just include a bounded set you can reason over).

| Source | Size / volatility | Decision | Why |
|---|---|---|---|
| `get_task` | Bounded, one static doc per run | Long-context | Must be seen in full; no selective querying makes sense for a single task brief |
| `get_activity` | Large, grows unboundedly per project | Retrieve | Needs citation of specific PR/issue IDs, so it must be queried per-project rather than dumped whole |
| `search_past_updates` | Unbounded corpus across all history | Retrieve | Already keyword-searched rather than loaded in full |
| `get_roadmap` | Medium, has confidential/embargoed flags | Retrieve | Confidentiality means it needs targeted, filtered access — dumping the whole roadmap risks a leak even with instructions not to use it |
| `get_norms` | Medium, must stay current | Retrieve | Baking it into a static long-context prompt risks working off stale norms; fetching fresh each run keeps it live |

## 3. Retrieval quality plan

| Source | Move(s) | Why |
|---|---|---|
| `get_activity` | Self-verification | Claims must be traceable to actual activity pulled — the critic already catches this (e.g. an ungrounded "Green" status relative to an open issue) |
| `search_past_updates` | Document grading | Must grade whether a match is actually about the right project before treating it as precedent — naive keyword search returned irrelevant Northstar/Vega precedent for a "P-HALO" query |
| `get_roadmap` | Routing + Self-verification | Route to the audience-appropriate view (internal vs. company-wide); verify no CONFIDENTIAL/embargoed item leaked into the final draft |
| `get_norms` | Caching (short TTL) + Self-verification | Cache since norms don't change every run, refreshed periodically to stay current; verify the draft complies with the cited norm |

## 4. Memory map (your PM brain)

| Memory type | What Cortex stores | Scope / TTL |
|---|---|---|
| **Working** (in-loop) | This run's task brief, pulled project/activity/roadmap/norms, draft-in-progress, critic feedback during revision | This run only — purged after it ends |
| **Episodic** (past runs) | Past status updates + decision log, used as precedent via `search_past_updates` | Long-lived, but only ever written once a human actually approves and posts an update — a draft or escalated attempt never gets appended |
| **Semantic** (durable facts/prefs) | Team norms/playbook, prior commitments/dates already communicated | Durable until explicitly superseded — norms until the playbook changes; a commitment until its date resolves or is revised |
| **Shared** (across agents) | Source data + the draft itself, shared between Cortex and the critic during one review exchange | One review call only; each agent's own reasoning stays isolated |

## 5. Memory risks & mitigations

| Risk | Where it bites Cortex | Mitigation |
|---|---|---|
| **Drift** | Precedent from past updates could subtly shift tone/facts over many runs without anyone noticing | Precedent is for tone/style only — live retrieved data (activity, roadmap, norms) always overrides episodic precedent on facts |
| **Poisoning** | A task brief with injected instructions, or a bad/fabricated entry in past-updates/decision-log, propagating as if it were real precedent | Brief content is data, not instructions (already enforced); episodic memory is only ever written from a human-approved, actually-posted update — nothing unapproved enters the record |
| **Staleness** | Cached norms serving outdated policy after a real change | Short TTL + forced refresh (e.g. daily) on cached norms; roadmap is never cached, fetched fresh every run |
| **Confidential / retention (PII)** | Episodic logs could accumulate individual-attributed blame or retain confidential roadmap items indefinitely | Scope episodic storage to project/business facts, not individuals; same CONFIDENTIAL-flag enforcement as the roadmap; retention window on episodic logs, purged after (ties forward to M5 bounds) |
