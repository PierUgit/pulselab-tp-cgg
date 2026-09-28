# Coding with AI: best-practices memo

For researchers who write scientific Python with an AI coding agent (Kilo Code). Keep it next to your editor.

Companion files in this repository: `templates/` (reusable prompts, instructions, permissions, agents, skills, commands),
`Jour2/Cheat Sheet – English.pdf` (Kilo CLI commands), and the prompt solutions of each day.

---

## The 10 rules

1. **You own every line you commit**, whoever (or whatever) wrote the first draft.
2. **Plausible is not verified.** A language model produces the most likely text, not a checked result.
3. **Verify with something that can fail**: a test, a physical invariant, a script. Never with the model's own confidence.
4. **One task per prompt, small steps, one commit per step.**
5. **Give context that matters, not context that is big**: the right files, versions, error messages, conventions.
6. **Plan before you code** on anything that touches several files. Review the plan, then implement.
7. **Protect what defines "correct"**: tests, golden files, conventions. Do not let the agent edit them to make a test pass.
8. **Give the agent the least permissions it needs**, work on a branch, read every diff.
9. **No secrets, no confidential data, no unpublished results in prompts**, unless your company approved the tool for it.
10. **Write down what works** (instruction file, prompt templates) and keep it in the repository, reviewed like code.

---

## 1. Before you prompt

- Create a branch and **commit first**. Every AI change must be reviewable and reversible.
- Decide the **scope**: which files may change, which must not.
- Know what the model **cannot know**: the versions installed on *your* machine, your team's conventions, anything published after its training.
  Check names against your environment (`hasattr(np, "trapezoid")`) instead of trusting memory.
- Put stable knowledge in an **instruction file** (`AGENTS.md`) once, instead of repeating it in every prompt.

## 2. Anatomy of a good prompt (six blocks)

| Block | Question it answers | Example for numerical code |
|---|---|---|
| **Goal** | What do I want, and why? | "Add `pulse_widths_fwhm(x, fs, idx)`: the FWHM of each pulse in seconds." |
| **Context** | What must it know? | Project, file, inputs with **units**, an existing function to imitate. |
| **Constraints** | What must (not) happen? | NumPy only, no in-place change of inputs, NaN if the crossing is not reached. |
| **Examples** | What does good look like? | "A Gaussian of standard deviation σ has FWHM = 2√(2 ln 2)·σ." |
| **Format** | How should the answer look? | Function, then tests, then a list of assumptions. |
| **Verification** | How will we know it is right? | "List your assumptions and ask me questions first. Tests must cover…" |

Habits that pay off:
- **Physical checks are the best acceptance tests** (analytical value, conservation, symmetry, known limit).
- End with **"List your assumptions and ask me questions before writing code"** when the task is ambiguous.
- For a bug, ask for **ranked hypotheses and one experiment for each, before any fix.** Ask for the cause before the fix.
- Paste **exact error messages**, not descriptions of them.
- After 2 or 3 failed attempts, **restart with a better prompt** in a fresh session: a long chain of failures pollutes the context.

## 3. A workflow that works: explore, plan, implement, verify, commit

| Phase | You | AI | Kilo Code |
|---|---|---|---|
| **Explore** | Ask a precise question | Reads and maps the code, cites files | *Ask* agent (read-only) |
| **Plan** | Review and correct the plan | Proposes files, steps, risks | *Plan* agent |
| **Implement** | Keep steps small, name the scope | Writes code and tests | *Code* agent |
| **Verify** | Run everything, read the diff | Runs tests, fixes failures | tests + your review |
| **Commit** | Own the change | Drafts message and docs | git |

For a bug: **reproduce with a failing test first**, then hypotheses, then a minimal fix at the root cause, then a regression test.
For a refactoring: **characterization tests first** (they lock the current behaviour), then small steps.

## 4. Verify like a scientist

