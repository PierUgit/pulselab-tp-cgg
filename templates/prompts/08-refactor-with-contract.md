# Prompt template: refactoring with a contract (v1.0)

Use with: a planning agent for the plan (Kilo Code: *Plan*), then a coding agent, one increment per prompt (*Code*).

## Refactoring contract (you write it, the agent does not)
```
Goal:        {{what_improves_in_one_sentence}}
Invariants:  {{what_must_not_change: outputs, CLI, golden files}}
Forbidden:   new dependencies, editing tests/golden/*, weakening or deleting an assertion, touching files outside the plan
Done when:   {{commands_that_must_succeed}}; {{searches_that_must_return_nothing}}
Rollback:    one commit per increment on branch {{branch}}; `git restore .` or `git revert`
```

## Prompt 1: the plan
Plan only, do not edit any file. Refactor {{scope}} as follows: {{target_design}}.
Follow the contract above. Give an ordered plan in increments so that the tests can run after each increment: files touched, risks, and the command that proves each increment.

## Prompt 2..n: one increment
Execute increment {{n}} of the plan only. Rules: do not touch tests/golden/; no new dependency; do not weaken any assertion; if a test must change, tell me first.
Run the tests and show the result. Stop after this increment.

## After each increment
Run the tests, read the diff (test files first), commit.
