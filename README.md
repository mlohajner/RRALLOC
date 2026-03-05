# RRALLOC (formerly "rotalloc")

RRALLOC, originally named *rotalloc*,
has been renamed to better reflect its behavior:

* The name "rotalloc" implied rotational (HDD) storage, whereas this
policy is **not** optimized for rotational devices.  

* Conceptually, it exhibits **round-robin allocation** behavior,
hence the new name: RRALLOC (Round-Robin Allocator).  

* This is **V2** of the patch, slightly more elaborate than the initial
trivial V1.

## Overview

This patch introduces an optional **round-robin allocation policy**
as a new EXT4 mount option (`rralloc`).  

Key characteristics:

* Rotates the starting block group for new allocations
across **per-CPU dedicated LBA zones**.  

* Enforces **stream allocation** for all files, helping distribute
allocations sequentially and evenly across block groups.  

* Implemented as a **separate allocation path**, avoiding additional
conditional branches in the regular allocator.  

* Does **not modify the on-disk format**; safe to enable without
affecting existing data.  

* Allows the rotating allocator to evolve independently, keeping the
main allocator simple and stable.

## Testing

The policy was tested locally on **v6.18.9-stable** with the 'rralloc'
mount option enabled.  

Results confirmed that allocations rotate as expected,
maintain stream behavior, and preserve performance and correctness of
the existing allocator.
---
RRALLOC is intended as an **optional, experimental allocation policy**
to explore alternative allocation geometry and parallel allocation
strategies without impacting the default EXT4 behavior.
