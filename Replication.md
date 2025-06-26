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

### Follower failure: Catchup recovery

Followers keep a log of the data changes they received from the leader.
If a follower crashes, given this log, they know the last transaction that was processed before the fault occured => they can connect to the leader and request all the data changes since they were down.

When the changes are applied, they are caught up and continue receiving streams of data changes as before.

### Leader failure: Failover

What happens when the leader crashes?

1) First we need to determine that the leader has failed (crashes, power outages, network issues etc.). Most systems simply use a timeout to determine this: nodes frequently bounce back messages back and forth between each other and if a node doesn't respond for some time, it is assumed to be dead.
2) Choosing a new leader. Getting all nodes to agree on a new leader is a consensus problem. The best candidate for the leadership is usually the replica with the most up to date data changes from the old leader.
3) Reconfiguring the system to use the new leader. The system needs to ensure that the old leader becomes a follower.


Examples of what can go wrong with failovers:
- if an async replication is used, the new leader may not have received all the writes from the old leader before it failed. If the former old leader rejoins after the new leader has been chosen, what should happen to those writes? The most common solution is for the old leader's unreplicated writes to simply be discarded, not good for clients' durability expectations.
- 2 nodes both believe they are the leader: if both leaders accept writes, there is no process for resolving conflicts. A safety mechanism is to manually shut down one node if 2 leaders are detected.
- determining the right timeout before a leader is declared dead.

## Implementation of Replication Logs

- Statement based replications
- Write ahead log: tied to the storage engine
- Logical (row-based) replication: a sequence of records describing writes to the database tables at the granularity of a row.
- Trigger based replication: move up the replication logic to the application layer. An alternative is to use features available in many relational databases: triggers and stored procedures.


## Replication lag 

These are caused because of staling replicas, and introduces the notion on *eventual consistency*. Because at some point in time the stale follower will catchup, as the term "eventually" implies that the in general, there is no limit to how far a replica can fall behind.

![[Pasted image 20250622231936.png]]

Techniques to prevent this:
- When reading something that the user may have modified, read it from the leader. Otherwise, read it from the replica. This requires that you have some way of knowing whether something might have been modified, without actually querying it.
- You could track the time between the last update, and for one minute after the last update, make all the reads from the leader.

![[Pasted image 20250622232507.png]]

This can be solved using [[Monotonic Reads]]


![[Pasted image 20250622233016.png]]

Preventing this kind of anomaly requires another type of guarantee: [[Consistent prefix reads]] . This guarantee says that if a sequence of writes happens in a certain order,
then anyone reading those writes will see them appear in the same order.****


Another strategy worth discussing is the [[Multi-Leader Replication]].