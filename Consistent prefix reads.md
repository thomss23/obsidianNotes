If the database always applies writes in the same order, reads always see
a consistent prefix, so this anomaly cannot happen. However, in many distributed different partitions operate independently, so there is no global ordering of
writes: when a user reads from the database, they may see some parts of the database
in an older state and some in a newer state.

One solution is to make sure that any writes that are causally related to each other are
written to the same partition—but in some applications that cannot be done efficiently.
There are also algorithms that explicitly keep track of causal dependencies