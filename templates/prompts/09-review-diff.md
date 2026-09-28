# Prompt template: review a diff (v1.0)

Use with: a read-only agent in a **fresh session** (Kilo Code: *Ask* with `@git-changes`, or the `reviewer` subagent of the project kit).

## Goal
Review the changes below as a first, ADVISORY reviewer. A human decides.

## Context
- Project conventions: the project instruction file. Review criteria: `{{path_to_REVIEW_CRITERIA.md}}`.
- The changes: {{the_diff_or_@git-changes}}

## Constraints
- At most 7 comments, most severe first. Do not rewrite the code. Do not comment on formatting.

## Format
For each comment: file:line, severity (high / medium / low), the problem, a one-sentence fix. Or "no issue found".

## Verification
Say which parts of the change you could NOT judge (missing context), so that I review them myself.
