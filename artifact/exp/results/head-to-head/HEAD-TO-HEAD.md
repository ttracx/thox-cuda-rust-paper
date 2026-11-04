# Head-to-head: thox-edge-ops CUDA C++ RMSNorm vs thox-tile-ops Tile RMSNorm

Run: 2026-11-03, the THOX RTX 4060 Ti workstation (sm_89) (WSL2, driver 630.20, CUDA 13.3.1), the same host and day as the Tile bench it is compared against. This closes the last open item of the adoption gate (the edge-ops comparison). The edge-ops source repo was read, not modified.

## Bottom line

Under the as-implemented launch-path timing both benches use (host wall clock including output allocation and copy-back), the result splits by shape:

| Shape | Tile (cutile 0.4.0) median | edge-ops C++ median | Faster launch path |
|---|---|---|---|
| 1 x 2048 | 0.0810 ms | 0.0274 ms | C++ by 2.96x |
| 128 x 4096 | 0.3118 ms | 0.4726 ms | Tile by 1.52x |

Both kernels are numerically identical against the FP32 CPU reference at these shapes (same max diffs to the digit, below). The C++ kernel alone, measured with CUDA events on a preallocated output, takes 4.1 us (1x2048) and 8.2 us (128x4096) — so at these sizes both wall-clock figures are dominated by allocation, launch, and copy-back overhead, not kernel math. No kernel-only Tile figure exists (the Tile bench never measured one), so **no kernel-vs-kernel speed claim is made**. What was compared is the full launch path each implementation presents today, which is the number a caller would actually pay per call.

## What was run on the C++ side, exactly

Source: HF `Thox-ai/thox-edge-ops-src` @ `605f73c5f937263ec9f6a337e7c91bb107ae2c14` (private; read via the connector). File `edge_ops_cuda/edge_ops.cu` SHA-256 `a5a141b215b00439bdd4bb1fae7801bc1d5fc2c684bbb023a1d30e44ffb8f3e5` (copy kept beside this file as `edge_ops.cu.provenance`), plus `edge_ops_cuda/reduce.cuh` used unmodified.

Harness (`bench_edge_rms.cu`, in this directory):
- `rms_norm_kernel` and the `threads_for` host helper are copied **verbatim** from edge_ops.cu; `thox::Vec` / `thox::block_reduce_sum` come from the repo's own reduce.cuh, unmodified.
- The launcher replicates the FP32 branch of the repo's `rms_norm()` entry point exactly: vector width VN = 4, vector path taken because hidden % 4 == 0 and all pointers are 16-byte aligned (cudaMalloc output is 256-byte aligned), grid = one block per row, threads = `threads_for(hidden/4)` = 512 (1x2048) and 1024 (128x4096).
- Compiled on the host: `nvcc -O3 -std=c++17 -arch=sm_89`, CUDA 13.3.1.
- The repo's torch/ATen binding layer was NOT used: the host has no torch, and edge_ops.cu includes `<torch/all.h>`, so the packaged extension could not be built as-is in reasonable time. The kernel device code and launch geometry are the repo's; the host driver is this harness. This is the strongest honest comparison available on this host today, and the distinction matters: a caller going through the torch binding would additionally pay torch op-dispatch overhead, which is not measured here.

Repo state verified during this lane (read-only): the source repo's `build.toml` defines a single kernel, `thox_rmsnorm`, language `cpp-cpu`, backends `["cpu"]` — the CUDA and Metal sources are present in the tree but orphaned from the build, exactly as today's review reported. The artifact repo `Thox-ai/thox-edge-ops` @ `cc340750` currently lists only a Metal (darwin) variant on main. Neither repo was changed.

## Protocol and timing definitions

Shared protocol (mirrors thox-tile-ops `src/bin/bench.rs`): FP32, eps 1e-6, identical input data (LCG, x seed 0x7407, w seed 0x05A4), inputs uploaded once and resident outside the timing, one untimed first launch, 3 warmups, 30 timed reps, median with q1/q3 by the same quantile method.

