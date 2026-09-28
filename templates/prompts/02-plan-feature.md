# Prompt template: plan a feature (v1.0)

Use with: a planning agent (Kilo Code: *Plan*). Plan only, no code.

## Goal
{{feature_in_one_sentence}}

## Context
- Project: {{project}}. Files I think are involved: {{files}}.
- Acceptance criteria:
  - AC1 {{criterion_1}}
  - AC2 {{criterion_2}}
  - AC3 {{criterion_3}}

## Constraints
- Plan only: do not write or edit code yet.
- Smallest change that satisfies the criteria. No new dependency. Existing outputs must not change: {{what_must_not_change}}.

## Format
Numbered steps in a safe order (types and helpers first, plumbing next, command line last). For each step: the files touched and how to check it (a command or a test).

## Verification
List the risks and the questions you need answered before implementing.
