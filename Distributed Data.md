
Why should you distribute a database across multiple machines:

- Scalability: spread load across multiple machines
- Fault tolerance: use multiple machines to give you redundancy 
- Latency: have servers at various locations around the world near users

**Types of architectures**:
- *Shared disk architecture* - uses several machines with independent CPUs and RAM , but stores data on an array of disks that is shared between the machines, which are connected via a fast network.
- *Shared-nothing architectures* (aka horizontal scaling) - each machine or virtual machine running the database software is called a node. Each node uses its CPUs RAN and disk independently. Coordination between the nodes is done at the software level using a conventional network.


### [[Replication]] vs [[Partitioning]]

* Replication:
	- keeping a copy of the same data on several different nodes, potentially in different locations
	- replication provides redundancy - if some nodes are unavailable, the data can still be served from the remaining nodes
	- it can also help improve performance
- Partitioning:
	- splitting the database into smaller subsets called partitions, so different partitions can be assigned to different nodes (aka *sharding*).