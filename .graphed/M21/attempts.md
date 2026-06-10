# M21 attempts — graphed-awkward (partitioned-source writer dispatch, P3.6 revision)

## Iteration 0 — TEST_AUTHORING/TEST_SANITY — 2026-06-10 (freeze-M21-0)

- frozen suite tests/frozen/m21 (4 tests); NON-VACUOUS (4/4 fail pre-implementation). One
  authoring fix pre-freeze (recorded): a single test compiled TWO different expressions from one
  session and hit the compile_ir output-accumulation footgun (graph outputs accumulate per
  session -> multi-output IR); re-pinned with one session per compiled expression and the
  footgun recorded for future work.

## Iteration 0 — IMPLEMENTING/REVIEW — 2026-06-10

- to_parquet dispatches on graphed.write.PartitionedSource: any source describing its own
  partitioning is written partition-wise — its whole-dataset loader is NEVER invoked (witnessed
  by counters); _DatasetLoader rides the same protocol (one code path for parquet and reader
  integrations alike). behavior= kwarg (dict or importable "module:attr" — behavior dicts hold
  lambdas and do not pickle to process workers).
- The read list became the MERGED logic: the graph's SYNTACTIC source-field accesses (evaluation
  replays every node, a zip's untouched legs included — the UPROOT-2 finding) refined per field
  by the buffer projection (DATA leaves; one carrier leaf for offsets-only). For the frozen m15
  pins this reduces to the same sets — m15 stayed green UNCHANGED.
- gates: frozen_tests 225/225 PASS · coverage 95% (>=90, branch) · ruff+format clean ·
  mypy --strict clean · determinism green · sphinx -W clean.
