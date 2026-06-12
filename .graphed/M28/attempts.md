# M28 attempts — graphed-awkward (the external-recording seam, preservation-aligned)

## Iteration 0 — 2026-06-12 (freeze-M28-0)

- Post-M27 analysis exposed three defects in the M3-era recorders (gak.apply_correction /
  gak.onnx_inference): (1) descriptor hashes = RAW FILE BYTES vs the preserve plugins' CONTENT
  IDENTITY (same kind, two identities -> bundle integrity unpassable; latent because m9 never
  recorded through gak); (2) no call template -> record-time (user callable, all inputs) and
  replay (hard-wired legacy shape) could disagree; (3) params {"path": ...} leaked filesystem
  paths into the IR.
- The frozen m3 suite PINS the raw-bytes hash + path param exactly -> the fix is ADDITIVE: with
  args=/kwargs= the recorders take the M28 path (content-identity descriptors via payloads.
  correctionlib_contents_descriptor/onnx_weights_descriptor — algorithms + domain strings
  identical to graphed-preserve's plugins; params carry name + the canonical-JSON template, NO
  path; record-time evaluation MATERIALIZES THE TEMPLATE — constants + native inputs for
  corrections (jagged preserved), float32 group-matrices for onnx — so record == replay by
  construction; descriptor-override recording via the M23 seam, output form approximated by
  the first slotted input). Template-less calls remain the M3 path byte-for-byte (pinned).
- frozen m28 (7): contents-hash formatting-insensitivity + != raw-bytes; onnx weights/structure
  identity; descriptor metadata; template path records path-free with the template + aligned
  hash and passes inputs natively (jagged witnessed via a capturing evaluator); bytes-or-path
  payload equivalence; group stacking for onnx; the legacy path byte-for-byte. Non-vacuous
  (collection failed on the new payload names).
- Gates: 251 passed · coverage 94.88% · ruff/format/mypy/sphinx clean. Cross-repo acceptance
  (bundle integrity + bit-for-bit replay) lives in graphed-preserve frozen m30.
- Per user directive, the FULL cross-repo sweep (11 package repos + 3 forks, 1276+346 tests)
  ran green with M28+M29+M30 applied before this commit.
