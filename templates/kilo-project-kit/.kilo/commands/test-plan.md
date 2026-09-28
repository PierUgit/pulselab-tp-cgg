---
description: Propose a test plan (no test code yet)
agent: plan
---

Propose a test plan for the code I name in my message. Do not write test code yet and do not change the source.

Give a table: behaviour | test name | the single-line change in the source that this test should catch.
Prefer analytical checks. Cover edge cases (empty input, NaN, one sample) and error paths. Seeded random data only. No test may recompute its expected value with the code under test.
Wait for my approval before anything else.
