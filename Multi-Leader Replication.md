A model that allows more than one node to accept writes. Replication still happens in the same way: each node that processes a write must fotward that data change to all the other nodes.
## Use Cases

### Multi-datacenter operation

You can have a leader in each datacenter.

![[Pasted image 20250626215414.png]]

What are the benefits?
- *Performance* - every write can be processed in the local datacenter and is replicated asynchronously
- *Tolerance of datacenter outages* - In a multi-leader configuration, each datacenter can continue operating independently of the others, and replication catches up when the failed datacenter comes back online
- *Tolerance of network problems* - A multi-leader configuration with asynchronous replication can usually tolerate network problems better: a temporary network interruption does not prevent writes being processed

**Downside** -> some data may be concurrently modified in two different datacenters, and those write conflicts must be resolved. Examples: collaborative editing on a software like Google Docs, Clients with offline operations such as a calendar that needs to store meetings etc.

## Handling Write Conflicts

![[Pasted image 20250626220225.png]]

There can be synchronous or asynchronous conflict detection.

In a single leader database, the second writer will be blocked or wait for the first write to complete, or completely abort the second write transaction, forcing the user to retry the write.


How to converge toward a consistent state when handling conflicts?

- Give each write a unique ID (e.g a timestamp, a long random number, UUUID), pick the write with the highest ID as the winner, and throw away the other writes. Downside is that is dangerously prone to data loss.
- Give each replica a unique ID and let writes that originated at a higher replica always take precedence over writes that originated at a lower-numbered replica. This approach also implies data loss.
- Somehow merge the values together
- Record the conflict in an explicit data structure that preserves all information, and write app code that resolves the conflict at a later time (e.g by prompting the user)

### Custom conflict resolution logic

Most multi leader replication tools let you write conflict resolution logic using application code.

**On write**
As soon as the db system detects a conflict in the log of replicated changes, it calls the conflict handler.

**On read**
When a conflict is detected, all conflicting writes are stored. The next time the data is read, these multiple versions of the data are returned to the application. The application may prompt the user or automatically resolve the conflict, and write the result back to the database.

## Multi-Leader Replication Topologies

![[Pasted image 20250626223450.png]]

Circular topology -> each node receives writes from one node and forwards the writes
Star topology -> one designated root node forwards writes to all of the other nodes

A problem with circular and star topologies is that if just one node fails, it can interrupt
the flow of replication messages between other nodes, causing them to be unable
to communicate until the node is fixed.

![[Pasted image 20250626223923.png]]

To order these events correctly, a technique called *version vectors*
