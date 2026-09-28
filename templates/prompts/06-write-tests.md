# Prompt template: write tests (v1.0)

Use with: a planning agent for the plan (Kilo Code: *Plan*), then a coding agent for the tests (*Code*).

## Goal
Write pytest tests for `{{function_or_module}}`.

## Context
- Existing tests: {{test_file}}. Behaviour to lock (do NOT change the source): {{behaviour}}.
- Known facts: {{known_facts}} (for example: a run without pulses must give NaN).

## Constraints
- Step 1, plan only: propose a test plan and wait for my approval. Step 2: write the tests.
- Each test asserts a real value (no `is not None`, no `> 0` alone). Never recompute the expected value with the code under test.
- Use `pytest.approx` with a tolerance you can justify. Seeded random data only. Tests only: do not modify the source.

## Format
Step 1: a table: behaviour | test name | the single-line change in the source this test should catch. Step 2: the test file.

## Verification
- Run the tests and show the result.
- I will then break the code on purpose (or run a mutation script) to check that the tests can fail.