- Tile side timing (from the earlier run, files `../bench-*.json`): host wall clock around `launch()` = output tensor allocation + Tile kernel launch + sync + copy back to a host Vec. First launch 186.4 ms / 196.2 ms (includes Tile IR JIT).
- C++ side, primary series (`result-*.json`, mode `wall-alloc-launch-d2h-pageable`): host wall clock around { cudaMalloc(output), kernel launch, cudaMemcpy D2H to a pageable host buffer (blocking, so it synchronizes) }. First launch 0.86 ms / 1.11 ms (CUDA context + module load; NOT comparable to Tile's JIT figure and not a like-for-like number).

Known non-equivalences, stated plainly:
1. **Copy-back path.** The C++ primary series copies to pageable host memory; what buffer type cutile's `to_host_vec` uses internally is not visible from the bench. At 128x4096 the output is 2 MiB, where this choice is worth a large fraction of the total. A pinned-memory variant was run as a sensitivity check (below); it narrows but does not close the gap.
2. **Deallocation.** The C++ clock stops before `cudaFree(output)`; whether the Tile side's timed region includes its output tensor's drop is not determinable from bench.rs. cudaFree of a just-used buffer can synchronize, so this favours the C++ side by an unmeasured amount.
3. **Allocation mechanism.** Per-rep allocation is inside both timings, but the allocators differ (CUDA caching behaviour of raw cudaMalloc vs cutile's tensor allocation). This is inherent to comparing launch paths as implemented.
4. Cross-process variance at the small shape is real: a pinned-host-buffer series at 1x2048 measured 0.0433 ms median, slower than the pageable series' 0.0274 ms despite the nominally faster copy path — at 8 KiB output the copy is irrelevant and the difference is host/launch noise between processes. Small-shape ratios should be read as approximate.
5. WSL2 host: both sides ran under the same WSL2 driver shim on the same day, so the comparison is internally fair; absolute numbers may differ on bare-metal Linux.

## Results

Primary comparison (Tile from `../bench-*.json`; C++ primary series, pageable; a repeat pageable series from an earlier build of the same harness measured 0.0280 / 0.4812 ms, consistent):

| Shape | Side | Median ms | q1 | q3 | First launch ms |
|---|---|---|---|---|---|
| 1x2048 | Tile | 0.0810 | 0.0808 | 0.0830 | 186.44 |
| 1x2048 | C++ (pageable) | 0.0274 | 0.0273 | 0.0278 | 0.86 |
| 128x4096 | Tile | 0.3118 | 0.3090 | 0.3169 | 196.22 |
| 128x4096 | C++ (pageable) | 0.4726 | 0.4624 | 0.4891 | 1.11 |

Supplementary C++ series (same harness, same day; diagnostics, not the head-to-head metric):

| Shape | Variant | Median ms | q1 | q3 |
|---|---|---|---|---|
| 1x2048 | pinned D2H (`result-1x2048-pinned.json`) | 0.0433 | 0.0431 | 0.0441 |
| 128x4096 | pinned D2H (`result-128x4096-pinned.json`) | 0.4232 | 0.4152 | 0.4448 |
| 1x2048 | kernel-only, CUDA events, preallocated (`result-1x2048-kernelonly.json`) | 0.0041 | 0.0038 | 0.0041 |
| 128x4096 | kernel-only, CUDA events, preallocated (`result-128x4096-kernelonly.json`) | 0.0082 | 0.0081 | 0.0082 |

Reading: kernel math is 4–8 us; the remaining ~20–460 us on both sides is allocation, launch, synchronization, and copy-back. At 128x4096 the Tile launch path's total (0.3118 ms) beats both C++ wall variants, which are dominated by the 2 MiB copy-back; at 1x2048 the C++ path's lower fixed overhead wins. If a future caller keeps inputs and outputs resident and calls repeatedly (the production integration shape), neither of these wall numbers is the right metric — the kernel-only series and a resident-output Tile measurement would be, and the Tile side of that pair has not been measured.

## Correctness

C++ kernel output vs the FP32 CPU reference (same formula and summation order as thox-tile-ops `rms_norm_reference`; rel diff denominator max(|ref|, 1e-6); criterion rel <= 1e-5 or abs <= 1e-5):

| Shape | Max abs diff | Max rel diff | Parity | Deterministic (first == last) |
|---|---|---|---|---|
| 1x2048 | 4.768e-07 | 2.982e-07 | PASS | yes |
| 128x4096 | 4.292e-06 | 1.410e-06 | PASS | yes |

These diffs are identical to the Tile side's recorded diffs at the same shapes — both implementations land on the same FP32 result within the same rounding, which cross-validates the two benches' data generation and references.

## Files

- `bench_edge_rms.cu` — the harness (verbatim kernel provenance noted in its header).
- `reduce.cuh` — the repo's helper header, unmodified.
- `edge_ops.cu.provenance` — the repo file the kernel was taken from, for audit.
- `result-1x2048.json`, `result-128x4096.json` — primary C++ series (30 raw reps each).
- `result-*-pinned.json`, `result-*-kernelonly.json` — supplementary series.
- Tile counterparts: `../bench-1x2048.json`, `../bench-128x4096.json`; host facts: `../host.txt`.
