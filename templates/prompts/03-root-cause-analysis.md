# Prompt template: root-cause analysis (v1.0)

Use with: a read-only agent (Kilo Code: *Ask*). **Do not ask for a fix yet.**

## Goal
Find the root cause of: {{symptom}}. Do not propose a fix yet.

## Context
- Expected: {{expected}}. Observed: {{observed}}.
- Failing test or reproduction: {{repro}}. Output: {{paste_the_exact_output}}
- Relevant files: {{files}}.

## Constraints
- No code change. Rank the hypotheses from most to least likely.

## Format
A table: hypothesis | why it is plausible | one experiment (a few lines of Python) that would confirm or reject it.

## Verification
Say what evidence would change your ranking.
