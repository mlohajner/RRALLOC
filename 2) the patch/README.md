# RRALLOC: Optional Allocation Policy for EXT4

RRALLOC is an optional allocation policy designed to explore alternative
allocation geometry in EXT4, while preserving all existing allocator
behavior and performance characteristics.
It introduces deterministic allocation patterns and reduced contention
under parallel workloads.

## Key Features

1. **Enforcing Stream Allocation for All Files**  
   Ensures sequential allocation to maximize throughput and reduce
   fragmentation for write-heavy workloads.

2. **Deterministic Per-Inode Cursors/Goals**  
   - Avoids the need for the global goals hash array.  
   - Ensures predictable and stable allocation behavior per inode.  

3. **Per-CPU RRALLOC Cursors/Goals**  
   - Reduces contention in high-concurrency environments.  
   - Avoids race conditions without introducing global locks.  

All other allocation heuristics remain as in the original EXT4 allocator,
ensuring stability and compatibility.

## Usage

RRALLOC is fully optional and does not change the default behavior of
the EXT4 allocator.
It can be enabled per mount or as a patch for experimental evaluation.

## Notes

- Initially motivated by wear-leveling considerations, but its main
contribution is **alternative allocation geometry** across the LBA space.

- Provides small occasional performance gains and potential benefits for
certain high-concurrency or sequential workloads.  

- Ideal for testing or exploring allocator strategies without affecting
the original allocator’s correctness.
