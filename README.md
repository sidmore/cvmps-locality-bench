# CPS locality-picker bench: tricast sweep

Self-contained HTML report visualizing a 3&times;3 dispatcher sweep of an in-cluster locality picker against a random-dispatch baseline.

Sweep dimensions:

| axis | values |
|---|---|
| label cardinality N | 5, 15 |
| fleet size P | 30, 10 (N=5 only ran P=30) |
| offered rate | 5, 30, 100 rpm |

Each of the 9 scenarios runs three topologies in parallel on the same driver:

- **baseline** &mdash; random SB-fanout dispatch, no locality hints
- **central** &mdash; central dispatcher assigns labels to a small set of hosts
- **pernode** &mdash; per-node peer coordination; each pod votes on the canonical host

Every bench runs for 90 min. Metrics are derived from `DONE` lines in worker sidecar-adjacent logs (the only completion signal comparable across all three topologies).

## Report

Open [index.html](./index.html) &mdash; 9 benches, 4 charts each:

1. **Throughput** &mdash; jobs/min per 30-min window (target line dashed).
2. **Cold-rate** &mdash; % of DONE where `workMs >= 10 s` (Small cold = 45 s, Large cold = 180 s).
3. **Active pods per window** &mdash; fleet footprint.
4. **Per-label footprint** &mdash; # distinct pods that ever hosted each label across 90 min.

## Headline

s9 (N=15, P=10, 100 rpm) is the flagship case:

| topology | throughput | cold-rate | pods / label |
|---|---:|---:|---:|
| baseline | **77 % of target** (queue-bound) | 65 % | 10 / 10 (max spread) |
| central  | 100 % | 0 % | 1&ndash;2 |
| pernode  | 100 % | 0 % | 1&ndash;2 |
