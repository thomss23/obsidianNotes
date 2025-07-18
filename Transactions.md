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

=====

## Transaction Isolation types:

### Read committed: 
- when reading from the db, you will only see data that has been committed (no dirty reads)
- when writing to the db, you will only overwrite data that has been committed (no dirty writes)

### Snapshot isolation
The idea is that each transaction reads from a consistent snapshot of the database—that is, the transaction sees all the data that was committed in the database at the start of the transaction.
Even if the data is subsequently changed by another transaction, each transaction sees only the old data from that particular point in time.

### Rules for observicing a consistent snaphot
Put another way, an object is visible if both of the following conditions are true:
• At the time when the reader’s transaction started, the transaction that created the
object had already committed.
• The object is not marked for deletion, or if it is, the transaction that requested
deletion had not yet committed at the time when the reader’s transaction started


### Preventing Lost Updates

The lost update problem occurs if an app reads some value from the db, modifies it and writes back the modified value. IF 2 transactions do this concurrently, one of the modifications can be lost, because the second write does not include the first modification.

**Solutions:**

#### Atomic write operations

Many databases provide atomic update operations, removing the need to implement read-modify-write cycles in application code.

They are usually implemented by taking an exclusive lock on the object when it is read so that no other transaction can read it until the update has been applied. This is called *cursor stability*.

Another option would be to simply force all atomic operations to be executed on a single thread.


#### Explicit locking

If the database doesn't support such atomic operations, it is the duty of the application instead to explicitly lo ck objects that are going to be updated.


### Write Skews

![[Pasted image 20250718161018.png]]![[Pasted image 20250718161942.png]]

![[Pasted image 20250718162141.png]]

### Serializability

![[Pasted image 20250718162357.png]]


### Pros and cons of stored procedures

![[Pasted image 20250718163327.png]]

###  Two phase locking

![[Pasted image 20250718164319.png]]

**Implementing 2PL**

![[Pasted image 20250718164423.png]]


### Index Range Locks