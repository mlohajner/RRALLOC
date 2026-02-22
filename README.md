# RRALLOC
Originally named "rotalloc" – now renamed to RRALLOC:
* "rotalloc" was implying rotational storage media, but this policy is in concept orthogonal to that → RRALLOC
* In concept, it clearly exhibits round-robin behavior, hence the name change
* This patch is V2

Support for the round-robin allocation policy as a new mount option.
The policy rotates the starting block group for new allocations across
per-CPU dedicated LBA zones.

The policy is selected via a mount option (rralloc) and does not change
the on-disk format.

It enforces existing stream allocation behavior (for all files) to
help distribute allocations across block groups in a more sequential manner.

The rotating allocator is implemented as a separate allocation path
selected at mount time. This avoids conditional branches in the regular
allocator and keeps allocation policies isolated.
This also allows the rotating allocator to evolve independently in the
future without increasing complexity in the regular allocator.

The policy was tested using v6.18.9 stable locally with the new mount
option "rrlloc" enabled, confirmed working as described!

NOTE: "proof of concept" showcase!
