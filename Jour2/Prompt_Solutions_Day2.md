# Day 2: prompt solutions

These are **model answers**, not the only right ones. Try each step with your own prompt first, then compare:
which blocks (goal, context, constraints, examples, format, verification) did you have that the model prompt does not, and the other way round?

Each entry gives the lab step, the suggested mode, and the prompt. Lab 2.4 (team assets) and lab 2.5 (automation) use the files in `templates/`.

## Kilo Code quick mapping

| Mode used in the labs | In Kilo Code |
|---|---|
| CHAT | Ask agent |
| PLAN | Plan agent |
| EDIT | Code agent, with `edit` on *ask* so you review each change |
| AGENT | Code agent (or Debug agent) |
| REVIEW | Ask agent, with `@git-changes` (or `/review` in the CLI) |

Useful in any prompt (Kilo Code documentation, checked in September 2026): attach a file with `@/path/to/file` (drag and drop from the Explorer also works),
`@terminal` adds the terminal output, `@git-changes` adds your uncommitted diff. The agent can also find files itself with its `read`, `grep` and `glob` tools.
Names can differ in older versions of the extension (for example a planning agent called *Architect*): check what your installation shows.

> The prompts below mention file paths such as `pulselab/fit.py`: in Kilo Code write them as `@/pulselab/fit.py` if you want to attach the file explicitly.

---

## Lab 2.1: Large-scale refactoring with an agent

*Module 5 · Using AI agents for complex tasks (1/3)*

### 2.1.1 Refactoring contract (a filled example)

**Mode:** HUMAN

```text
REFACTORING CONTRACT
Goal:        replace the global CONFIG dict and the run dicts by explicit Config and Run dataclasses (plus pathlib, logging, type hints) without changing any result.
Invariants:  tests/golden/summary_expected.csv and tests/golden/pulses.json are untouched and the tests pass; the CLI options of scripts/run_analysis.py are unchanged; no new dependency.
Forbidden:   editing tests/golden/*, weakening or deleting an assertion, adding a dependency, touching files outside the plan.
Done when:   python -m pytest -q passes; `git diff main -- tests/golden` prints nothing; `git grep -nE "CONFIG|os\.path|glob\.glob" -- pulselab scripts` prints nothing; python scripts/benchmark.py prints the same checksum as before.
Rollback:    one commit per increment on branch refactor-config; `git restore .` or `git revert` to go back.
```

*Reference prompt written for this handout (the lab sheet only gives a scaffold for this step).*

### 2.1.2 Ask for a plan and challenge it

**Mode:** PLAN

```text
Plan only, do not edit any file.
Refactor this project: (1) replace the global CONFIG dict in pulselab/config.py by a frozen dataclass Config (threshold_v, min_gap_s, band) passed explicitly; (2) replace the run dict returned by load_run by a Run dataclass (name, t, signal, temp, fs, meta); (3) use pathlib instead of os.path and glob; (4) use logging instead of print for messages (keep the results table printed by print_table); (5) add type hints to public functions.
Invariants: tests/golden/* untouched; the summary CSV and the pulses do not change; CLI options unchanged; no new dependency; do not weaken any assertion.
Give an ordered plan in increments so that the tests can run after each increment: files touched, risks, and the commands that prove each increment.
```

### 2.1.4 Increment by increment (tests after each, one commit each)

**Mode:** AGENT, EDIT

```text
Execute increment A of the plan only: create the frozen dataclass Config in pulselab/config.py (threshold_v=0.25, min_gap_s=0.05, band=None), the Run dataclass in pulselab/io.py, make load_run return a Run built from pathlib.Path, and use logging (logger = logging.getLogger(__name__)) instead of print in io.py.
Rules: do not touch tests/golden/; no new dependency; do not weaken any assertion; if a test must change, tell me first. After the change run python -m pytest -q and show the result. Stop after this increment.
```

---

## Lab 2.2: Advanced tests: mutation score, edge cases, properties

*Module 5 · Using AI agents for complex tasks (2/3)*

### 2.2.2 Ask for a test plan aimed at the survivors

**Mode:** PLAN, CHAT

```text
These mutants survived my test suite (python scripts/mini_mutation.py):
M2 pulselab/spectrum.py: freqs[1] - freqs[0] -> freqs[1] + freqs[0]
M3 pulselab/spectrum.py: (freqs >= fmin) & (freqs <= fmax) -> (freqs > fmin) & (freqs <= fmax)
M4 pulselab/spectrum.py: if not fmin < fmax -> if not fmin <= fmax
M9 pulselab/peaks.py: (mid > thr) -> (mid >= thr)
M11 pulselab/fit.py: (t_peaks[-1] - t_peaks[0]) or 1.0 -> ... or 2.0
Plan only. For each mutant give EITHER a concrete test (input, expected value, why it fails on the mutant) OR a proof that it is equivalent. Tests only: do not modify the source. Seeded random data only.
```

### 2.2.4 Property-based tests

**Mode:** EDIT, AGENT

