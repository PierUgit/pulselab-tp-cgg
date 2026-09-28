# Prompt for an agent that may delegate

Use with a Code or Plan agent. Adapt the names to the subagents defined in your project (`.kilo/agents/`).

```
Task: {{task_in_one_sentence}}

You may use subagents, but only for pieces that are independent:
- `explore` (built-in, read-only) to map {{area}} and return a summary of at most 10 lines;
- `test-writer` to write the tests for {{function}} in tests/ only;
- `reviewer` (fresh context) to review the final diff.

Rules:
- Give each subagent a written brief: goal, allowed files, out of scope, deliverable, check.
- Never let two subagents edit the same file.
- Do the integration yourself (wiring, command line, golden files), run `python -m pytest -q`, and show me the diff before anything is committed.
- If the task is small or tightly coupled, do it yourself without subagents and tell me why.
```

You can also call a subagent directly: `@reviewer review my last change`.
