# Development — CUDA Rust on ThoxOS (paper repository)

## Architecture overview

This is a publication repository, not a code repository. It carries
one paper edition of record (v0.2.0), its demo, and its evidence
results. The systems the paper describes live elsewhere:

- `ttracx/thox-tile-ops` (private) — the FP32 Tile RMSNorm crate
  qualified on device in the paper.
- thox-edge-ops / thox-rmsnorm — the production CUDA C++ kernel and
  the CPU numerical oracle the paper compares against.
- The ThoxOS GPU layer (probe, qualification registry, fallback
  order, diagnostics) is specified and prototyped alongside the
  paper; its adoption target is the thox-gpu Accelerator contract in
  the ThoxOS harness.

## Conventions

- **Editions are immutable.** A new paper edition is a new version:
  new files under `paper/`, a new GitHub release, and a new Zenodo
  version under concept DOI 10.5281/zenodo.23955184. Never edit a
  shipped PDF in place; never overwrite a versioned filename.
- **Evidence first.** No number enters the paper or README unless it
  traces to a file in `artifact/exp/results/`. New results land as
  new evidence files before any text cites them.
- **Citations stay in sync.** `CITATION.cff`, `paper.bib`
  (canonical), and `citation.bib` carry the same DOI and version;
  update all three together.
- **Commit style:** short imperative subject, body explains why.
  Branch from `main`, PR back to `main`.

## Build and test commands

There is no build. Validation for any change:

```bash
# Canonical bytes unchanged
sha256sum paper/THOX-CUDA-Rust-on-ThoxOS-v0.2.0.pdf
# expect 9aba898eea4591792e753692bb9f7776d829e8f2d8ec25dfdd2db21f01363b8f
# No stale DOI placeholders anywhere in the publication record
grep -rni "pending" README.md CITATION.cff .agents/HANDOFF.md || echo CLEAN
# CITATION.cff is valid YAML (GitHub citation parsing depends on it)
python3 -c "import yaml,sys; yaml.safe_load(open('CITATION.cff'))"
```

## Work log

- 2026-11-04 — v0.2.0 public edition published: repo created, paper +
  demo + evidence pushed, release `v0.2.0` cut with the PDF and demo
  as assets, DOI 10.5281/zenodo.23955185 live on Zenodo first per the
  THOX Research Paper Process v1.0.

## Known limitations

- The single-file HTML edition of the paper is hosted on the website
  and the Hugging Face dataset folder, not in this repo; `paper/`
  here carries the PDF and the demo.
- Evidence run records retain the internal host naming used at run
  time; the paper text itself uses the public host description.

## Roadmap

- Next edition only when new device evidence lands (Jetson/Orin
  qualification, kernel-only Tile timing); each is a new version on
  Zenodo, GitHub, and the website in one pass.
