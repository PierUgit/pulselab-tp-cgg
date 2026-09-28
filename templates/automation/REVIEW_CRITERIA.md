# Review criteria for AI-assisted pull request review

The AI reviewer is an ADVISORY first pass. A human reviewer decides.

## Check
- Numerical correctness: units, dB conventions (amplitude vs power), off-by-one, dtype, NaN handling.
- Input arrays modified in place (aliasing) when the caller does not expect it.
- Behaviour changes not covered by a test; a test that cannot fail.
- Hard-coded constants that should be parameters.
- New dependency: does it exist, is it maintained, is it needed?

## Ignore
- Formatting and import order (handled by the linter).
- Naming preferences that are not in the conventions file.

## Output format
List at most 7 comments. For each: file:line, severity (high / medium / low),
what is wrong, a suggested fix in one sentence. Say "no issue found" if there is none.
Do not rewrite the whole file.
