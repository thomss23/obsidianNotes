![[Pasted image 20250622000826.png]]

What is the difference when we say synchronous and asynchronous?

In the picture above, the replication to *follower 1* is synchronous , while the replication to *follower 2* is asynchronous. This means that in the case of follower 1, the leader will wait its ok before reporting success to the user. In the case of follower 2, it will reply to the user before the waiting for a response from it.

An advantage of synchronous replication is that the follower is guaranteed to have all the data up to date exactly as the leader. If the leader suddenly fails, we can be sure that the data is still available from the follower.

The disadvantage is that if the synchronous follower does not respond, the write cannot be processed. Any other writes will be blocked by the leader until the synchronous replica is available again. Therefore, it is impractical for all the followers to be synchronous.