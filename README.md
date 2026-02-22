# RRALLOC
Support for the round-robin allocation policy as a new mount option.
Policy rotates the starting block group for new allocations.

The policy is selected via a mount option and does not change the
on-disk format.
It preserves existing allocation heuristics within a block group
while distributing allocations across block groups in a more
deterministic sequential manner by promoting stream allocation.

The rotating allocator is implemented as a separate allocation path
selected at mount time. This avoids conditional branches in the regular
allocator and keeps allocation policies isolated.
This also allows the rotating allocator to evolve independently in the
future without increasing complexity in the regular allocator.

The policy was tested using v6.18.9 stable locally with the new mount
option "rotalloc" enabled, confirmed working as desribed!
