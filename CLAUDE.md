# CLAUDE.md — graphed-awkward

Defers to the root **`graphed-project/CLAUDE.md`**; the **project plan
(`graphed-project-plan-gated.md`) always wins.** This file distills **milestone M3**.

## What this repo is

`graphed-awkward`: the **reference backend**. `op_form` uses the awkward **typetracer** (metadata
only — no event data is read); `eval_stage` uses real awkward. Both go through one `apply` dispatch
(`_ops.py`), so forms and results share a single source of truth. The `gak` namespace mirrors the
awkward API so corpus analyses record a backend-agnostic graph.

> Guardrails (M3): reuse awkward typetracer (don't reimplement type inference) · reuse
> correctionlib/ONNX (invent no formats) · **no optimization** (M4) · column projection is M5.

## M3 — implemented

- `AwkwardForm` (wraps a typetracer array; `is_typetracer` proves metadata-only) + `AwkwardBackend`.
- `_ops.apply`: the op dispatch — elementwise (arith/comparison/bool/ufuncs incl. `%`), field access,
  indexing, and the `ak.*` functions (combinations, cartesian, zip, num, sum, any/all, argmin/argmax,
  argsort, firsts, local_index, concatenate, flatten, fill_none, drop_none, where, with_field,
  zeros/ones_like, values_astype).
- `gak` namespace (`functions.py`) recording those ops.
- `from_awkward` / `from_parquet` (metadata-only) sources.
- `payloads`: **content-hashed** correctionlib (JSON), ONNX (model + opset + I/O), dataset descriptors.
- Real provenance lives in `graphed` (M3); this repo exercises it through realistic analyses.

## Validated against the corpus

**ADL queries 1–8 + the AGC object-selection slice** (`graphed_corpus`) all record metadata-only with
correct typetracer forms; `materialize` matches plain awkward; every node maps to a user-code line;
correctionlib + ONNX inputs record `External` nodes whose descriptors content-hash the file.

## Layout / gates

```
src/graphed_awkward/_ops.py       the apply dispatch (typetracer + real)
src/graphed_awkward/backend.py    AwkwardForm, AwkwardBackend, from_awkward/from_parquet
src/graphed_awkward/functions.py  the gak namespace
src/graphed_awkward/payloads.py   correctionlib / ONNX / dataset descriptors
tests/frozen/m3/                  recording, eval, provenance, payloads, metadata-only, dispatch
```

`ruff` + `ruff format` · `mypy --strict` (awkward/numpy untyped at the boundary) ·
`pytest tests/frozen/m3 --cov=graphed_awkward` (≥90%) · `sphinx -W`. Depends on `graphed` +
`graphed-core` + `awkward` + `numpy`; tests also use `graphed-corpus`, `correctionlib`, `onnx`,
`pyarrow`, `pandas`. Status: see `.graphed/state.json`.
