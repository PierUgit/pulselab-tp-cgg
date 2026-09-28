# Prompt template: property-based tests (v1.0)

Use with: a coding agent (Kilo Code: *Code*). Requires `hypothesis`; otherwise ask for seeded random loops.

## Goal
Write property-based tests for `{{function}}` in `tests/test_properties.py`.

## Context
- Module: `{{module}}`. Domain of valid inputs: {{valid_inputs_with_ranges}}.

## Constraints
- `hypothesis` with `max_examples=50` and `deadline=None`; skip the module with `pytest.importorskip("hypothesis")` if it is not installed.
- Finite floats in a bounded range (no NaN, no infinity) unless the property is about them. Tolerances relative to the data scale.
- Tests only: do not modify the source.

## Examples of properties
- The input is not modified. Applying the function twice gives the same result (idempotence).
- A known analytical result holds for any parameter in the valid range.
- Invariance: {{invariance_e_g_adding_an_offset_does_not_change_it}}.
- Output ordering, spacing or bounds hold for any input.

## Format
One test per property, with a docstring that states the property in one sentence.

## Verification
Run `python -m pytest -q` and show the result. Tell me the input ranges each test explores.
