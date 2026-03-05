# Real-World Example – Linux Kernel Tree

Workload:
Source: Linux kernel tree
Files: ~180 000
Total size: ~27 GiB

Operation:
full tree copy + repeated overwrite cycles

Observations:
Round-robin allocation confirmed across entire LBA space.
Files “float” across the LBA but do not fragment.
Allocation maps show consistent shape and size compared to the regular
allocator.

In-place overwriting is reduced, demonstrating a smoother and more
balanced allocation pattern in practical scenarios.

Conclusion:
RRALLOC provides an optional, round-robin allocation policy that
preserves baseline performance, improves allocation distribution,
reduces contention under high concurrency, and offers predictable
behavior for both synthetic and real-world workloads.
