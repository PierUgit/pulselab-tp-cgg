---
name: characterization-tests
description: How to lock the current behaviour of a function before refactoring it, with a golden file computed by the current code and an oracle copy of the current implementation. Use it before optimizing, vectorizing or restructuring numerical code.
---

# Characterization tests before a refactoring

1. **Do not touch the source yet.** Read the function and list its callers and the preprocessing that happens before it is called.
2. **Golden file.** Run the CURRENT code on the real data files and store the outputs (for example the detected indices) in `tests/golden/<name>.json`. A test compares the code with this file.
3. **Oracle.** Copy the current implementation into the test file (`legacy_<name>`). A second test compares the new code with the oracle on many generated inputs.
4. **Generated inputs.** Random data with ties and plateaus, several values of every parameter including 0, and inputs of length 0 to 3.
5. **They must pass on the unchanged code.** If not, the tests are wrong: fix them before refactoring.
6. **Refactor in small steps**, running these tests after each one. A library function that looks equivalent (for example `scipy.signal.find_peaks`) is only accepted if these tests pass.
7. Commit the tests and the golden file **before** the refactoring commit.
