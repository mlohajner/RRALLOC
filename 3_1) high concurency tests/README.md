# High concurency tests
Preliminary observations under heavy multi-threaded workloads suggest
reduced contention effects, but this has not yet been fully characterized.

To provide a more concrete comparison, the table below summarizes average
bandwidth, IOPS, latency metrics, and system utilization across multiple 
hardware platforms, contrasting the default allocator with the experimental RRalloc.

While the results are preliminary, they illustrate consistent improvements
in throughput and latency behavior under high-concurrency conditions.


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

