# rralloc allocator geometry and performance

Included files show detailed information; this is the "long story short" summary.

The maps below show three successive copies of the same file, written to
`/dev/sda5` (block size 4096 bytes, 32768 blocks per group) with the
**`rralloc` mount option enabled**.

| First copy | Second copy | Third copy |
|:-:|:-:|:-:|
| ![First copy](4%29%20allocation%20map.svg) | ![Second copy](6%29%20allocation_map.svg) | ![Third copy](8%29%20allocation%20map.svg) |

## What the maps show

Each square represents a block group; color indicates its state at the time
of the snapshot (free space, in-use/metadata blocks, and the target file's
own blocks) — same legend as the regular-allocator maps, for direct
comparison.

* On the **first copy**, placement looks similar to the regular allocator:
  a small, unremarkable footprint, since there is no prior allocation
  history yet to rotate away from.
* On the **second and third copies**, the picture changes compared to the
  regular allocator: instead of the target file's blocks re-landing in the
  *same* narrow band used by the previous copy, `rralloc` starts each new
  stream allocation from a **rotated starting block group**, drawn from a
  per-CPU dedicated LBA zone. New writes are steered away from whichever
  region was hot a moment ago, rather than being pulled back into it.

## Why this matters

This is the direct contrast to the **in-place overwrite** pattern seen with
the regular allocator [(see `1) regular allocator/README.md`)](../1%29%20regular%20allocator/README.md): there, repeated
copies/overwrites kept re-concentrating in the same block groups, because
goal selection is influenced by the previous allocation state. That
concentration is what produces allocation contention and hotspotting under
overwrite-heavy workloads.

With `rralloc` enabled:

* **No persistent hot region** — the rotating starting point means
  consecutive copies are not steered back into the same block groups copy
  after copy.
* **Contention is spread, not concentrated** — allocation pressure from
  repeated writes/copies is distributed across per-CPU LBA zones instead of
  piling onto a single narrow band, reducing the odds of tasks contending
  over the same hot groups.
* **Stream allocation is preserved** — within a single file/stream, blocks
  are still allocated sequentially (intra-file locality is maintained via
  the per-inode atomic cursor); it's the *starting point* for each new
  stream that rotates, not the sequential nature of a single file's layout.

As with the regular-allocator test, this doesn't change on-disk format or
allocator semantics — it only changes *where* new stream allocations start,
which is enough to avoid the same-region reallocation pattern that the
regular allocator exhibits under repeated overwrite/copy workloads.
