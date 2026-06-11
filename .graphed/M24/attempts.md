# M24 attempts — graphed-awkward (gak <-> ak interface parity)

## Iteration 0 — 2026-06-11 (freeze-M24-0)

- USER: "Check all awkward ops in gak, including zip, and bring gak to interface parity for
  genuinely missing functionality."
- Mechanical signature audit (inspect-based): 38 gak functions with deltas. Triage: naming-only
  (no action), DELIBERATE exclusions (highlevel/attrs eager-only; behavior backend-owned, M18),
  Phase-2 niche (right_broadcast, optiontype_outside_record, broadcast rules, mergebool,
  including_unknown, unzip how, nan_to_num copy), and the ADD set implemented here.
- ADDED: zip sequence->tuple records + depth_limit + with_name + parameters(JSON);
  combinations/argcombinations replacement/axis/with_name/parameters; cartesian/argcartesian
  axis/nested-list/with_name/parameters; reducers keepdims/mask_identity (per-op ak defaults
  mirrored exactly)/initial(min,max); WEIGHTED mean/var/std/moment (weight = a second graph
  INPUT, not a param); sort/argsort stable; drop_none axis; nan_to_num nan/posinf/neginf;
  isclose equal_nan; broadcast_arrays depth_limit; zeros/ones_like dtype=None preserves dtype.
- DEFAULT FIXES to ak parity (no consumer relied on the old defaults — swept): concatenate
  axis 1->0; sort/argsort/local_index axis 1->-1; argmin/argmax/count axis 1->None;
  cartesian nested False->None; zeros/ones_like dtype 'int64'->None.
- frozen suite tests/frozen/m24 (19 tests) incl. the inspect-based ANTI-DRIFT defaults pin
  (it caught my own wrong mask_identity assumption for mean/var/std/moment during authoring —
  recorded) and an IR byte-determinism pin over the new params. NON-VACUOUS: 18/19 failed
  pre-implementation. Two pre-freeze authoring fixes recorded: scalar materialize comparison
  for argmax(axis=None); a malformed combinations-over-depth-limited-records expression.
- gates: 244/244 PASS · coverage 95.37% (>=90, branch) · ruff+format · mypy --strict ·
  sphinx -W clean. Downstream sweep green: graphed, graphed-numpy, exec-local, histogram,
  preserve, checkpoint, uproot fork, hist fork.
