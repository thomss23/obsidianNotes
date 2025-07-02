This refers to breaking up data into partitions or shards -> each piece of data (record, row or document) belongs to exactly one partion.

In effect, each partition is a small database of its own, although the database may support operations that touch multiple partitions at the same time.

Why do we want to partition data?
For *scalability*. Different partitions can be placed on different nodes in a shared-nothing cluster. Thus, a large dataset can be distributed across many disks, and the query load can be distributed across many processors.

![[Pasted image 20250627135023.png]]


## Partitioning Key-Value Data

If the partitioning is unfair, so that some partitions have more data or queries than others, we call it *skewed*.

A partition with disproportionately high load is called a *hot spot.*


## Partitioning by Key Range

One way is to assign a continuous range of keys (from some minimum to some maximum) to each partition. If you know the boundaries between the ranges, you can easily determine which partition contains a given key.


## Partitioning by Hash Of Key

Once you have a suitable hash functions for keys, you can assign each partition a range of hashes (rather than a range of keys), and every key whose hash falls within a partition’s range will be stored in that partition. This technique is good at distributing keys fairly among the partitions. The partition boundaries can be evenly spaced, or they can be chosen pseudorandomly (in which
case the technique is sometimes known as *consistent hashing*).

## Skewed workloads and relieving hot spots

You can reduce skewness from the application code. Example: You can add a random number to the beginning or the end of key to split writes across 100 different keys.

## Partitioning Secondary Indexes by Document


