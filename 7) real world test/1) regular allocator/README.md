# Regular allocator geometry and performance

Included files show detailed information; this is the "long story short" summary.

The maps below show three successive copies of the same file, written to
`/dev/sda5` (block size 4096 bytes, 32768 blocks per group) using the
**regular (unmodified) ext4 allocator**.

| First copy | Second copy | Third copy |
| --- | --- | --- |
| [First copy](4%29%20allocation_map.svg) | [Second copy](6%29%20allocation_map.svg) | [Third copy](8%29%20allocation_map.svg) |

## What the maps show

Each square represents a block group; color indicates its state at the time
of the snapshot (free space, in-use/metadata blocks, and the target file's
own blocks).

* On the **first copy**, the file's blocks are placed largely where the
  allocator's normal heuristics land them — a fairly small, expected
  footprint with no particular concentration.
* On the **second and third copies**, the target file's blocks are no
  longer spread according to fresh allocation logic — they **re-land in
  the same block groups used by the previous copy**. The concentration is
  visible in the maps as the same bands of block groups lighting up again,
  copy after copy.

## Why this is problematic

This is the regular allocator exhibiting an **in-place overwrite**
tendency: because the allocator's goal selection is influenced by the
previous allocation state (global goals, proximity heuristics), repeated
writes/copies of a file tend to be steered back into the *same* region of
the LBA space rather than being distributed across the full device.

Under overwrite-heavy or copy-heavy workloads, this produces:

* **Allocation contention** — repeated allocation/deallocation pressure
  concentrated on the same block groups, instead of spread across the
  device.
* **Hotspotting** — a small subset of block groups absorbs a
  disproportionate share of allocation activity over time, even though
  free space exists elsewhere on the device.
* **Increased contention on shared allocator state** for those specific
  groups under concurrent access, since multiple allocating tasks end up
  competing for the same hot region instead of naturally spreading out.

This behavior is functionally correct (it doesn't corrupt data or violate
allocator semantics) but it is exactly the geometry pattern that **rralloc**
targets: by rotating the starting block group per allocation across
per-CPU LBA zones, rralloc avoids this same-region reallocation pattern and
spreads repeated writes/copies evenly across the entire device.
