---
name: scientific-code-review
description: Criteria for reviewing a diff of scientific Python code (numerical correctness, in-place modification, units, dB convention, missing or weak tests, paths, dependencies, scope). Use it when asked to review changes, a pull request or generated code.
---

# Scientific code review

Review in this order and report at most 7 comments, most severe first (file:line, severity, problem, one-sentence fix).

1. **Numerical correctness**: units, dB convention, off-by-one, dtype, NaN and empty-input handling, division by zero.
2. **Aliasing**: an argument modified in place (`-=`, `/=`, slice assignment) that the caller does not expect.
3. **Tests**: behaviour changes with no test; tests that could not fail; expected values recomputed with the code under test; loose tolerances.
4. **Hygiene**: hard-coded constants, absolute paths, secrets, new or unused dependencies, large files.
5. **Scope**: files changed that the task did not require; golden files changed without a stated reason.

Do not comment on formatting. Say "no issue found" when there is none, and say which parts you could not judge.
