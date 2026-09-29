# TP 2.3 with Kilo Code — trainer notes

This folder turns the TP 2.3 multi-agent mini-lab (three briefs, three sub-agents, one
integration) into a concrete Kilo Code setup, so you can run or demo it instead of only
describing it. It is the filled-in counterpart of
`pulselab-tp-participants/templates/multi-agent/` (generic templates for the participants'
own projects) — here every file names the real pulselab functions, branches and tests.

## What is here

```
.kilo/
  kilo.jsonc          project config: default permissions + which sub-agents may be called (permission.task)
  agents/
    explorer-a.md      sub-agent A: read-only, may only write docs/ARCHITECTURE.md
    fit-b.md            sub-agent B: may only edit pulselab/fit.py + tests/test_fit_uncertainty.py
    export-c.md          sub-agent C: may only create pulselab/export.py + tests/test_export.py
briefs/
  BRIEF_A.md, BRIEF_B.md, BRIEF_C.md   the three briefs, verbatim (identical to the ones in
                                         Jour2/Prompt_Solutions_Day2.md given to participants)
DELEGATION_PROMPT.md  ready-to-paste prompt for a Code agent that orchestrates the three sub-agents
```

Copy `.kilo/`, `briefs/` and `DELEGATION_PROMPT.md` to the root of a checkout of the Day 2
starter (`pulselab_day2_starter.zip`) before the session — not into the reference solution
in this folder, which already contains the finished `fit.py` / `export.py` / tests.

## Two ways to run the demo

**A. Agent Manager (closest to "real" isolation).** Create the three branches
(`docs-arch`, `feat-fit-uncertainty`, `feat-json-export`) as the lab already shows, open the
Agent Manager, and start one session per branch, each in its own git worktree. Paste the
matching `briefs/BRIEF_*.md` as the first message of each session, using a plain Code agent
(no need to `@mention` a sub-agent here: the worktree already gives the isolation). This is
the closest to "three separate people working at once" and best shows *why* isolation matters
(a session literally cannot see the others' files).

**B. Sub-agents in one session (closest to what the lab briefs describe as "your tool's
sub-agents if it has them").** From a single Code session at the repo root, either let the
agent call `task` itself after you paste `DELEGATION_PROMPT.md`, or call each one directly and
sequentially: switch to its branch, then `@explorer-a`, `@fit-b`, `@export-c`, each with its
brief. `permission.task` in `.kilo/kilo.jsonc` allows exactly these three names; anything else
falls back to "ask". A sub-agent only returns a summary to whoever called it — if you called it
directly, that summary comes back to you.

Either way, **you do the integration step yourself** (merge, wire `report.py` and
`scripts/run_analysis.py`, update the golden file on purpose, run the tests) — that step is
intentionally not delegated, per the lab and per `templates/multi-agent/README.md`
("you are the orchestrator of record").

## What to expect, and the honest lesson

Reference result after integration: **90 tests pass**; the golden CSV fails on purpose right
after the merge (1 failed, 89 passed) until you regenerate it; run01 `tau_s` ≈ 3.683 ± 0.180 s.
These numbers come from real runs of the reference code (see the Day 2 lab sheet's *Verified
facts* table), not from a specific AI assistant.

The debrief question is deliberately "was it worth it?": for a change this size, briefing,
running and integrating three sub-agents usually costs **more** than doing it in one session —
that is the point of the exercise, not a failure of the setup.

## Not verified

The `.kilo/` files here follow the Kilo Code documentation as read on kilo.ai/docs in
September 2026 (same facts as `templates/kilo-project-kit/README.md`) and were checked for
schema correctness only (frontmatter keys, permission values, that `permission.task` names
agents that actually exist). They were **not run inside Kilo Code**. Before using this live,
run the smoke test described in `templates/kilo-project-kit/README.md` and confirm on your
installed version that: `.kilo/agents/*.md` are picked up (`/reload`, or `/agents`), a plain
`@explorer-a` message reaches the sub-agent, and a denied `edit` path is actually blocked.
The Kilo documentation is also not fully consistent on the default permission when no rule
matches a tool, and on rule ordering in some examples — this config avoids the question by
setting every rule this lab needs explicitly.
