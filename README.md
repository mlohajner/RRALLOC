# RRALLOC
Originaly named "rotalloc" -now renamed to RRALLOC:
	1) rotalloc was implying rotational storage media, since this
		policy is in condept orthogonal to that -> RRALLOC
	2) in concept clearly round-robin behavior so why not call it accordingly
	3) this patch is V2

Support for the round-robin allocation policy as a new mount option.
Policy rotates the starting block group for new allocations across per-cpu
dedicated LBA zones.

The policy is selected via a mount option (rralloc) and does not change
the on-disk format.

It enforces existing stream allocation behavior (for all files) to
help distribute allocations across block groups in a more sequential
manner.

The rotating allocator is implemented as a separate allocation path
selected at mount time. This avoids conditional branches in the regular
allocator and keeps allocation policies isolated.
This also allows the rotating allocator to evolve independently in the
future without increasing complexity in the regular allocator.

The policy was tested using v6.18.9 stable locally with the new mount
option "rotalloc" enabled, confirmed working as desribed!
NOTE: proof of concept
