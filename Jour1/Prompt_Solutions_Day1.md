# Day 1: prompt solutions

These are **model answers**, not the only right ones. Try each step with your own prompt first, then compare:
which blocks (goal, context, constraints, examples, format, verification) did you have that the model prompt does not, and the other way round?

Each entry gives the lab step, the suggested mode, and the prompt. Steps whose prompt is already given on the lab sheet (for example the four experiments of lab 1.1) are repeated here for completeness.

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

## Lab 1.1: LLM reality check: see the limits with your own eyes

*Module 1 · Understanding how AI actually codes*

### 1.1.1 Same prompt, two sessions: is the answer the same?

**Mode:** CHAT

```text
Write a Python function dominant_frequency(x, fs) that returns the dominant non-DC frequency in Hz of a real signal x sampled at fs Hz, using NumPy.
```

### 1.1.2 Blind to your code: ask about a function the model has not seen

**Mode:** CHAT

```text
In my Python project pulselab, what does the function fit_decay in pulselab/fit.py do, and what are its weaknesses? Quote the lines you refer to.
```

### 1.1.3 Outdated APIs: does the code still run on YOUR versions?

**Mode:** CHAT, HUMAN

```text
Write a short NumPy/SciPy snippet that (1) integrates sin(t) over [0, pi] with the trapezoid rule using np.trapz, (2) integrates it with Simpson's rule using scipy.integrate.simps, and (3) tests which elements of an array are in a list using np.in1d. Print the three results.
```

### 1.1.4 Tokens: counting and arithmetic without tools

**Mode:** CHAT, HUMAN

```text
How many times does the letter 'e' appear in: estimate_noise_from_first_differences
And what is 48213 * 7391 ? Answer directly, do not use a tool.
```

---

## Lab 1.2: Prompting lab: from a weak prompt to a reusable template

*Module 2 · Effective prompting for developers*

### 1.2.2 Weak prompt first (on purpose)

**Mode:** CHAT, EDIT

```text
Write a function to measure pulse width.
```

### 1.2.3 Six-block prompt

**Mode:** CHAT, EDIT

```text
GOAL: Add pulse_widths_fwhm(x, fs, idx) to a new module pulselab/pulses.py. It returns the full width at half maximum, in seconds, of each pulse.
CONTEXT: Python project pulselab (NumPy/SciPy). x is a 1-D float signal in volts with baseline around 0. fs is the sampling frequency in Hz. idx holds the sample indices of the pulse maxima, as returned by pulselab.peaks.find_pulses. Output: a NumPy array of widths in seconds, one per pulse.
CONSTRAINTS: NumPy only. Do not modify x. The half-maximum level of a pulse is x[idx]/2. Locate both crossings with linear interpolation (sub-sample). If a crossing is not reached before the record ends, return NaN for that pulse. An empty idx returns an empty array.
EXAMPLES: For a Gaussian pulse of standard deviation sigma the FWHM is 2*sqrt(2*ln(2))*sigma = 2.3548*sigma. The width does not depend on the amplitude.
FORMAT: First the function with a docstring that states the units, then pytest tests, then a short list of your assumptions.
VERIFICATION: List your assumptions and ask me questions before writing code. The tests must cover: Gaussian FWHM within 3 %, amplitude independence, two pulses, a pulse cut by the end of the record, empty idx.
```

### 1.2.4 Assumptions and questions: reuse your step 1.2.3 prompt and end it with

**Mode:** CHAT

```text
List your assumptions and ask me questions before writing any code.
```

*Reference prompt written for this handout (the lab sheet only gives a scaffold for this step).*

---

## Lab 1.3: Explore an unknown codebase with AI

*Module 4 · Complete AI-driven workflow (1/6)*

### 1.3.1 Ask for a map, with evidence

**Mode:** CHAT, AGENT

