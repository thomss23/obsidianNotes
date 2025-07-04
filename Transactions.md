Reads and writes in a transaction are treated as a single operation: either the entire operation succeeds (commit) or it fails (abort, rollback).

**ACID** = Atomicity, Consistency, Isolation and Durability
**BASE** = Basically Available, Soft state, and Eventual Consistency

## Atomicity 

In multi threaded programming, if one thread executes an atomic operation, that means there is no way that another thread could see the half-finished result of the operation. The system can only be in the state it was before the operation or after the operation, not something in between.

By similar analogy, ACID atomicity describes what happens if a client wants to make several writes, but a fault occurs after some of the writes have been processed. Without atomicity, if an error occurs partway through making multiple changes, it’s difficult to know which changes have taken effect and which haven’t.

## Consistency

ACID Consistency refers to the idea that you have certain statements about your data (*invariants*) that must always be true.
However, this idea of consistency depends on the application’s notion of invariants,
and it’s the application’s responsibility to define its transactions correctly so that they
preserve consistency.

**Atomicity, isolation, and durability are properties of the database, whereas consistency**
**(in the ACID sense) is a property of the application.**


## Isolation

Isolation in the sense of ACID means that concurrently executing transactions are
isolated from each other: they cannot step on each other’s toes.
You can think of it as if each transaction pretends that it is the only one running on the entire database.


## Durability

Durability is the promise that once a transaction has committed successfully, any data it has written will not be forgotten, even if there is a hardware fault or the database crashes.

Historically, durability meant writing to an archive tape. Then it was understood as
writing to a disk or SSD. More recently, it has been adapted to mean replication.


## The need for multi-object transactions

When?

- In a RDBMS, a row in one table often has a foreign key reference to a row in another table. Such multi obj transactions allow you to ensure that these references remain valid.
- Document databases lacking join functionality also encourage denormalization. When denormalized, information needs to be updated so you need to update several documents in one go. Transactions are useful to prevent denormalized get out of sync.
- In databases with secondary indexes. The indexes also need to be updated every time you change a value.

## Handling errors and aborts

test