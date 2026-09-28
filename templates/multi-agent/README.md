# Multi-agent templates

**Start with one agent.** Add more only when a single agent hits a concrete limit.

## When several agents are worth it

| Situation | Why it helps |
|---|---|
| The task no longer fits one context window | Subagents explore separately and return short summaries, so the main context stays clean |
| Independent subtasks on different files | They can run at the same time without touching the same files |
| Different roles or permissions | A read-only explorer, a writer limited to `tests/`, a reviewer with no edit rights |
| Independent verification | A reviewer in a fresh context does not inherit the author's assumptions |

## When to stay with one agent

- The task is small or well scoped: coordination costs more than it saves.
- The steps are coupled or sequential: each depends on the previous one.
- You cannot yet verify one agent's output: more agents means more output to check.
- Cost, latency or traceability matter: more tokens, more waiting, harder to debug.

**A useful test:** can you describe, in one sentence each, the tasks you would give the subagents, and check the result of each one? If not, the task is not ready to be split.

## Four common patterns

1. **Orchestrator and workers**: one agent splits the task and delegates.
2. **Parallel workers**: independent subtasks run at the same time (different files!).
3. **Pipeline**: for example planner, then implementer, then reviewer, with explicit hand-offs.
4. **Author and independent reviewer**: a second agent, in a fresh context, checks the first one's work.

## How it works in Kilo Code (from the documentation, September 2026)

- **Subagents** are agents with `mode: subagent` (see `../kilo-project-kit/.kilo/agents/`). They are called by an agent through its `task` tool, or by you with `@agent-name`.
  When a subagent finishes, only a **summary** goes back to the parent agent. A subagent cannot ask you questions directly when an agent invoked it.
- The agents with full tool access (**Code, Plan, Debug**) can delegate to subagents by themselves. The former **Orchestrator** agent is marked *deprecated* in the current documentation: you no longer switch to it before a complex task. Older versions of the extension may still show it.
- **Control who may be called** with `permission.task` (for example allow `reviewer` and `test-writer`, ask for the others). The `steps` setting of an agent limits how many iterations it may run.
- Built-in subagents named in the documentation: **`explore`** (fast, read-only codebase exploration) and **`general`**.
- **Agent Manager** (VS Code): run several sessions in parallel, optionally each in its own **git worktree** (a separate checkout on its own branch), then review and apply the changes. It requires a git repository.
- Foreground tasks return before the parent continues; background tasks let the parent continue and deliver their result later.

## What to remember

- A subagent only knows what you tell it: **write a precise brief** (`brief-template.md`). Hand-offs lose information.
- **Never let two agents edit the same file.** Give parallel agents separate branches or worktrees.
- Errors compound: a wrong early assumption can spread. Keep summaries short and verifiable.
- **You are the orchestrator of record**: you integrate, run the tests and read every diff, whoever wrote the code.
- Measure: did it really save time compared with a single agent? If not, keep it simple.

## Files

- `brief-template.md`: the brief to give each subagent or session.
- `delegation-prompt.md`: a prompt for a Code or Plan agent that may delegate to subagents.
- `../kilo-project-kit/.kilo/agents/`: `reviewer` and `test-writer` subagents, ready to adapt.
