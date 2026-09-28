# Prompt template: bug fix (v1.0)

Use with: a coding agent (Kilo Code: *Code*), after the cause is known.

## Goal
Fix: {{bug_summary}}. Root cause (found and confirmed by me): {{root_cause}}.

## Context
- Stack: {{language_and_version}}, {{framework}}. Files involved: {{files}}.
- Reproduction (command or test): {{repro}}. Expected vs actual: {{expected}} / {{actual}}.

## Constraints
- Write a failing test that reproduces the bug BEFORE changing the code.
- Smallest possible change, at the cause (not a workaround in the caller). Do not touch unrelated files. Do not edit existing tests.
- If the cause is a scientific convention (units, dB, windowing), ask me instead of choosing.

## Format
1. The failing test. 2. The fix as a diff. 3. Two sentences on why it removes the cause.

## Verification
- Show the output of the test suite. List other functions that could have the same problem, and check them with a search.
