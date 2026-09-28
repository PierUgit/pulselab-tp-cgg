# <project name>: guide for humans and AI assistants

<!-- Kilo Code loads AGENTS.md from the project root (the file name is uppercase). Keep it short: about 40 lines.
     Only write what the assistant cannot guess from the code. Replace every <placeholder>. -->

## What this project is
<One or two sentences: what it computes, on which data.>

## Commands
- Install: `<pip install -r requirements.txt>`
- Tests: `<python -m pytest -q>`
- Run: `<python scripts/run_analysis.py --data data --out summary.csv>`

## Layout
- `<package>/`: <one line per module>
- `scripts/`: command-line entry points. `tests/`: pytest. `tests/golden/`: reference outputs.

## Scientific conventions
- Units are in names and docstrings: `_V`, `_s`, `_hz`, `_db`.
- <State your dB convention: amplitude ratio 20*log10, power ratio 10*log10.>
- Never modify an input array in place: return a new array.
- Prefer vectorized NumPy/SciPy; a Python loop over samples needs a comment that explains why.
- Docstrings state the units and the behaviour for NaN and empty input.

## Rules for changes
- A change of numerical behaviour needs a test AND a sentence in the pull request description.
- `tests/golden/*` changes only on purpose, with the reason in the commit message.
- Do not weaken or delete an existing test to make it pass.
- No new dependency without asking. No absolute paths. No secrets. No large data files.

## When unsure
Ask instead of guessing a scientific convention (dB, windowing, detrending, units).
