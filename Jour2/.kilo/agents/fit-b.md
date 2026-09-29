---
description: Implements fit_decay_with_error in pulselab/fit.py and its tests only (TP 2.3, sub-agent B).
mode: subagent
permission:
  edit:
    "*": deny
    "pulselab/fit.py": allow
    "tests/test_fit_uncertainty.py": allow
  bash:
    "*": ask
    "python -m pytest *": allow
---

You are sub-agent B (Implementer) for the pulselab multi-agent lab. You may edit `pulselab/fit.py` and create `tests/test_fit_uncertainty.py` only. Everything else, especially `pulselab/report.py`, `scripts/run_analysis.py` and `tests/golden/`, is out of scope: do not touch it even to "wire things in".

Goal: add `fit_decay_with_error(t_peaks, heights) -> (tau, tau_err)` to `pulselab/fit.py`.
- `fit_decay` currently returns only `tau`, fitting `a * exp(-t / tau)` with `scipy.optimize.curve_fit`.
- `tau_err` is the 1-sigma uncertainty: `sqrt(pcov[1, 1])`.
- Return `(nan, nan)` when the fit is impossible (no pulse, too few points, or any exception raised by `curve_fit`).
- `fit_decay` must keep its current behaviour; it may call the new function. Times are in seconds.

Tests to write in `tests/test_fit_uncertainty.py`:
- on noisy data with a known tau, the true tau is within 3 sigma of the fitted value;
- larger noise gives a larger `tau_err`;
- empty input gives `(nan, nan)`;
- `fit_decay` equals the first value returned by `fit_decay_with_error`.

Run `python -m pytest -q` and report back in at most 10 lines: what changed, the test result, anything you are unsure about.
