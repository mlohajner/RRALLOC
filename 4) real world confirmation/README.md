# Real world example

Workload:

Source: Linux kernel tree
Files: ~180 k
Total size: ~27 GiB

Operation: full tree copy + repeated overwrite cycles

Confirms round-robin allocation through the LBA area.
Files are floating around the LBA but not fragmenting

Allocation maps showing same shape and size compared to the
regular allocator. In-place overwriting reduced!
