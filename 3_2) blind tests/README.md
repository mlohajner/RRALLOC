# Test Configuration Details

| Metrics            | **Test 1** | **Test 2** | **Test 3** |
| ------------------ | ---------- | ---------- | ---------- |
| BW (MiB/s)         | 533        | 533        | 532        |
| IOPS               | 136k       | 136k       | 136k       |
| Avg clat (µs)      | 351.5      | 351.5      | 352.1      |
| Stddev clat (µs)   | **5335**   | 4863       | **4777**   |
| Median (50th) (ns) | 2480       | **2416**   | 2480       |
| 99th (ns)          | 7520       | **6816**   | 7008       |
| 99.50th (ms)       | 58.4       | 58.4       | 58.4       |
| 99.90th (ms)       | 60.5       | 60.5       | **59.5**   |
| 99.95th (ms)       | **65.8**   | 62.6       | **60.5**   |
| 99.99th (ms)       | **127**    | 102        | **89**     |
| Max latency        | **1.46 s** | 268 ms     | 275 ms     |
| Disk util          | 99.08%     | 99.00%     | 99.02%     |

These results represent three allocator variants evaluated under
identical workload conditions (4k sync writes, 48 jobs, iodepth=1,
device fully saturated ~99% util):

Test 1 – stock EXT4 allocator
(global goal hash array + dynamic resize logic)

Test 2 – stock EXT4 allocation policy, but refactored to use per-inode
cursors instead of the global goal hash array
(architectural change only, no policy change)

Test 3 – full rralloc implementation (per-inode cursors + per-CPU
cursors; deterministic round-robin allocation policy)

Important:
Throughput and average latency remain effectively identical across all
variants (device-limited workload). The measurable differences appear
exclusively in latency distribution stability
(stddev and high-percentile tails), where progressive removal of global
goal hashing and introduction of deterministic cursors reduces extreme
outliers.

This indicates that the architectural change
(eliminating global goal hashing in favor of per-inode state)
improves allocation determinism and tail stability without affecting
peak throughput.
