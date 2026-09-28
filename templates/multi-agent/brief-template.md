# Brief template for a subagent or a separate session

A subagent or a fresh session knows **only what you write here**. Make it self-contained.

```
BRIEF <name>
Goal:            {{one_sentence}}
Context:         {{what it needs to know: project, conventions, units, the files it should read}}
Allowed files:   {{the ONLY files it may create or edit}}
Out of scope:    {{everything else, explicitly (report.py, the CLI, tests/golden/...)}}
Deliverable:     {{signature / file / behaviour}}
Check:           {{command it must run}}. Report back in at most 10 lines: what was done, files touched, test result.
```

## Rules for the set of briefs

- The "allowed files" of two briefs never overlap.
- Anything that touches several pieces (wiring, CLI options, golden files) is **your** integration step, not a subagent's.
- Each brief says how the result will be checked.
- After integration: run all the tests, read the diffs file by file, and ask a fresh-context reviewer for a second opinion.

## Example (from lab 2.3)

```
BRIEF B
Goal:            add fit_decay_with_error(t_peaks, heights) -> (tau, tau_err) to pulselab/fit.py.
Context:         fit_decay currently returns only tau (scipy.optimize.curve_fit of a*exp(-t/tau)). tau_err is the 1-sigma uncertainty sqrt(pcov[1,1]). Return (nan, nan) when the fit is impossible. fit_decay keeps its behaviour. Times are in seconds.
Allowed files:   pulselab/fit.py and tests/test_fit_uncertainty.py (new).
Out of scope:    everything else, especially report.py and the golden files.
Deliverable:     the function and its tests: true tau within 3 sigma on noisy data; larger noise gives a larger tau_err; empty input gives (nan, nan); fit_decay equals the first value.
Check:           python -m pytest -q. Report back in at most 10 lines.
```
