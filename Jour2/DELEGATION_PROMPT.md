# Delegation prompt for the orchestrator (Code agent)

Ready to paste into a Kilo Code **Code** session opened at the root of `lab2.3_multi_agent/`
(with the `.kilo/kilo.jsonc` and `.kilo/agents/` from this folder in place). Concrete version
of `pulselab-tp-participants/templates/multi-agent/delegation-prompt.md` for this lab.

```
Task: land the three independent pieces of TP 2.3 on pulselab, each on its own branch, then integrate.

You may use subagents, but only for pieces that are independent:
- `explorer-a` (read-only) to write docs/ARCHITECTURE.md: see briefs/BRIEF_A.md for the full brief;
- `fit-b` to add fit_decay_with_error to pulselab/fit.py: see briefs/BRIEF_B.md;
- `export-c` to add pulselab/export.py: see briefs/BRIEF_C.md.

Rules:
- Before calling a subagent, switch to its branch (docs-arch / feat-fit-uncertainty / feat-json-export)
  so its one commit only touches its allowed files.
- Give each subagent its brief verbatim (paste the content of the matching briefs/BRIEF_*.md file).
- Never let two subagents edit the same file.
- Do the integration yourself once all three report back: merge the three branches into `multi-agent`,
  wire fit_decay_with_error into report.py (new tau_err_s column) and write_json into
  scripts/run_analysis.py (--format {csv,json}), update tests/golden/summary_expected.csv on
  purpose with the reason in the commit message, run python -m pytest -q, and show me the diff
  before anything is committed.
```

You can also call a subagent directly, one session at a time, without a Code orchestrator:
`@fit-b` followed by the content of `briefs/BRIEF_B.md`, then repeat for `@export-c` and `@explorer-a`
on their own branches — see `MULTI_AGENT_KILO.md` for both ways to run this.
