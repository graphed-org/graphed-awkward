# M19 attempts — graphed-awkward (conveniences, dask-awkward parity plan P3.8 — the last MVP tier)

## Iteration 0 — TEST_AUTHORING/TEST_SANITY — 2026-06-10 (freeze-M19-0)

- frozen suite tests/frozen/m19 (4 tests); NON-VACUOUS (4/4 fail pre-implementation).

## Iteration 0/1 — IMPLEMENTING/REVIEW — 2026-06-10

- gak.fields/type_of/backend_of: pure form/session metadata, recording NOTHING (node-count
  witnessed); gak.head/sample: eager peeking sugar over the common slice op + reference
  materialize (partitioned head is executor territory, not MVP).
- Gap found and closed: the awkward backend never implemented the M13 COMMON slice/index ops
  (numpy got them in M13); added to the apply dispatch — head/sample and a[start:stop] now
  evaluate on awkward sources.
- gates: frozen_tests 221/221 PASS · coverage 97% (>=90, branch) · ruff+format clean ·
  mypy --strict clean · determinism green · sphinx -W clean.
