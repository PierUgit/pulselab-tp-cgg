---
description: Advisory review of my uncommitted changes (comments only)
agent: ask
---

Review my uncommitted changes (use git diff) as a first, advisory reviewer. Do not edit anything.
Check: numerical correctness (units, dB convention, NaN and empty input), arrays modified in place, missing or weak tests, absolute paths, new dependencies, changes outside the scope.
At most 7 comments, most severe first: file:line, severity, problem, one-sentence fix. Say "no issue found" if there is none, and say what you could not judge.
