1) Take a consistent snapshot of the leader's data without taking a lock on the entire database.
2) Copy the snapshot to the new follower
3) The follower connects to the leader and requests all the data since the snapshot to catch up. This requires that the snapshot is associated with the leader's replication log.
4) When the follower has processed the backlog of data since the changes in the snapshot, we say it has caught up. It can now proceed to process changes from the leader as they happen.