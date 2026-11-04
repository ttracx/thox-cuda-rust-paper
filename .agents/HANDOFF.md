# HANDOFF — CUDA Rust on ThoxOS (paper repo)

## Status

- **v0.2.0 public edition PUBLISHED 2026-11-04.** Paper, demo, and
  evidence are on `main`; formal release `v0.2.0` carries the PDF
  and demo as assets (canonical bytes, hashes below).
- Zenodo: DOI **10.5281/zenodo.23955185** (concept
  10.5281/zenodo.23955184), record
  https://zenodo.org/records/23955185 — deposited and verified live
  before this repo was created; the DOI is in the README,
  CITATION.cff, both BibTeX files, and the release notes.
- Publication basis: Tommy Xaypanya's explicit publish order of
  2026-11-04 for this paper ("Review then publish, update our
  website, huggingface, etc"), executed per the THOX Research Paper
  Process v1.0. Sole author of record: Tommy Xaypanya.

## Edition of record

| File | Bytes | SHA-256 |
|---|---|---|
| paper/THOX-CUDA-Rust-on-ThoxOS-v0.2.0.pdf | 396,798 | 9aba898eea4591792e753692bb9f7776d829e8f2d8ec25dfdd2db21f01363b8f |
| paper/THOX-CUDA-Rust-on-ThoxOS-v0.2.0-demo.html | 197,813 | 7884c23a222c82bbeebb1a4ce397f6cdcbb77fae61e17e44b7eaaa5b9d3d1448 |

Paper: THOX-TR-2026-11-03, v0.2.0, 2026-11-04, sole author Tommy
Xaypanya (CTO & Co-Founder, corresponding), THOX.ai LLC, Cedar Park,
Texas, ORCID 0009-0000-4425-9565, CC BY 4.0, preprint (not peer
reviewed). Non-producer review: `artifact/wp/QA-REVIEW.md`.

## Surfaces (association pass, 2026-11-04)

- Zenodo record (above) — canonical archival copy.
- Website paper page: https://thox.ai/research/papers/cuda-rust-on-thoxos
- Hugging Face: `cuda-rust/` folder in Thox-ai/thox-whitepapers.
- This repo + release v0.2.0.
- Code association (private, by design): ttracx/thox-tile-ops.

## Key files

- `README.md` — §3-standard README (badges, DOI block, hashes).
- `paper/` — canonical shipped bytes. Never edit in place.
- `artifact/exp/results/` — the only evidence source for printed
  numbers. `artifact/wp/QA-REVIEW.md` — the pre-publication review.
- `CITATION.cff`, `paper.bib` (canonical BibTeX name per the THOX
  paper-repo template), `citation.bib` (identical copy).

## Conventions

- Repository Creation Standard v1.0 applies in full (badges,
  governance note, docs set, formal release per version).
- New edition = new version on Zenodo + GitHub + website in one
  pass; update `.agents/HANDOFF.md` in the same commit series.
- No kernel source code in this repo, ever: thox-tile-ops is private
  by design and no public-code order covers it.

## Blockers and open questions

- None for v0.2.0. Future evidence (Jetson/Orin run, kernel-only
  Tile timing) belongs to a future edition, not a patch to this one.

## Last verified

- 2026-11-04 by Sadie Weaving (GitHub lane): repo, release, and
  anonymous readback verified at creation (see the lane report in
  the paper workdir, review-v020/github-report.md).