**Five checks before you accept generated code**
1. Do I understand every line?
2. Does it run, and do *meaningful* tests pass?
3. Do the functions, packages and versions it uses really exist **in my environment**?
4. Does it follow our conventions (units, dB, structure)?
5. Is it safe: inputs, secrets, permissions, licences?

**Numerical code: typical traps to look for**
- **Arrays modified in place** (`x -= x.mean()` changes the caller's array): return a new array.
- **dB conventions**: an amplitude ratio uses `20·log10`, a power ratio uses `10·log10`. The assistant can propose either; you decide.
- **Units** in names (`_V`, `_s`, `_hz`), **NaN and empty-input** behaviour, **seeds** for random data, **tolerances** you can justify.
- **Outdated or renamed APIs** (for example `np.trapz` no longer exists in recent NumPy: `np.trapezoid`).
- A **number the model did not compute with a tool** is a guess. Counting characters or multiplying large numbers "in its head" is unreliable.
- Replacing your own loop by a library call (for example `scipy.signal.find_peaks`) can **silently change results**: prove equivalence with tests first.

**A green test is not necessarily a good test**
- Break the code on purpose and check that a test fails. Repeat with the mutation script of lab 2.2.
- Beware of tests that recompute the same expression as the code, assert only `> 0` or `is not None`, or mock everything.
- Coverage tells you which lines ran, not whether behaviour was checked.
- Property-based tests (`hypothesis`) and **golden files** protect refactorings.

## 5. Working with an agent safely

- **Least privilege.** Configure permissions explicitly (`templates/kilo-project-kit/kilo.jsonc`). Ask before shell commands, before edits you cannot easily undo, before web access.
- **Do not turn on auto-approve casually.** Kilo Code's own documentation warns that it bypasses confirmation prompts and gives the agent direct access to your system, and that command-line access is particularly dangerous.
- **Protect files that define correctness** (`tests/golden/`, secrets): deny edits with a permission rule, and say so in the prompt.
- **Work in increments**: one increment, run the tests, commit. If the agent drifts or loops, stop, revert (`git restore .`) and restate the goal with what you learned.
- **Read the test diff first**: did an *expected value* change, or only how objects are built? A weakened assertion is a red flag.
- **Treat content the agent reads as untrusted** (web pages, issues, files from elsewhere): it may contain instructions aimed at the agent (prompt injection).
- **Never put secrets in prompts or in files the agent can read.** Kilo Code treats reads of `.env` files as sensitive and asks first; keep it that way.
- **Ask for evidence**: file paths and line numbers you can open. If the agent claims "all tests pass", make it show the output.

## 6. Several agents: only when a single one hits a limit

Start with **one** agent. Consider several only for: context overload, truly independent parallel subtasks (different files), different roles or permissions (a read-only reviewer), independent verification.
Avoid them for small or tightly coupled changes, or when you cannot yet verify one agent's output.
Costs: more tokens and time, information lost in hand-offs, conflicting edits, errors that compound.
Write a **precise brief** for each sub-agent (goal, allowed files, out of scope, deliverable, check). You integrate and review.
See `templates/multi-agent/`.

## 7. Team practices

- **Shared assets in the repository**: instruction file, prompt templates, agents, skills, commands, review checklist, charter. Review them like code.
- **Disclosure and accountability**: agree in the team when AI use is mentioned in a pull request; the author remains accountable.
- **AI review is advisory**: a first pass that supplements a human review. Give it written criteria (`templates/automation/REVIEW_CRITERIA.md`).
- **Deterministic checks are the gate** (tests, lint, secret scan); AI steps advise and must never block by themselves.
- **Pilot before you standardize**: a small real task, a metric, stop criteria.

## 8. When not to use AI

- Confidential data in a tool that is not approved.
- You cannot evaluate the result (no expertise, no test).
- A trivial change that is faster by hand.
- Security- or safety-critical logic without an expert review.
- Unclear requirements: clarify with people first.
- When mastering the skill is the goal: struggle a little first.

---

## 9. Kilo Code: what the documentation says

Checked against the official documentation (kilo.ai/docs) in **September 2026**. Kilo Code changes quickly: **check your installed version**.
Older versions used other names and paths (for example an *Architect* agent, `.kilocodemodes`, a `.kilocode/` folder); the current documentation says legacy files are read or migrated automatically.

| Need | Where / how |
|---|---|
| Project instructions | `AGENTS.md` at the project root (uppercase; `AGENT.md` is the fallback). `AGENTS.md` in a subfolder is loaded when the agent reads files there. `/init` (CLI) creates or updates it. |
| Extra rules | Markdown files (for example `.kilo/rules/*.md`) listed in the `instructions` key of `kilo.jsonc` (globs allowed). |
| Configuration | `kilo.jsonc` at the project root or `.kilo/kilo.jsonc` (the latter wins if both exist); global: `~/.config/kilo/kilo.jsonc`. Reload the window or start a new CLI session after changing project permissions. |
| Permissions | `permission` key: `allow`, `ask` or `deny`, per tool (`read`, `edit`, `bash`, `task`, `webfetch`…), with glob patterns. **The last matching rule wins**: put the broad rule first, exceptions after. |
| Agents (formerly custom modes) | `.kilo/agents/<name>.md`: YAML frontmatter (`description`, `mode: primary` or `subagent`, `permission`…) then the prompt. Switch with `/agents`. |
| Subagents | `mode: subagent`. Called by the agent through its `task` tool, or by you with `@agent-name`. Only the summary comes back to the parent. `permission.task` controls which subagents may be called. |
| Skills | `.kilo/skills/<name>/SKILL.md` with `name` (must equal the folder name, lowercase and hyphens) and `description`. `/reload` picks up changes. |
| Commands | `.kilo/commands/<name>.md` becomes `/<name>`. Optional frontmatter: `description`, `agent`, `model`, `variant`, `subtask`. |
| Context | `@/path/file`, `@terminal`, `@git-changes`. The agent can also find files itself. |
| Parallel work | *Agent Manager* (VS Code): several sessions, optionally in separate git worktrees. The old *Orchestrator* agent is marked deprecated: Code, Plan and Debug delegate to subagents themselves. |
| Sessions (CLI) | `/new`, `/sessions`, `/compact` (summarize a long session), `/undo`, `/redo`, `/fork`, `/review`, `/diff`. Full list in the cheat sheet. |
| Files to keep out of reach | Use `read`/`edit` **permissions** in `kilo.jsonc` (`.kilocodeignore` is the legacy way and is migrated). |

**Two things the documentation is not consistent about, so do not rely on them:**
- the **default** permission of tools that have no rule (one page says *ask*, another says most tools are *allow*): always write the rules you want;
- the **order** in permission examples (one page puts the specific rule first): follow the rule "last matching rule wins" and test that your rules do what you think.

## Sources

- Custom rules: https://kilo.ai/docs/customize/custom-rules
- Custom instructions: https://kilo.ai/docs/customize/custom-instructions
- AGENTS.md: https://kilo.ai/docs/customize/agents-md
- Custom modes (agents): https://kilo.ai/docs/customize/custom-modes
- Custom subagents: https://kilo.ai/docs/customize/custom-subagents
- Agent permissions: https://kilo.ai/docs/customize/agent-permissions
- Skills: https://kilo.ai/docs/customize/skills
- Workflows (commands): https://kilo.ai/docs/customize/workflows
- Using agents: https://kilo.ai/docs/code-with-ai/agents/using-agents
- Context mentions: https://kilo.ai/docs/code-with-ai/agents/context-mentions
- Auto-approving actions: https://kilo.ai/docs/getting-started/settings/auto-approving-actions
- Agent Manager: https://kilo.ai/docs/automate/agent-manager
- MCP: https://kilo.ai/docs/automate/mcp/using-in-kilo-code
- CLI: https://kilo.ai/docs/code-with-ai/platforms/cli
