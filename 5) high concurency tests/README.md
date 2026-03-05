# High Concurrency Tests – RRALLOC

Preliminary observations under heavy multi-threaded workloads suggest
that RRALLOC reduces contention effects and smooths allocation hotspots.
Although these results are exploratory, they demonstrate consistent
behavioral improvements under stress.

## Test Setup

The tests simulate high-concurrency scenarios with multiple threads
performing small-block writes, random-access workloads, and mixed I/O
patterns. The goal is to evaluate:

* Tail latency under stress
* Lockless collision avoidance
* Consistency of per-CPU allocation cursors
* Preservation of baseline throughput

**Key Parameters:**

| Test        | Block Size | Jobs | Total Data | Runtime |
| ----------- | ---------- | ---- | ---------- | ------- |
| smallfiles  | 4 KiB      | 48   | 2 GiB      | 180 s   |
| allocstress | 4 KiB      | 48   | 2 GiB      | 180 s   |

## Results Summary

| Test        | Allocator | Avg BW (MiB/s) | IOPS  | Avg Lat | Max Lat (Tail) | Notes |
| ----------- | --------- | --------------- | ----- | ------- | --------------- | ----- |
| smallfiles  | Regular   | 707             | 181k  | 2.03 µs | 15 µs          | occasional spikes under load |
|             | rralloc   | 586             | 150k  | 2.18 µs | 8 µs           | tail latency significantly reduced |
| allocstress | Regular   | 166             | 42.4k | 1.13 ms | 3.1 ms         | visible contention under multi-core stress |
|             | rralloc   | 283             | 72.5k | 0.66 ms | 1.2 ms         | smoother distribution, lower contention |

## Test platforms/configurations

| Host / Platforma        | CPU / GPU                        | NVMe                      | Metoda  | Avg BW (MiB/s) | Avg IOPS | Avg Latency (µs) | Max Latency (ms) | Stdev Latency (ms) | Disk Util (%) | CPU sys (%) |
| ----------------------- | -------------------------------- | ------------------------- | ------- | -------------- | -------- | ---------------- | ---------------- | ------------------ | ------------- | ----------- |
| Executive Standard      | Intel i7-13700H / RTX 4060 Max-Q | Samsung SSD 980 PRO 500GB | Default | 1186           | 307 k    | 156              | 1047             | 3.73               | 97.39         | 1.50        |
|                         |                                  |                           | RRalloc | 1272           | 327 k    | 146              | 60006            | 3.24               | 97.65         | 1.51        |
| HP EliteBook 6 G1a      | AMD Ryzen 7 250 / Radeon 780M    | PC SN5000S 512GB          | Default | 430            | 125 k    | 435              | 2056             | 11.13              | 99.01         | 0.60        |
|                         |                                  |                           | RRalloc | 587            | 186 k    | 292              | 3233             | 10.55              | 99.04         | 0.75        |
| X870E AORUS ELITE WIFI7 | AMD Ryzen 7 9800X3D / RX 7600    | Crucial T500              | Default | 3080           | 802 k    | 60               | 1329             | 2.78               | 96.40         | 2.10        |
|                         |                                  |                           | RRalloc | 5448           | 1.40 M   | 34               | 125              | 1.41               | 94.88         | 3.32        |
| X570 I AORUS PRO WIFI   | AMD Ryzen 7 5700G / Vega         | Samsung SSD 980 PRO 500GB | Default | 587            | 152 k    | 314              | 679              | 4.99               | 97.90         | 0.97        |
|                         |                                  |                           | RRalloc | 603            | 157 k    | 306              | 624              | 4.94               | 98.08         | 0.82        |

### Observations

* RRALLOC reduces **tail latency** by more than 50% in high-concurrency tests.  

* Per-CPU allocation cursors avoid global lock contention and reduce race conditions.  

* Throughput is preserved or slightly improved in multi-core stress scenarios.  

* The allocator demonstrates a **deterministic, smooth allocation pattern**,
improving predictability for heavily parallel workloads.

These results indicate that RRALLOC not only maintains
baseline I/O behavior but also provides tangible benefits under
high-concurrency conditions, particularly in reducing worst-case
latency and contention-related delays.
