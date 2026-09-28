# Scientific conventions

- Name quantities with their unit: `offset_V`, `duration_s`, `fs_hz`, `snr_db`.
- A dB value of an amplitude ratio is `20*log10(ratio)`; of a power ratio it is `10*log10(ratio)`. If the convention of a quantity is not stated, ask.
- Functions never modify their arguments in place. Use `x - x.mean()`, not `x -= x.mean()`, unless the function is explicitly documented as in-place.
- Random data uses a seeded generator: `numpy.random.default_rng(seed)`.
- Behaviour for empty input and for NaN is defined in the docstring and covered by a test.
- Do not replace a loop by a library call (for example `scipy.signal.find_peaks`) without a test proving the results are identical.
- Check that a NumPy or SciPy function exists in the installed version before using it (some old names no longer exist).
