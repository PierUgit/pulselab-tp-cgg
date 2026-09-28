# Prompt templates

Reusable, tool-agnostic prompts built on the **six blocks**: goal, context, constraints, examples, format, verification.
Copy one, replace every `{{placeholder}}`, delete what you do not need. Keep your adapted versions in your own repository (for example in `prompts/`), versioned and reviewed like code.

| File | Use it to | Kilo Code agent |
|---|---|---|
| `01-explore-codebase.md` | understand an unknown project, with evidence | Ask |
| `02-plan-feature.md` | plan a change across files before any code | Plan |
| `03-root-cause-analysis.md` | find the cause of a bug, no fix yet | Ask |
| `04-bugfix.md` | fix a bug after the cause is known | Code |
| `05-new-function.md` | write a new numerical function | Code |
| `06-write-tests.md` | plan then write tests | Plan, then Code |
| `07-characterization-tests.md` | lock current behaviour before a refactoring | Code |
| `08-refactor-with-contract.md` | refactor with invariants and increments | Plan, then Code |
| `09-review-diff.md` | get an advisory review of a diff | Ask |
| `10-property-based-tests.md` | write property-based tests | Code |

In Kilo Code, attach files with `@/path/to/file`, the terminal output with `@terminal`, and your uncommitted diff with `@git-changes`.
The same prompts work with any other assistant: only the way you attach context changes.
