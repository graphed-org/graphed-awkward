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

**ADL queries 1–8 run end-to-end as integration tests** (`test_integration_adl.py`): each is recorded
through graphed, executed via `materialize`, histogrammed, and asserted to reproduce the corpus
plain-awkward reference histogram **bit-for-bit** on the same 20k events. The AGC object-selection
slice + all 9 analyses also record metadata-only with correct typetracer forms; every node maps to a
user-code line; correctionlib + ONNX inputs record `External` nodes whose descriptors content-hash the
file. (The integration tests caught a real q8 port bug that the record-only tests missed.)

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

## M5 additions (necessary-buffer projection)

- `graphed_awkward.project(array, *, on_fail)` — column projection via a **reporting typetracer**:
  builds one per source (metadata only, no data read), replays the recorded stages symbolically,
  collects touched buffer form-keys, maps them to column names. The output is materialized so its
  columns are touched; an opaque `map` touches its inputs and honors the on-fail policy.
- **Over-touch protected** (`tests/frozen/m5/test_no_overtouch.py`): reading one column reads ONLY
  that column; `jets[jets.pt>30].eta` reads exactly `{Jet.pt, Jet.eta}`, never sibling columns —
  the dask-awkward over-touching bug this milestone exists to avoid. Projection is store-state
  independent (correct whether or not the graph has been M4-reduced).

## M11 factorization (design review 2026-06-10)

`graphed.Array` is backend-idiom-neutral; each backend completes its own idiom. **awkward's idiom
is functions over arrays** — this repo deliberately supplies NO `array_type` proxy and NO member
functions: the user surface is the `gak` namespace (plus ufuncs/operators, which both idioms
share). Pinned by `tests/frozen/m11/test_functions_only_idiom.py`. The numpy method/property idiom
lives in `graphed-numpy` (`NumpyArray`).
