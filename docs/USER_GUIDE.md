# User Guide — CUDA Rust on ThoxOS (paper repository)

## What this repository is

This is the publication record for THOX.ai Research technical report
**THOX-TR-2026-11-03, "CUDA Rust on ThoxOS", v0.2.0 (public edition)**,
by Tommy Xaypanya (THOX.ai LLC), DOI
[10.5281/zenodo.23955185](https://doi.org/10.5281/zenodo.23955185).
It holds the paper, its interactive demo, and the evidence files every
printed number traces to. It does not hold the kernel source code;
the Tile RMSNorm crate lives in its own private repository
(`ttracx/thox-tile-ops`).

## Who it is for

- Readers evaluating Rust for NVIDIA GPU kernel authoring.
- ThoxOS engineers deciding which kernel track a host should bind.
- Reviewers who want to check a claim in the paper against its
  evidence file.

## Reading the paper

Open `paper/THOX-CUDA-Rust-on-ThoxOS-v0.2.0.pdf`. Structure: the two
CUDA Rust tracks reviewed (§§2–4), the GPU-less build/safety results
(§5), the device qualification and same-day head-to-head against the
production CUDA C++ kernel (§6), the ThoxOS GPU layer and its virtual
lab (§7), and honest limitations (§8). The canonical archival copy is
the Zenodo record: https://zenodo.org/records/23955185

## Running the demo

Open `paper/THOX-CUDA-Rust-on-ThoxOS-v0.2.0-demo.html` in any modern
browser. It is a single self-contained file (fonts and data embedded,
no network requests) with four tools: the kernel binding checker, the
same-day Tile versus CUDA C++ comparison with its caveats, the kernel
ownership checker, and the RMSNorm reference.

## Verifying a number

Every measured number in the paper traces to `artifact/exp/results/`:

- `summary.json` — GPU-less evidence (toolchains, header matrix,
  aliasing cases, no-driver behaviour, build cost).
- `device-run/` — the RTX 4060 Ti (sm_89) qualification: run record
  plus the two 30-run bench JSONs.
- `head-to-head/` — method, caveats, and result JSONs for the
  comparison against the production edge-ops CUDA C++ kernel.

`artifact/wp/QA-REVIEW.md` is the non-producer review that re-checked
the printed numbers against these files before publication.

## Citing

Use `CITATION.cff` (GitHub renders a "Cite this repository" entry
from it) or the BibTeX in `paper.bib` (canonical name; `citation.bib`
is an identical copy). Always cite the DOI:
10.5281/zenodo.23955185.

## Troubleshooting

- **Demo does not render:** use a current browser with JavaScript
  enabled; the file needs no server and no network.
- **Hash mismatch on the PDF:** re-download from the `v0.2.0` release
  or Zenodo; the canonical SHA-256 is
  `9aba898eea4591792e753692bb9f7776d829e8f2d8ec25dfdd2db21f01363b8f`.
- **A number looks wrong:** check it against the evidence JSONs
  above; if it still disagrees, open an issue with the section and
  the evidence key.

## FAQ

- **Is the Tile kernel faster than CUDA C++?** The paper makes no
  kernel-versus-kernel speed claim. On the full launch path the
  result splits by shape (C++ 2.96x faster at 1x2048, Tile 1.52x
  faster at 128x4096) and both figures are overhead-dominated.
- **Does ThoxOS now default to Rust GPU kernels?** No. Tile is
  adopted for new kernel authoring on qualified hosts behind the GPU
  layer fallback; the production default stays on CUDA C++.
- **Is Jetson supported?** Not yet run. The paper states this as a
  limitation, not a roadmap promise.
