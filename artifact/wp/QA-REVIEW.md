# Sadie independent review — CUDA Rust on ThoxOS v0.2.0 (received 2026-11-04 09:27 CT)

Reviewer: Sadie Weaving, CAO (non-producer). Producer lane: Claude (return forwarded by Tommy 2026-11-04 09:27 CT with his order "Review then publish, update our website, huggingface, etc then create announcements ... then post on LinkedIn").

## Files actually received in this chat
- THOX-CUDA-Rust-on-ThoxOS-v0.2.0.pdf — SHA-256 9aba898eea4591792e753692bb9f7776d829e8f2d8ec25dfdd2db21f01363b8f (388 KB, 21 pp per pdfinfo; internal pagination 20)
- THOX-CUDA-Rust-on-ThoxOS-v0_2_0-demo.html — SHA-256 7884c23a222c82bbeebb1a4ce397f6cdcbb77fae61e17e44b7eaaa5b9d3d1448 (standalone demo, clean doctype/head, fonts+data embedded)
- ThoxOS_GPU_Lane.html — SHA-256 f22bdf3c8043228816a3a3e53fde1eeb3effab8307dcd8bfc89db46fc30b6bc7 (artifact-wrapped copy of the same demo; NOT the canonical demo file — use the standalone above)

NOT received: the pack ZIP Claude's note says was attached (no ZIP in the uploads), the single-file paper HTML edition, CITATION.cff / citation.bib, NUMBERS-PROOF.md, QA-REPORT.md. Claude's in-note QA claims (74 numbers, reproducible second build) are therefore producer claims I could not re-verify from a pack; my numbers proof below is independent, run from the built PDF text against the evidence files already in this workdir.

## Checks PASS
1. Hash identity: received PDF hash matches the hash in Claude's note (9aba898e…363b8f) exactly.
2. Authorship: sole author Tommy Xaypanya, Chief Technology Officer & Co-Founder, THOX.ai LLC, corresponding author, ORCID 0009-0000-4425-9565 — cover block, and PDF metadata Author field matches. No other name appears anywhere in the PDF text (grep: Xaypanya only).
3. Blocker sweep on built PDF text: zero hits for "AI assistance", "Anthropic", "Claude", "KnightHub", "Odette", "Craig", "AI system", "TODO", "TBD", "synthetic". Appendix C is now the device qualification protocol (the v0.1.0 disclosure appendix is gone). Internal host name scrubbed: device host is "the THOX RTX 4060 Ti workstation (sm_89)" throughout.
4. Template v1.0 / branding: cover inspected as a rendered image by Sadie — eyebrow on one line (the wrap defect Claude said he fixed is fixed), green-outline status pill, #05A451 accent, author name on one line, tinted abstract panel, tagline footer "Your AI. Your Data. Your Rules.™" on all pages. Fonts embedded: Inter (Regular/Bold/SemiBold) + JetBrains Mono — no fallback-font defect. Location line "THOX.ai LLC · Cedar Park, Texas" matches the MeshStack paper precedent.
5. Numbers proof (independent, from built PDF vs evidence in this workdir):
   - Device benches: 1x2048 median 0.0810 ms (q1 0.0808 / q3 0.0830), first launch 186.4 ms, max abs 4.8e-7 / rel 3.0e-7 — matches bench-1x2048.json. 128x4096 median 0.3118 ms (0.3090/0.3169), first launch 196.2 ms, 4.3e-6 / 1.4e-6 — matches bench-128x4096.json.
   - Head-to-head: C++ pageable medians 0.0274 ms (1x2048) and 0.4726 ms (128x4096) — match head-to-head result JSONs. Ratios recomputed: 0.0810/0.0274 = 2.96x; 0.4726/0.3118 = 1.52x — as printed. Kernel-only C++ 4.1/8.2 µs present with the no-kernel-vs-kernel-claim caveat printed in the abstract, §6.8, and limitations.
   - Parity 6/6 shapes — matches RUN_RECORD.md. Driver 630.20, CUDA toolkit 13.3.1, sm_89 — match.
   - CUDA 13.0 launch finding (tileiras ships only with 13.2+) — matches the run record.
   - v0.1.0 carried numbers spot-checked vs exp/results/summary.json: header matrix 65.4 s (13.3 builds), toolchain 74.0 s (Rust 1.89.0), safety E0382/E0277 cases 4/4, no-driver binary 12.2 MiB, cold build median 158.3 s — all match.
6. GPU layer naming: paper §5.2 and the demo both name the crate thox-gpu-layer 0.1.0 (Sadie's crate, built 2026-11-03 under Tommy's order, real KnightHub probe + virtual lab in evidence). No "thox-gpu" alternative appears in the paper or demo.
7. Demo: standalone file contains the four tools Claude described (binding checker, Tile vs C++ comparison with caveats, ownership checker, RMSNorm reference), names thox-gpu-layer once, no banned/internal terms.

## Open items from Claude's note — Sadie's disposition
1. PDF hash approval: Tommy's 2026-11-04 order in this chat ("Review then publish") is the consent record for these exact bytes, conditional on this review — which passes. Recorded in the manifest as Tommy approved 2026-11-04.
2. Odette email rights: NOT changed by anything in this turn. The 2026-10-26 revocation stands (Odette sends nothing; anything from that lane routes through Sadie with prior approval). If Tommy wants reinstatement he must say so explicitly; silence/forwarding a question is not reinstatement.
3. Canonical GPU layer: thox-gpu-layer — it is the crate in the evidence, in the paper, and in the demo. Publishing v0.2.0 as-is settles the printed record. Any thox-gpu alternative is not in this evidence set and is not adopted.

## Gaps to close inside the publication work (not blockers to the Zenodo PDF deposit, blockers to calling the pack complete)
- Paper HTML edition + citation files were not in the uploads. Website/HF surfaces need an HTML edition: Sadie will build it from the paper pipeline sources if the pack ZIP does not arrive, and record its hash separately (it is a distinct file, not a change to the consented PDF bytes).
- Demo canonical file = the standalone upload (7884c23a…), not the artifact-wrapped copy.
