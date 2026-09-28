---
name: verify-numerical-change
description: Checklist to verify a change to numerical or scientific Python code before calling it done (units, dB convention, in-place modification, NaN and empty inputs, tolerances, golden files, breaking the code on purpose). Use it after any change that can alter a computed number.
---

# Verify a numerical change

1. **State the invariant.** What must not change (outputs, command line, golden files)? What may change, and why?
2. **Inputs are not modified.** Search the diff for in-place operators (`-=`, `+=`, `/=`, `[:] =`) applied to arguments. Functions return new arrays.
3. **Units and conventions.** Units in names and docstrings. dB: amplitude ratio `20*log10`, power ratio `10*log10`. If a convention is ambiguous, ask instead of choosing.
4. **Edge cases.** Empty input, one sample, NaN, all zeros, constant signal: the behaviour is defined and tested.
5. **Tolerances.** Every `approx` tolerance can be justified (noise level, discretization). No loose tolerance chosen to make a test pass.
6. **A physical check exists.** An analytical value, a conservation law or a known limit is tested.
7. **Tests can fail.** Change one operator or constant on purpose: at least one test must fail. Restore it.
8. **Golden files** change only on purpose, in a separate commit whose message says why.
9. **Report.** List what was checked, what was not, and every assumption.
