# Test Configuration Details

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
