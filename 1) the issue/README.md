# THE ISSUE

These are snapshots from different systems, provided as-is and without
annotations.
While they do not explicitly show the allocation bitmap, they are
statistically correlated with the most frequently used blocks and block
groups across the LBA space.

In both qualitative and quantitative analyses of this data, we may debate
the methods or exact measurements, but can we, with full confidence,
dismiss these observations as inconclusive?

NOTE:
We may argue about why this happens and what the causes and effects are.
However, we propose providing "rralloc" as an option (disabled by default)
to help mitigate this at a higher level (the ext4 file system level),
agnostic of the underlying hardware.
