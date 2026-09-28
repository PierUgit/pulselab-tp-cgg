# Templates

Reusable files to adapt in your team. Copy them into your own repository, change them, review them like code.

| Folder | What is inside |
|---|---|
| `prompts/` | Ten tool-agnostic prompt templates built on the six blocks (explore, plan, root cause, bug fix, new function, tests, characterization tests, refactoring contract, review, property-based tests) |
| `kilo-project-kit/` | A copy-ready Kilo Code setup: `kilo.jsonc` (instructions + **permissions**), `AGENTS.md`, `.kilo/rules/`, `.kilo/agents/` (reviewer, test-writer, docs-writer), `.kilo/skills/` (3 skills), `.kilo/commands/` (4 slash commands) |
| `multi-agent/` | When several agents are worth it and when not, how Kilo Code subagents work, a brief template, a delegation prompt |
| `team/` | Charter, review checklist, AI journal, pilot plan, workflow card |
| `automation/` | Pre-commit hook with an advisory AI step, review criteria for a bot, a CI draft |

## What was checked, and what was not

**Checked**
- The file locations, key names and rules of the Kilo Code files were read in the **official documentation (kilo.ai/docs) in September 2026** (sources are listed in `../AI_Coding_Best_Practices.md`).
- Syntax was validated by a script: `kilo.jsonc` parses (comments removed), every `SKILL.md` has a valid `name` (lowercase, digits, hyphens, at most 64 characters, equal to its folder name) and a `description` (at most 1024 characters), every agent and command has valid YAML frontmatter that only uses keys named in the documentation.
- `automation/precommit.py` was replayed in a scratch git repository (six scenarios).

**Not checked**
- The kit was **not run inside Kilo Code** by its author (it was not installed where the files were written). Use the smoke test in `kilo-project-kit/README.md`.
- The documentation of Kilo Code changes quickly and is not always consistent with itself (defaults of permissions, order of rules in examples). Check your installed version.
- `automation/ci-example.yml` was never executed.

## Conventions used in the templates

- `{{double_braces}}` in prompts: replace with your own value.
- `<angle brackets>` in files (AGENTS.md, charter): a value to fill in.
