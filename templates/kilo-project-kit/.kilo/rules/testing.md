# Testing rules

- Every test asserts a real value. No `assert x is not None`, no `assert x > 0` alone.
- Never compute the expected value with the code under test.
- Use `pytest.approx` with a tolerance you can justify in a comment.
- Prefer analytical checks (a known exponential must give back its time constant, a Gaussian of width sigma has FWHM 2*sqrt(2*ln 2)*sigma).
- Before a refactoring, write characterization tests (golden file + an oracle copy of the current code).
- Never edit `tests/golden/*` or weaken an assertion to make a test pass. If a golden value must change, stop and ask.
- After writing tests, break the code on purpose (change one operator or constant): at least one test must fail.
