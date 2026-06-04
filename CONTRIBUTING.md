# Contributing to graphed-awkward

Part of the `graphed` project, governed by the gated three-role pipeline. The root
[`graphed-project/CLAUDE.md`](https://github.com/graphed-org/graphed-project) and the project plan
are authoritative; the plan always wins.

## Guardrails (M3)

- **Reuse awkward typetracer** for `op_form` (metadata only — no data read) and real awkward for
  `eval_stage`; do not reimplement type inference.
- **Reuse correctionlib / ONNX** — invent no correction or model formats. External payload
  descriptors content-hash the actual file.
- **No optimization** (M4); **no fusion**. Column projection is M5.

## Integrity rules — NON-NEGOTIABLE (plan A.7 / B.6)

Never edit/skip/weaken `tests/frozen/**`; never lower a threshold or relax CI; never stub the thing
under test. Dispute a frozen test via `.graphed/<Mx>/disputes/<test_id>.md`.

## Local gates

```bash
pip install "graphed-core @ git+https://github.com/graphed-org/graphed-core@main"   # needs Rust
pip install "graphed @ git+https://github.com/graphed-org/graphed@main" \
            "graphed-corpus @ git+https://github.com/graphed-org/graphed-corpus@main"
pip install -e ".[dev,docs]"
ruff check . && ruff format --check . && mypy
pytest tests/frozen/m3 --cov=graphed_awkward --cov-branch   # >=90%
sphinx-build -W -b html docs docs/_build/html
```