```text
Explain this Python project to me. It analyses noisy pulse-train measurements from CSV files in data/.
Constraints: read-only, do not modify any file. Cite the file path and function name for every claim.
Give me (1) the entry points, (2) one line per module in pulselab/, (3) the data flow from a CSV file to the summary table as numbered steps, (4) where module-level (global) state lives. Say what you are unsure about instead of guessing.
```

### 1.3.2 Ask for smells and risks (scientific code)

**Mode:** CHAT, AGENT

```text
List the risks for scientific correctness in this code base: hidden global state, arrays modified in place, magic numbers, silent data dropping, broad exception handling, unit ambiguities. For each risk give file:line and one sentence. Do not modify files.
```

---

## Lab 1.4: Implement a multi-file feature: band power

*Module 4 · Complete AI-driven workflow (2/6)*

### 1.4.2 Ask for a PLAN, then correct it

**Mode:** PLAN

```text
Add a band power feature to this project. Plan only: do not write or edit any code yet.
Feature: `python scripts/run_analysis.py --band FMIN FMAX` adds a column band_power_V2 (last column) to summary.csv with the signal power in V^2 between FMIN and FMAX Hz. Without --band the CSV must be unchanged.
Acceptance: (AC1) spectrum.band_power(x, fs, fmin, fmax, nperseg=1024) = Welch PSD (scipy.signal.welch) integrated over [fmin, fmax], bounds inclusive. (AC2) sine of amplitude A inside the band -> A^2/2 within 5 %, outside ~0, DC ignored. (AC3) fmin >= fmax raises ValueError. (AC4) CLI option and CSV column as described. (AC5) README updated, tests added, existing tests pass.
Give numbered steps with the files touched and how to verify each step. List risks and questions.
```

### 1.4.3 Implement in three small steps, one commit each

**Mode:** EDIT, AGENT

```text
Implement step A of the plan only: add band_power(x, fs, fmin, fmax, nperseg=1024) to pulselab/spectrum.py and tests in tests/test_band_power.py.
Constraints: use scipy.signal.welch, integrate the PSD over the band (bounds inclusive) by summing psd*df; raise ValueError if fmin >= fmax; do not modify x; do not touch any other file.
Tests: sine A=2 at 50 Hz, fs=1000 Hz, 10 s: band (40, 60) gives 2.0 within 5 %; band (100, 200) gives < 1e-3; a DC offset does not change the result; band (0, fs/2) is close to the variance; invalid band raises ValueError.
Run the tests and show the result.
```

---

## Lab 1.5: Fix a complex bug: reproduce, hypothesize, verify, fix

*Module 4 · Complete AI-driven workflow (3/6)*

### 1.5.2 Ask for ranked hypotheses, not a fix

**Mode:** CHAT, PLAN

```text
Root-cause analysis, do NOT propose a fix yet.
Symptom: analyze_run(run)["offset_V"] is ~0 for every run, although the raw data has offsets (run03 ~ +0.6 V). My failing test compares the reported offset with the mean of a copy of run["signal"] taken before the call.
Relevant files: pulselab/report.py (analyze_run), pulselab/preprocess.py.
Give me ranked hypotheses in a table: hypothesis | why plausible | one experiment (a few lines of Python) that would confirm or reject it. Say what evidence would change your ranking.
```

### 1.5.4 Fix at the root, minimally

**Mode:** EDIT

```text
Fix the root cause: estimate_noise (via remove_offset) modifies its input array in place, which corrupts run["signal"] used later by analyze_run.
Constraints: smallest change; remove_offset must return a new array and never modify its argument; analyze_run must compute the offset from the RAW signal; do not change the results for n_pulses and tau_s; do not edit existing tests.
Then add regression tests: remove_offset returns a copy; estimate_noise leaves its input untouched; analyze_run does not modify the run; the reported offset of run03 equals the raw mean (about 0.6 V).
Run the tests and list other functions with the same hazard.
```

---

## Lab 1.6: Generate and improve tests (and check that they can fail)

*Module 4 · Complete AI-driven workflow (4/6)*

### 1.6.2 Ask for a TEST PLAN before any test

