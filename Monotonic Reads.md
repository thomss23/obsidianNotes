One way of achieving monotonic reads is to make sure that each user always makes
their reads from the same replica (different users can read from different replicas).
For example, the replica can be chosen based on a hash of the user ID, rather than
randomly. However, if that replica fails, the user’s queries will need to be rerouted to
another replica.