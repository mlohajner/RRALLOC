# RRALLOC (formerly "rotalloc")

RRALLOC is an optional ext4 allocation policy exploring alternative
allocation geometry and reduced concurrency contention, preserving all
existing heuristics and semantics.

Originally named *rotalloc*, has been renamed to better reflect
its behavior:

* The name "rotalloc" implied rotational (HDD) storage, whereas this
  policy is **not** optimized for rotational devices (SSD/NVMe).
* Conceptually, it exhibits **round-robin allocation** behavior,
  hence the new name: RRALLOC (Round-Robin Allocator).

---

## NEWS (V3)

* **Tested and validated against Fedora's kernel 7.1.3.** [The patch](2) the patch) is
  directly applicable to the Fedora 7.1.3 kernel tree with no
  additional adaptation required.
* The regular allocator path has been modified in line with the
  "additional case study" (point 6): it no longer relies on a **hash
  array of global goals**. Instead, stream-mode allocation now uses a
  **per-inode atomic cursor** to track intra-file locality, replacing
  the previous global lookup structure.
* This is **V3** of the patch, building on the V2 round-robin
  allocation design.

---

## Overview

This patch introduces an optional **round-robin allocation policy**
as a new EXT4 mount option (`rralloc`).

Key characteristics:

* Rotates the starting block group for new allocations
  across **per-CPU dedicated LBA zones**.
* Enforces **stream allocation** for all files, helping distribute
  allocations sequentially and evenly across block groups.
* Intra-file locality during stream-mode allocation is now tracked via
  a **per-inode atomic cursor**, rather than a hash array of global
  goals — reducing shared-state contention and improving locality
  accuracy per file.
* Implemented as a **separate allocation path**, avoiding additional
  conditional branches in the regular allocator.
* Does **not modify the on-disk format**; safe to enable without
  affecting existing data.
* Allows the rotating allocator to evolve independently, keeping the
  main allocator simple and stable.

## Testing

The policy was tested locally on **v6.18.9-stable** (V1/V2 baseline)
and has now been further tested and validated against **Fedora's
kernel 7.1.3**, with the `rralloc` mount option enabled. The patch
applies **directly** to the Fedora 7.1.3 kernel tree.

Results confirmed that:

* Allocations rotate as expected across per-CPU LBA zones.
* Stream allocation behavior is preserved.
* The new per-inode atomic cursor correctly maintains intra-file
  locality without relying on the previous global hash-array lookup.
* Performance and correctness of the existing allocator are preserved.

---

RRALLOC is intended as an **optional, experimental allocation policy**
to explore alternative allocation geometry and parallel allocation
strategies without impacting the default EXT4 behavior.
