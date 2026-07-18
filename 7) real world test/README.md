# Linux Kernel Tree — Real-World Example – Direct Comparison

## Workload

* **Source:** Linux kernel tree
* **Files:** ~180,000
* **Total size:** ~27 GiB
* **Operation:** full tree copy + repeated overwrite cycles

Two runs of the same workload are compared side by side:

* [`1) regular allocator`](1%29%20regular%20allocator/README.md) — baseline
  ext4 allocator, unmodified.
* [`2) rralloc allocator`](2%29%20rralloc%20allocator/README.md) — same
  workload, with the `rralloc` mount option enabled.

## Observations

With the **regular allocator**, repeated copy/overwrite cycles keep
steering new allocations back into the same block groups used by the
previous copy. The allocation maps show this as a persistent hot band that
re-lights up copy after copy — an in-place overwrite pattern that
concentrates allocation pressure into a narrow region of the LBA space
instead of spreading it across the device.

With **`rralloc` enabled**, round-robin allocation is confirmed across the
entire LBA space:

* Files "float" across the LBA rather than settling back into the same
  region on every copy — but they do **not** fragment; stream allocation
  and intra-file locality are preserved.
* Allocation maps are consistent in shape and size compared to the regular
  allocator, so this isn't a throughput or layout trade-off — it's a
  difference in *where* new streams start, not in how a single file is laid
  out.
* In-place overwriting is visibly reduced: instead of the same block
  groups being repeatedly hammered, allocation pressure is distributed
  across per-CPU LBA zones, which lowers the odds of concurrent allocations
  contending over the same hot region.

## Conclusion

Across a real, full-size workload (not just synthetic tests), `rralloc`
delivers what it set out to do: an optional, round-robin allocation policy
that preserves baseline performance and existing allocator heuristics,
while improving allocation distribution and reducing the in-place
overwrite / allocation-contention pattern seen with the regular allocator
under repeated-copy and overwrite-heavy workloads — with predictable
behavior in both synthetic and real-world scenarios.
