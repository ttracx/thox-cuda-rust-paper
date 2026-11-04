# Quickstart — CUDA Rust on ThoxOS (paper repository)

Five minutes from zero to reading and checking the paper.

```bash
# 1. Clone
git clone https://github.com/ttracx/thox-cuda-rust-paper.git
cd thox-cuda-rust-paper

# 2. Verify the shipped bytes (expect the hashes below)
sha256sum paper/THOX-CUDA-Rust-on-ThoxOS-v0.2.0.pdf
# 9aba898eea4591792e753692bb9f7776d829e8f2d8ec25dfdd2db21f01363b8f
sha256sum paper/THOX-CUDA-Rust-on-ThoxOS-v0.2.0-demo.html
# 7884c23a222c82bbeebb1a4ce397f6cdcbb77fae61e17e44b7eaaa5b9d3d1448

# 3. Read the paper
xdg-open paper/THOX-CUDA-Rust-on-ThoxOS-v0.2.0.pdf      # Linux
open paper/THOX-CUDA-Rust-on-ThoxOS-v0.2.0.pdf          # macOS

# 4. Open the interactive demo (single file, offline)
xdg-open paper/THOX-CUDA-Rust-on-ThoxOS-v0.2.0-demo.html

# 5. Check a headline number against its evidence
cat artifact/exp/results/device-run/bench-1x2048.json
cat artifact/exp/results/head-to-head/HEAD-TO-HEAD.md
```

No build step, no dependencies, no network needed after the clone.
The archival copy with the DOI is at
https://zenodo.org/records/23955185 and the paper page is at
https://thox.ai/research/papers/cuda-rust-on-thoxos.
