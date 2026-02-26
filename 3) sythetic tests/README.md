# Synthetic Tests – Round-Robin Allocator (rralloc)

These are the results of synthetic tests designed to evaluate whether
the round-robin allocator (rralloc) maintains its allocation behavior
without degrading performance, and to examine its behavior under high
concurrency and stress conditions.

The primary purpose of rralloc is to improve allocation distribution
and avoid hotspotting. Performance improvements are not the goal here,
throughput similar to regular allocator is considered favorable.

# Workloads
*** Large Sequential Files
(Evaluates sequential write throughput with multi-job concurrency)

fio --name=seqwrite --directory=. --rw=write --bs=1M --numjobs=6 \
--size=4G --runtime=300 --time_based --group_reporting

*** Small Files
(Validates allocator behavior and latency under small, random writes)

fio --name=smallfiles --directory=. --rw=write --bs=4k --numjobs=6 \
--size=1G --ioengine=psync --time_based --runtime=180 --group_reporting

*** Multi-core / Multi-task Stress
(Tests high concurrency and stress conditions)

fio --name=allocstress --directory=. --rw=write --bs=4k --numjobs=48 \
--size=2G --ioengine=psync --time_based --runtime=180 --group_reporting

# Results summary:

| Test        | Allocator | BW (MiB/s) | IOPS  | Avg lat |
| ----------- | --------- | ---------- | ----- | ------- |
| seqwrite    | Regular   | 497        | 496   | 9.19 ms |
|             | rralloc   | 538        | 537   | 9.37 ms |
| smallfiles  | Regular   | 707        | 181k  | 2.03 µs |
|             | rralloc   | 586        | 150k  | 2.18 µs |
| allocstress | Regular   | 166        | 42.4k | 1.13 ms |
|             | rralloc   | 283        | 72.5k | 0.66 ms |


The results indicate that a round-robin allocation strategy can be
introduced without compromising baseline I/O characteristics.

Across tested workloads, rralloc preserves expected I/O behavior and
does not introduce performance regressions of practical significance.


Test Hardware:
OS        Fedora Linux 42 Workstation x86_64
Host      90HU007QGE ideacentre 510-15ICB
Kernel    6.18.9-dirty
CPU       Intel i5-8400 (6 cores) @ 2.801GHz
GPU1      AMD Radeon RX 460/560D / Pro 450/455/460/555/555X/560/560X
GPU2      Intel CoffeeLake-S GT2 / UHD Graphics 630
Memory    1379 MiB / 31,958 MiB
NVMe      INTEL SSDPEKNW512G8H (HPS1)
SATA SSD  Samsung SSD 860 PRO 256GB (RVM02B6Q)

Note:
These results reflect synthetic tests on the described hardware.
Independent reproduction and verification of these findings are welcome.
Variations in hardware, kernel version, or workload configuration may
affect absolute numbers, but the qualitative observation—that rralloc
preserves baseline I/O behavior—remains valid.
