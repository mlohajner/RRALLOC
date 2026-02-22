# RRALLOC
Originally named "rotalloc" – now renamed to RRALLOC:
* "rotalloc" implied rotational storage, but this policy is not optimized for rotational devices.
* Conceptually, it clearly exhibits round-robin behavior, hence the rename to RR (Round Robin).
* This patch is V2, slightly more elaborate than the otherwise trivial V1.

This patch introduces support for a round-robin allocation policy as a new mount option.
The policy rotates the starting block group for new allocations across per-CPU dedicated LBA zones.
It is selected via the mount option (rralloc) and does not modify the on-disk format.

The policy enforces existing stream allocation behavior (for all files) to help distribute
allocations across block groups in a more sequential and balanced manner.

The rotating allocator is implemented as a separate allocation path selected at mount time.
This avoids introducing additional conditional branches into the regular allocator and keeps
allocation policies isolated. It also allows the rotating allocator to evolve independently in
the future without increasing the complexity of the regular allocator.

The policy was tested locally on v6.18.9-stable with the new mount option (rralloc) enabled
and was confirmed to work as described.

NOTE:
Results in "proof of concept"!
