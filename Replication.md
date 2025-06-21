Why should we replicate data?
- to keep data geographically close to your users -> reduce latency
- to allow the system to continue working even if some parts have failed
- to scale out the number of machines that can serve read queries -> increase read throughput

Strategies for replicating data:
- Single-leader
- Multi-leader
- Leaderless

Tradeoffs need to be considered when choosing the replication strategy. An example would be when to choose [[Synchronous vs Asynchronous Replication]] and how to handle failed replicas. 

## Leaders and followers

When we have multiple replicas, we need to ensure that all the data ends up on all the replicas.
Every write to the database must be processed by all the replicas, otherwise they won't contain the same data. 

The most common solution would be the [[Leader-Based Replication]] (aka *active/passive* or *master-slave replication*)

## Setting up new followers

When adding new followers we need to ensure that the new follower has an accurate copy of the leader's data.

Simply copying data from one node to another is typically not sufficient: clients are constantly writing to the database, and the data is always in flux, so a standard file copy would see different parts of the database at different points in time. The process of [[Setting up new followers]] can be done without downtime.

## Handling node outages

Our goal is to keep the system as a whole running despite individual note failures + keep the impact of a node outage as small as possible.

