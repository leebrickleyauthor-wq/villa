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

## Limitations

These benchmarks use a standalone reproduction of the relevant processing and are not end-to-end native renderer measurements. The TIFF stack was constructed from public surface-volume layers, not produced by a native rendering invocation. The ambiguous-rank fallback can allocate a float32 copy; the histogram adds modest overhead in such cases. Integrated repository tests and native renderer benchmarks remain pending. No claim of recovered text or an improvement to the whole pipeline is made.

The follow-up PR should be based on upstream main after #2446 merges, not on this development branch as-is.
