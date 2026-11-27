# ScrollMemory follow-up (development evidence)

This branch is based on the original ScrollMemory submission, which builds on Stan Shih's PR ScrollPrize/villa#2446 (head 6222de0749401a4209b35453d1989de62d9ce2a6). The original PR's memory changes remain credited to its authors. Developed with AI assistance. The original prize-submission branch is unchanged.

## Measurements

Public PHerc0139 surface volume: https://vesuvius-challenge-open-data.s3.us-east-1.amazonaws.com/PHerc0139/segments/20250108000000-w025_2025010863/surface-volumes/9.362um-1.2m-113keV-volume-20250728140407.zarr

Level 0, layers 12–16, 7280 × 6800 pixels; NumPy 2.4.6; medians of five trials per method, alternating order.

| Standalone benchmark | PR-style baseline | Revised histogram |
|---|---:|---:|
| Percentile calculation | 0.390334 s | 0.078463 s |
| TIFF-to-JPEG post-processing | 0.656434 s | 0.349187 s |

All trials in each benchmark matched percentile bytes and output pixel SHA-256. All post-processing trials matched JPEG SHA-256. p95=167.0. Pixel SHA-256: c30dde5f4d3c1b173ea031a2b2d03ea6871404874471b8407c81fb91c41b41ac. JPEG SHA-256: db54eb0c55ef64723ae026ace30c2c301a33c6aa8a49e695bbbc38aba1cd141c.

Standalone synthetic compatibility suite: 112 exact matches on NumPy 2.3.5 in the local environment. The implementation does not rely on a version whitelist; this is not a guarantee of correctness on arbitrary future NumPy versions.

## Integrated tests

At commit d6b41edc568a16e6c46770ff6bdeb64e821a0d15, 13 repository render_ink tests passed in 8.99 seconds on Python 3.13.18 and NumPy 2.4.6 (Linux x86_64). See scrollmemory_integrated_check.json. The project declares Python >=3.14 and NumPy >=2.5.0, so testing in the declared environment remains desirable.

## Native one-segment rendering

PHercParis4 segment 20231012184424; public mesh PHercParis4/segments/20231012184424/mesh/20231012184424-on-20260411134726-2.4um.tifxyz/; volume https://vesuvius-challenge-open-data.s3.us-east-1.amazonaws.com/PHercParis4/volumes/20260411134726-2.400um-0.2m-78keV-masked.zarr/

Official renderer: VC3D-f7756c7-2026-11-03-linux-x86_64.AppImage, SHA-256 17d9f2649c5f42b76c39e4e09ec87412002f505577e48400693c477b581e9c05. Baseline PR head 6222de0749401a4209b35453d1989de62d9ce2a6; revised d6b41edc568a16e6c46770ff6bdeb64e821a0d15. Python 3.13.18, NumPy 2.4.6, Linux x86_64. Both invoked render_ink.py with --strips --no-flatten --no-full-scroll --group-idx 4 --scale 2 --num-slices 5 --fail-on-empty.

| Run order | Method | Wall time (s) |
|---|---|---:|
| 0 (warm-up, excluded) | Baseline | 93.332078 |
| 1 | Baseline | 69.313402 |
| 2 | Revised | 68.458225 |
| 3 | Revised | 65.899010 |
| 4 | Baseline | 68.698776 |
| Median (timed) | Baseline | 69.006089 |
| Median (timed) | Revised | 67.178617 |

All five invocations exited successfully. Each produced five TIFFs and a 9465 x 6350 JPEG; the SHA-256 of each corresponding output file matched across all five runs. Printed p95 was 119.0 in every run. JPEG SHA-256: 8114d78960260495178737e62555209b18bf2b667c7d5af12c78e296405dab37.

The observed wall-time reduction is 2.65%; two timed runs per method are insufficient to attribute this difference confidently to the optimisation. This is principally native-workflow output-equivalence evidence. No native peak-memory measurements were collected. The renderer warned that its default 16 GB cache exceeded available process memory (12.7 GB), but all runs completed. Exact mesh equivalence to the original developer's local copy is not established.

## Limitations

The PHerc0139 benchmarks use a standalone reproduction of the relevant processing; their TIFF stack was constructed from public surface-volume layers. The separate Paris 4 native experiment does not cover flattening, full-scroll rendering or the default level 1. The ambiguous-rank fallback can allocate a float32 copy; the histogram adds modest overhead in such cases. No claim of recovered text or a statistically established improvement to the whole pipeline is made.

The follow-up PR should be based on upstream main after #2446 merges, not on this development branch as-is.
