# ScrollMemory: bounded-memory percentile optimisation

Incremental contribution by Lee Brickley, developed with AI assistance, based on PR #2446 by stantheman0128 (head 6222de0749401a4209b35453d1989de62d9ce2a6). The existing blockwise composite and LUT work is credited to that PR and its contributors.

## Results

Standalone post-processing benchmark, five trials per method, Google Colab, NumPy 2.5.2. Median seconds / additional peak RSS MiB: upstream 1.792806 / 619.012; PR #2446 1.666385 / 131.238; histogram 1.132717 / 132.809. Time reductions 36.8% and 32.0% respectively. Most memory savings relative to upstream derive from PR #2446; the histogram uses about 1.2% more additional memory than that PR.

Data: public S3 Zarr `vesuvius-challenge-open-data/PHerc0139/segments/20250108000000-w025_2025010863/surface-volumes/9.362um-1.2m-113keV-volume-20250728140407.zarr`, level 0, layers 12–16, uint8, 7280 x 6800. Five layers were exported to TIFF and max-composited. Integrated scaling took 1.141338 seconds in a single run; p95 167.0, exact reference percentile bytes and pixels. Output SHA256 c30dde5f4d3c1b173ea031a2b2d03ea6871404874471b8407c81fb91c41b41ac.

Twelve synthetic stress cases matched reference percentile bytes and pixels. Local integrated regression suite: 12 passed. Wider comparison: baseline 95 passed, 10 failed, 9 skipped; modified 96 passed, same 10 failed, 9 skipped. Failures were missing vc_object_store._native in Colab.

## Limitations

This is post-processing, not an end-to-end 3D renderer benchmark. Fast path restricted to NumPy 2.3.5 and 2.5.2 and contiguous unsigned 8/16-bit arrays; other releases retain the existing PR percentile approach. Results are local measurements, not independent verification. Raw benchmark scripts and reports are not yet included in this branch. Community adoption and prize eligibility are not established.
