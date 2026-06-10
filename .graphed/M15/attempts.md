# M15 attempts — graphed-awkward (partitioned parquet I/O, dask-awkward parity plan M15.2)

## Iteration 0 — TEST_AUTHORING/TEST_SANITY — 2026-06-10 (freeze-M15-0)

- frozen suite authored (tests/frozen/m15: 11 tests); NON-VACUOUS (collection fails on the
  missing graphed_awkward.io). One authoring slip fixed pre-freeze (recorded): a call omitted
  the required `name` argument.

## Iteration 0/1 — IMPLEMENTING/REVIEW — 2026-06-10

- io.py specializes graphed.parquet: from_parquet (multi-file/dir/glob/list; form from the ARROW
  SCHEMA alone via ak.from_arrow_schema — witnessed with a garbage second file; columns= filter
  via Form.select_columns; blind mode opens nothing), read_parquet_partition (blind resolution +
  column projection), to_parquet (compiled-IR evaluation per partition — R7.8; compute-disabled
  task graph == compute-enabled run bit-for-bit — R15.4; part index derived from the partition —
  R15.9; multi-source rejected; in-memory sources write by steps). M3's single-file from_parquet
  is the path's special case; backend.py's old implementation removed.
- Iteration 1 (the real finding): the read list was first wired from the COLUMN projection and
  `gak.num(jets) + x` failed — num needs Jet's OFFSETS, which the column view cannot express
  (the A.3 under-specification M10 fixed for ROOT). The read list is now wired from the BUFFER
  projection: DATA needs read their column; an OFFSETS need reads its cheapest carrier leaf
  (parquet has no standalone counter column) — the parquet analogue of R15.8.
- gates: frozen_tests 85/85 PASS · coverage 97% (>=90, branch) · ruff+format clean ·
  mypy --strict clean · determinism green · sphinx -W clean.
