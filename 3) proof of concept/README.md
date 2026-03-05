# Proof of Concept

We performed allocation pattern tests using files of different sizes to
observe the effects of RRALLOC:

* **Small file** – 32 KiB  
  Minimal fragmentation observed; allocations generally remain within
  the first few block groups.

* **Medium/Large file** – 100 MiB  
  Filefrag analysis shows that RRALLOC rotates allocations across
  per-CPU LBA zones, spreading blocks across multiple block groups.

* **Large/Huge file** – 8.5 GiB  
  Extensive sequential allocation across the entire LBA space,
  demonstrating the round-robin policy in action and reducing potential
  contention hotspots.

These tests confirm that RRALLOC preserves sequential stream allocation
while introducing an alternative allocation geometry, without regressing
on the existing allocator's performance.