```text
Write property-based tests in tests/test_properties.py with hypothesis (max_examples=50, deadline=None). Skip the whole module with pytest.importorskip if hypothesis is not installed.
Property 1 (remove_offset): for any finite float array of length >= 2 with values in [-1e3, 1e3], the input is not modified, the mean of the result is ~0 (tolerance relative to the data scale) and applying it twice gives the same result.
Property 2 (dominant_frequency): for any integer frequency f in [2, 200] Hz, fs = 1000 Hz, duration 4 s, any amplitude in [0.1, 5] on a constant offset, the result equals f within 0.3 Hz.
Property 3 (find_pulses): for any float array of length 3 to 400 with values in [-2, 2], the indices are increasing, consecutive indices differ by at least int(min_gap_s * fs), every returned sample is above the threshold, and the first and last samples are never returned.
Tests only: do not change the source. Run python -m pytest -q and show the result.
```

*Reference prompt written for this handout (the lab sheet only gives a scaffold for this step).*

---

## Lab 2.3: Multi-agent orchestration mini-lab

*Module 5 · Using AI agents for complex tasks (3/3)*

### 2.3.1 Briefs A and C (the brief for B is in step 2.3.2)

**Mode:** HUMAN

```text
BRIEF A (explorer, read-only)
Goal:          write docs/ARCHITECTURE.md (at most 60 lines): modules, data flow from a CSV file to the summary table, entry points, and 3 risks, with file references.
Context:       Python project pulselab (NumPy/SciPy/pandas). Read the code; do not run it and do not change it.
Allowed files: docs/ARCHITECTURE.md (create) only.
Out of scope:  every other file.
Deliverable:   the file.
Check:         every module and function you cite exists (open the file). Report back in at most 10 lines: what you wrote and which risks you chose.

BRIEF C (implementer)
Goal:          add write_json(rows, path) in a new module pulselab/export.py.
Context:       rows is the list of dicts produced by pulselab.report.analyze_folder. Values can be str, int, float, NaN or numpy scalars.
Constraints:   standard library only; NaN is written as null; numpy scalars are supported (convert with .item()); strict JSON (no NaN token); indent=2; the file ends with a newline.
Allowed files: pulselab/export.py and tests/test_export.py (new). Out of scope: report.py, the CLI, the golden files.
Tests:         NaN becomes null; numpy scalars are supported; the output is valid strict JSON ending with a newline; empty list.
Check:         python -m pytest -q. Report back in at most 10 lines.
```

*Reference prompt written for this handout (the lab sheet only gives a scaffold for this step).*

### 2.3.2 Run the three sub-agents in isolation

**Mode:** AGENT, CHAT

```text
BRIEF B
Goal: add fit_decay_with_error(t_peaks, heights) -> (tau, tau_err) to pulselab/fit.py.
Context: Python/SciPy project pulselab. fit_decay currently returns only tau by fitting a*exp(-t/tau) with scipy.optimize.curve_fit. tau_err must be the 1-sigma uncertainty sqrt(pcov[1,1]). Return (nan, nan) when the fit is impossible (no pulse, too few points). fit_decay must keep its behaviour (it can call the new function). Times are in seconds.
Allowed files: pulselab/fit.py and tests/test_fit_uncertainty.py (new). Out of scope: everything else, especially report.py and the golden files.
Tests: on noisy data with a known tau the true tau is within 3 sigma; larger noise gives a larger tau_err; empty input gives (nan, nan); fit_decay equals the first value.
Check: python -m pytest -q. Report back in at most 10 lines.
```

---

## Lab 2.4: Team conventions: instruction file, prompt templates, charter, review checklist

*Module 6 · Integrating AI into your team workflow*

### 2.4.1 Instruction file for the project

**Mode:** CHAT, AGENT, HUMAN

```text
Draft a project instruction file for this repository (at most 40 lines) for AI assistants and new colleagues. Include: commands (install, tests, run analysis, mutation check), the layout of pulselab/, scientific conventions (units in names, SNR in dB is an amplitude ratio so 20*log10, never modify input arrays in place, prefer vectorized NumPy), and rules for changes (tests for numerical changes, tests/golden updated only deliberately, never weaken an existing test, no new dependency without asking). Only include what cannot be guessed from the code.
```

---

## Lab 2.5: Automate the development cycle: hook, CI draft, review bot

*Module 7 · Automating the development cycle with AI*

### 2.5.2 Implement `tools/precommit.py` (one script, testable)

**Mode:** AGENT, EDIT

```text
Write tools/precommit.py, a pre-commit hook with no third-party dependency (Python standard library only).
Blocking checks, in order: (1) secret scan on the staged diff (added lines only) with regexes for api_key/secret/token/password assignments to long string literals and for private key headers; (2) python -m compileall -q pulselab scripts tests; (3) python -m pytest -x -q.
Advisory step, only if all blocking checks passed and env var PULSELAB_AI_REVIEW_CMD is set: run that command (shlex.split) with the staged diff on stdin (truncated to 20000 characters), print its stdout as advice. It must NEVER block the commit: on non-zero exit, on timeout (env PULSELAB_AI_TIMEOUT, default 60 s) or if the command cannot start, print a one-line warning and continue.
Add --install which writes .git/hooks/pre-commit (a shell script running python tools/precommit.py).
Then test these six scenarios in a scratch git repo and show the output of each: clean commit; broken test; secret in a staged file; AI advice printed; AI command exits 3; AI command slower than the timeout.
```

---

## Also in this repository

- `templates/`: reusable versions of these prompts (`templates/prompts/`), instruction files, permissions, agents, skills and commands for Kilo Code.
- `AI_Coding_Best_Practices.md`: the memo of good practices.
