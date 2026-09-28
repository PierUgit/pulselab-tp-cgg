# Kilo Code project kit

A ready-to-copy set of team files for a scientific Python project: instructions, rules, permissions, agents, skills and slash commands.

```
kilo-project-kit/
  kilo.jsonc                 config: instructions list + permissions (edit it first)
  AGENTS.md                  project guide (fill in the <placeholders>)
  .kilo/
    rules/                   scientific-conventions.md, testing.md, security-and-data.md
    agents/                  reviewer (subagent, read-only), test-writer (subagent, tests only), docs-writer (primary, *.md only)
    skills/                  verify-numerical-change, characterization-tests, scientific-code-review
    commands/                /plan-feature, /root-cause, /review-diff, /test-plan
```

## Install

1. Copy the **contents** of this folder into the root of your project, including the hidden `.kilo` folder.
   If your project already has a `kilo.jsonc`, merge the `instructions` and `permission` keys by hand instead of overwriting it.
2. Open `AGENTS.md` and replace every `<placeholder>` with your project's facts. Keep it short.
3. Read `kilo.jsonc` line by line and adapt the permissions (commands you allow without a prompt, files you protect).
4. Reload: in the CLI use `/reload` or start a new session; in VS Code reload the window. (The documentation says project permissions are cached until you do.)
5. Commit the kit: these files are shared team assets and are reviewed like code.

## Smoke test (5 minutes): do not skip it

These files were checked against the Kilo Code documentation and their syntax was validated, but they were **not run inside Kilo Code** by the author. Prove they work in *your* version:

| Test | What you should see |
|---|---|
| Ask the agent: "Which instructions and rules are you following in this project?" | It mentions the content of `AGENTS.md` and of `.kilo/rules/`. |
| Ask the agent to edit a file under `tests/golden/`. | The edit is denied, or at least you are asked first. If it is **not**, the rule order is not what you think: see the note below. |
| Ask it to run `git status`, then `rm somefile`. | `git status` runs; `rm` is refused. |
| Type `/plan-feature` (CLI or chat). | The command exists and switches to the plan agent. |
| Type `@reviewer` followed by a request to review the last change. | The subagent runs and returns a short review. |
| Ask something that matches a skill (for example "verify this numerical change"). | The agent loads the skill (use `/reload` after adding skills). |

## Notes and known limits

- **Rule order.** The documentation of agent permissions says the *last matching rule wins*, so broad rules come first and exceptions after. Some example snippets in the documentation use the opposite order. These files follow the rule as written; if the smoke test shows the opposite behaviour, reverse the order of the entries.
- **Defaults.** Two pages of the documentation disagree on what happens for a tool with no rule (ask or allow). That is why `kilo.jsonc` writes every rule you care about explicitly.
- **Slash commands.** The documentation does not say how text typed after `/command` is passed to the command. The commands here therefore tell the agent that you will describe the task **in your message**. Check the behaviour in your version.
- **Agents and modes.** Custom modes are called *agents* in the current documentation. Older versions used `.kilocodemodes` and a `.kilocode/` folder; the documentation says these are read or migrated automatically.
- **Global vs project.** Everything here is project-level. Global equivalents live under `~/.config/kilo/` (see the documentation); avoid duplicating a rule in both places.
- **Windows.** The permission matcher normalizes backslashes; on Windows matching is case-insensitive (documentation).
