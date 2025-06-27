This refers to breaking up data into partitions or shards -> each piece of data (record, row or document) belongs to exactly one partion.

In effect, each partition is a small database of its own, although the database may support operations that touch multiple partitions at the same time.

Why do we want to partition data?
For *scalability*. Different partitions can be placed on different nodes in a shared-nothing cluster. Thus, a large dataset can be distributed across many disks, and the query load can be distributed across many processors.

![[Pasted image 20250627135023.png]]


## Partitioning Key-Value Data