**Mode:** PLAN, CHAT

```text
Propose a test plan (no test code yet) for pulselab/fit.py, stats.py, io.py and report.py.
Known facts: run04 has ~1 % missing samples that load_run drops; run06 has no pulse above the threshold so tau_s must be NaN; fs is estimated from the median time step; offsets of the six runs are 0.30, -0.20, 0.60, 0.10, 0.45, 0.15 V (within 0.06 V).
Give a table: behaviour | test name | which single-line change in the source this test should catch. Prefer analytical checks (a noiseless exponential must give back its tau). Seeded random data only. Do not re-implement the code under test in the test.
```

### 1.6.3 Generate the tests from the approved plan

**Mode:** EDIT, AGENT

```text
Implement the test plan we approved, and only the tests: create or extend tests/test_fit.py, tests/test_stats.py, tests/test_io.py and tests/test_report.py.
Constraints: do not modify any file outside tests/. Each test asserts a real value (no `is not None`, no `> 0` alone). Use pytest.approx with a tolerance you can justify in a comment. Seeded random data only (numpy default_rng). Never recompute the expected value with the code under test.
For each test, add a one-line docstring saying which single-line change in the source it should catch.
Run python -m pytest -q, show the result, then list the tests you are least sure about.
```

*Reference prompt written for this handout (the lab sheet only gives a scaffold for this step).*

---

## Lab 1.7: Guided refactoring: speed up `find_pulses` without changing its results

*Module 4 · Complete AI-driven workflow (5/6)*

### 1.7.2 Characterization tests before the refactoring

**Mode:** EDIT, AGENT

```text
GOAL: write characterization tests for find_pulses before I refactor it.
CONTEXT: pulselab/peaks.py, data/*.csv. In pulselab/report.py the signal is offset-removed, then smoothed with a moving average of max(3, int(0.008 * fs)) samples before find_pulses is called. Detection uses config.CONFIG["threshold_v"] and config.CONFIG["min_gap_s"].
CONSTRAINTS: do not modify pulselab/. The golden file is generated by the CURRENT code. Keep a copy of the current loop implementation inside the test file as an oracle.
EXAMPLES: random signals with plateaus (tie cases), min_gap_s in {0, 0.01, 0.05}, signals of length 0 to 3.
FORMAT: tests/test_characterization.py, plus a small script that writes tests/golden/pulses.json.
VERIFICATION: all tests pass on the current, unchanged implementation. Show the output.
```

*Reference prompt written for this handout (the lab sheet only gives a scaffold for this step).*

### 1.7.3 Refactor to remove the per-sample loop

**Mode:** EDIT

```text
Refactor find_pulses in pulselab/peaks.py to remove the per-sample Python loop.
Invariant: identical output for every input (same indices, same dtype). Rules to preserve: a pulse is a local maximum with x[i] > threshold, x[i] > x[i-1] and x[i] >= x[i+1]; among candidates closer than min_gap the FIRST one wins (greedy from the left); gap = int(min_gap_s * fs).
Constraints: NumPy only, no scipy.signal.find_peaks, no change outside peaks.py, do not edit tests.
Run tests/test_characterization.py and the benchmark, and show the results.
```

---

## Lab 1.8: Compare an IDE assistant and an autonomous agent on the same task

*Module 3 · Overview of AI tools*

### 1.8.1 Do the task with EDIT, then with AGENT; time yourself

**Mode:** EDIT, AGENT

```text
Add a --min-gap SECONDS option (float) to scripts/run_analysis.py. It overrides config min_gap_s; the default behaviour is unchanged. A negative value must stop the program with a clear error message (argparse error). Update the README and add a test in tests/ that runs the script through subprocess for one valid and one invalid value. Run the tests.
```

---

## Also in this repository

- `templates/`: reusable versions of these prompts (`templates/prompts/`), instruction files, permissions, agents, skills and commands for Kilo Code.
- `AI_Coding_Best_Practices.md`: the memo of good practices.
