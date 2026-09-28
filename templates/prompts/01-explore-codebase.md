# Prompt template: explore a codebase (v1.0)

Use with: a read-only agent (Kilo Code: *Ask*).

## Goal
Explain this project to me: {{what_it_does}}.

## Context
- Folders: {{folders}}. Entry points I already know: {{entry_points}}.
- Read the project instruction file first if there is one.

## Constraints
- Read-only: do not modify any file and do not run the code.
- Cite the file path and function name for every claim. Say what you are unsure about instead of guessing.

## Format
1. The entry points.
2. One line per module.
3. The data flow from {{input}} to {{output}}, as numbered steps.
4. Where global or module-level state lives.

## Verification
End with the three statements you are least sure about, so that I can check them by opening the files.
