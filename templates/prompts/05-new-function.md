# Prompt template: new numerical function (v1.0)

Use with: a coding agent (Kilo Code: *Code*).

## Goal
Add `{{function_name}}({{signature}})` in `{{module}}`. It {{purpose}}.

## Context
- Inputs: {{inputs_with_units}}. Output: {{output_with_units}}.
- An existing function to imitate for style: {{example_function}}.

## Constraints
- {{allowed_libraries}} only, no new dependency. Vectorized unless a loop is justified in a comment.
- Do not modify the inputs in place. Define the behaviour for empty input and for NaN: {{nan_and_empty_policy}}.

## Examples
- Analytical check: {{analytical_check}}
- Example: {{example_input}} gives {{example_output}}.

## Format
The function with a docstring that states the units, then the tests, then a short list of your assumptions.

## Verification
- List your assumptions and any question you have BEFORE writing code.
- The tests must include the analytical check and one edge case. Run them and show the result.
