Some data store systems abandon the idea of a leader that accepts writes and allow any replica to directly accept writes from the clients.

## Writing to the Database when a Node is Down

![[Pasted image 20250627115646.png]]

When the client sends a write request, it gets sent to all of the replicas. In our example, one of the replicas is down and will miss the request. After the user received two *ok* responses, we consider the write to be successful. The client ignores the fact that one of the replicas missed the write.

The problem is when the unavailable node comes back to life, and responds with outdated data.
To solve this issue, when a client reads from the database, it doesn't just send its request to one replica: *read requests are also sent to several nodes in parallel.* Version numbers are used to determine which value is newer.

### Read repair and anti-entropy

Two mechanisms are often used in Dynamo-style datastores:

*Read repair*
The client sees that a stale replica replies with an outdated value and writes the newer value to that replica. This works well for values that are frequently read.

*Anti-entropy process*
Some datastores have a background process that constantly looks for differences in the data between replicas and copies any missing data from one replica to another.


More generally, if there are n replicas, every write must be confirmed by w nodes to
be considered successful, and we must query at least r nodes for each read. (In our
example, n = 3, w = 2, r = 2.) As long as w + r > n, we expect to get an up-to-date
value when reading, because at least one of the r nodes we’re reading from must be
up to date. You can think of r and w as the minimum number of votes required
for the read or write to be valid.


## Multi-datacenter operation

Leaderless replication is also suitable for multi-datacenter operation, since it is designed to tolerate conflicting concurrent writes, network interruptions and latency spikes.

## Detecting Concurrent Writes

![[Pasted image 20250627130427.png]]

One solution as discussed previously is the last write wins method, attaching timestamps on the writes and figuring out which one was the last received. LWW achieves the goal of eventual convergence, but at the cost of durability: if there are several concurrent writes to the same key, even if they were all reported as successful to the client (because they were written to w replicas), only one of the writes will survive and the others will be silently discarded. Moreover, LWW may even drop writes that are not concurrent. **If losing data is not acceptable, LWW is a poor choice for conflict resolution**.

The only safe way of using a database with LWW is to ensure that a key is only written
once and thereafter treated as immutable, thus avoiding any concurrent updates
to the same key. For example, a recommended way of using Cassandra is to use a
UUID as the key, thus giving each write operation a unique key.

An operation A happens before another operation B if B knows about A, or depends
on A, or builds upon A in some way. Whether one operation happens before another
operation is the key to defining what concurrency means. In fact, we can simply say
that two operations are concurrent if neither happens before the other (i.e., neither
knows about the other).

Thus, whenever you have two operations A and B, there are three possibilities: either
A happened before B, or B happened before A, or A and B are concurrent.