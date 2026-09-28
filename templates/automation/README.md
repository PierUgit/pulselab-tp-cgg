# Automation templates

| File | What it is | Status |
|---|---|---|
| `precommit.py` | Pre-commit hook: **blocking** deterministic checks (secret scan, syntax, fast tests) + an **advisory** AI review that can never block | Tested in a scratch git repository with six scenarios (below) |
| `REVIEW_CRITERIA.md` | Criteria given to an AI reviewer or a pull-request bot: what to check, what to ignore, output format | Text file |
| `ci-example.yml` | A CI draft with a gating `tests` job and an advisory, disabled `ai-review` job (GitHub Actions syntax) | YAML syntax checked; **never executed**. Adapt it to your CI system |

## Principle

**Deterministic checks are the gate; AI advises.** An AI step must never decide the exit code of a commit or a build, must be fail-safe (errors and timeouts only print a warning), and must not receive more data than necessary.

## Install the hook

1. Copy `precommit.py` to `tools/precommit.py` in your repository (the script finds the repository root from its own location).
2. Edit the constants at the top: `SOURCE_DIRS` (folders to syntax-check) and the test command if needed.
3. Run `python tools/precommit.py --install`: it writes `.git/hooks/pre-commit`.
4. Optional advice from an AI tool: set `AI_REVIEW_CMD` to a command that reads the staged diff on **stdin** and prints comments on **stdout** (use a company-approved tool), and optionally `AI_REVIEW_TIMEOUT` (seconds, default 60).

## The six scenarios to replay in a scratch repository

| # | Situation | Expected |
|---|---|---|
| S1 | clean commit | passes |
| S2 | a test is broken | commit **blocked**, failing test shown |
| S3 | a staged file contains `API_KEY = "abcd1234efgh5678"` | commit **blocked**, the line is shown |
| S4 | the AI command prints advice | advice shown, commit passes |
| S5 | the AI command exits with an error | warning shown, commit passes |
| S6 | the AI command is slower than the timeout | warning shown, commit passes |

## Before you use it

- What does the AI command **receive**? (the staged diff, truncated to 20 000 characters.) Could it contain secrets, unpublished results, personal or confidential data? Where does the command run and where does the data go? Who approved the tool for this data?
- The secret scan is two simple regular expressions: it is predictable but **not** a complete secret scanner.
