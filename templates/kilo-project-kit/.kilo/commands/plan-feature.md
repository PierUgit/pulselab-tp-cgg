---
description: Plan a change across files, without editing code
agent: plan
---

Plan only: do not write or edit any code.
I will describe the feature and its acceptance criteria in my message. If either is missing, ask me first.

Give numbered steps in a safe order (helpers first, plumbing next, command line last). For each step: the files touched and how to check it (a command or a test).
Constraints: smallest change that meets the criteria, no new dependency, existing outputs and tests/golden files must not change.
End with the risks and the questions you need answered before implementing.
