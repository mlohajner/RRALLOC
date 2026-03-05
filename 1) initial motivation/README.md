# THE ISSUE

These snapshots come from different systems and are presented as-is,
without annotations.  

While they do not directly show the allocation bitmap, they are
statistically correlated with the most frequently used blocks and block
groups across the LBA space in long periods of time.

Analysis of this data—both qualitative and quantitative—may leave room
for debate regarding methodology or exact measurements. However,
the patterns observed suggest that certain areas of the LBA space tend
to be more heavily allocated than others.

**Initial motivation** for RRALLOC was inspired by wear-leveling concepts:
distributing allocations more evenly across the device.  

Over time, the focus evolved into providing
an **alternative allocation geometry** at the filesystem level,
independent of underlying hardware.  

**Note:**  
The goal is not to solve wear leveling per se, but to offer RRALLOC as
an optional mount-time policy (disabled by default) that can help
distribute allocations more evenly across block groups, improving
predictability and potentially mitigating contention hotspots.
