# graphed-awkward

The **reference backend** for `graphed` (milestone M3): `op_form` uses the awkward **typetracer**
(metadata only — no event data is read), `eval_stage` uses real awkward. The `gak` namespace mirrors
the awkward API so corpus analyses record a backend-agnostic graph. External inputs (correctionlib
corrections, ONNX models) record `External` nodes with **content-hashed** `PayloadDescriptor`s. Part
of the [`graphed-org`](https://github.com/graphed-org) project; see
[`graphed-project`](https://github.com/graphed-org/graphed-project) for root guidance and the plan.

```python
from graphed import Session
import graphed_awkward as ga
from graphed_awkward import gak

s = Session(ga.AwkwardBackend())
events = ga.from_awkward(s, "events", some_awkward_array)   # form via typetracer (metadata only)
pairs = gak.combinations(events.Muon, 2, fields=["a", "b"])
mass = (2 * pairs.a.pt * pairs.b.pt) ** 0.5                  # records nodes, reads no data
s.form(mass).is_typetracer        # True
```

## What it does (M3)

- `AwkwardBackend`: typetracer `op_form` + real `eval_stage` (one `apply` dispatch for both).
- `gak`: combinations/cartesian/zip/num/sum/any/all/argmin/argsort/firsts/where/... recording nodes.
- `from_awkward` / `from_parquet` metadata-only sources.
- `payloads`: correctionlib / ONNX / dataset descriptors that content-hash the file.
- Validated against the corpus: **ADL queries 1-8 + the AGC object-selection slice** record
  metadata-only with correct forms; `materialize` matches plain awkward.

Reuse awkward / correctionlib / ONNX — invent nothing. Status: see `.graphed/state.json` + `CLAUDE.md`.
