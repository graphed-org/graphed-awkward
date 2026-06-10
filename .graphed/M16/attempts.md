# M16 attempts — graphed-awkward (P0 foundation, dask-awkward parity plan)

## Iteration 0 — TEST_AUTHORING/TEST_SANITY — 2026-06-10 (freeze-M16-0)

- frozen suite authored (~120 tests); NON-VACUOUS (102 fail pre-implementation; the passing
  remainder is the already-implemented ufunc overlap). One harness defect fixed pre-freeze
  (recorded): pytest.approx cannot compare nested jagged lists — comparisons re-pinned on
  ak.array_equal(equal_nan=True), which is also the project's exactness ethos.

## Iteration 0/1 — IMPLEMENTING/REVIEW — 2026-06-10

- _ops.py: _UNARY/_BINARY completed to the full M11 canonical tier (~42 unary, ~36 binary;
  awkward takes numpy ufuncs; the typetracer infers every form); generic _AK_REDUCERS +
  std/var(ddof)/moment/softmax + two-input corr/covar/linear_fit dispatch.
- functions.py: the M12/M16 STRUCTURAL RULE on every reducer (axis None/0 -> boundary reduction
  node; inner axes -> fusible op; both directions frozen-witnessed per kind); gak.count joined
  the rule (its M3 form choked on axis=None); 13 new reducers; softmax always partition-local.
- Typetracer findings (documented shims, awkward's own inference throughout): min/max(axis=None)
  yield MaybeNone option-scalars that cannot be formed — the TRACING path uses awkward's
  mask_identity=False (real evaluation untouched); upstream ak.ptp(axis=None) indexes its scalar
  result (typetracer-incompatible) — the tracing path composes max - min (identical kernels).
- gates: frozen_tests 199/199 PASS · coverage 97% (>=90, branch) · ruff+format clean ·
  mypy --strict clean · determinism green · sphinx -W clean.
