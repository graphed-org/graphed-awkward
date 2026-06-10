# M17 attempts — graphed-awkward (structure-op parity, dask-awkward parity plan P1)

## Iteration 0 — TEST_AUTHORING/TEST_SANITY — 2026-06-10 (freeze-M17-0)

- frozen suite tests/frozen/m17 (12 tests over ~24 functions); NON-VACUOUS (12/12 fail
  pre-implementation).

## Iteration 0 — IMPLEMENTING/REVIEW — 2026-06-10

- gak grows: sort/ravel/run_lengths, mask/is_none/singletons/pad_none(clip), unflatten,
  to_regular/from_regular, full_like/nan_to_num/isclose, argcombinations/argcartesian,
  without_field, values_astype wrapper, multi-output broadcast_arrays (one node per output via
  an index param) and unzip (per-field ops from the typetracer form), EAGER to_list sugar.
  Backend "fields" op evaluates the M17 record-subset getitem. All through the single apply
  dispatch (typetracer + real, one source of truth); all partition-local fusible ops.
- gates: frozen_tests 211/211 PASS · coverage 97% (>=90, branch) · ruff+format clean ·
  mypy --strict clean · determinism green · sphinx -W clean.
