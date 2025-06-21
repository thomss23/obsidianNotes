![[Pasted image 20250621235904.png]]

How this works:

1) One of the leaders is designated as the *leader*. When it receives new data, it will write it to its local storage.
2) The other replicas are known as *followers*. Whenever the leader writes new data to its storage, it also sends the data to its followers as part of a *replication log* or *change stream*. Each of the followers will write the data based on the log from the leader, writing all the data in the same order as it was processed by the leader.
3) When a client wants to query the database, it can query either the leader or any of the followers.

This kind of replication is a built-in feature among many of the relational databases and non relational ones.

This is not restricted to databases, distributed message brokers such as Kafka also use it. Some network and file systems as well.