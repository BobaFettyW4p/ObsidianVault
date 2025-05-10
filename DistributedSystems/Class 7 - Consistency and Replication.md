
## Agenda
- Data-centric consistency models
- client-centric consistency models
- replica management and consistency protocols

## Replication
- Data (or processes) are replicated for
	- reliability
		- continue working after a crash, protect against corruption, etc
	- performance
		- scaling in terms of system size, number of users, geographical distribution, etc
- as soon as we replicate we then face the challenge of keeping replicas *consistent*
	- when one copy is updated, we need to update the others
	- in some cases we don't need *absolute* consistency, it might be ok to read a stale copy

# Data centric consistency models
- consistency is typically discussed in the context of read/write operations on shared data in a distributed data store
	- how much staleness can we tolerate and still get a good result?
![[Pasted image 20250510075855.png]]


## Basic idea
- to keep replicas consistent, we need to ensure that all *conflicting operations* are done in the *same order everywhere*
- Conflicting operations:
	- read-write conflict: a read operation and a write operation act concurrently
	- write-write conflict: two concurrent write operations
- guaranteeing global ordering on conflicting operations may be a costly operation (and reduce scalability)
	- solution: weaken consistency requirements so that hopefully global synchronization can be avoided

### Consistency model
- a *consistency model* is a contract between processes and the data store
	- it says that if processes (or the data store) agree to obey certain rules, the store promises to work correctly
- normally, a process that performs a read operation on a data item expects the operation to return a value that shows the results of the last write operation on that data
- without a global clock, it's hard to know which operation was the last.... we use more relaxed consistency models restricting what can be returned

## Data-centric consistency models

### Continuous consistency
- if we loosen the consistency model, then we can exploit efficient replication solutions
	- but all applications are different and thus we should consider different ways of loosening consistency
- how can we loosen consistency? consider a degree of consistency
	- replicas may differ in their numerical value (e.g. +/- 5%)
	- replicas may differ in their relative staleness (e.g. weather reports are relevant for hours)
	- differences with respect to (number and order) of performed update operations (within some bound)
### Data-centric consistency models
- parallel computing has formalized consistency for concurrent access to shared data -> methods for consistency ordering of operations
- we need a way of representing data store access
	- `W(x)a`: process writes value `a` to `x`
	- `R(x)b`: process reads `x` and returns value to `b`
![[Pasted image 20250510080830.png]]


### Sequential consistency
> "The result of any execution is the same as if the operations of all processes were executed in some sequential order, and the operations of each individual process appear in this sequence in the order specified by its program"
> - Lamport

- Note: no mention of time, just that processes see the same order/interleaving of operations

#### Sequentially consistent?
![[Pasted image 20250510081326.png]]
- Since `W(x)a` on `P1` and `W(x)b` on `P2` have no direct correlation, we cannot determine definitively which starts and completes first (without additional information)
- Since `P3` and `P4` both read the value of `x` to be `b` and then later read it to be `a`, this is considered to be sequentially consistent
![[Pasted image 20250510081924.png]]
- conversely this system is not sequentially consistent
	- `P3` sees `W(x)b` completing before `W(x)a`
	- `P4` sees `W(x)a` completing before `W(x)b`
	- thus, the writes were considered to have completed in different orders on different processes and are not sequentially consistent


### Causal consistency
- remember we often don't care about sequential ordering, what we care about is ordering of events that are *causally related*
	- if event `a` is caused by event `b`, causality means everyone should see `b` first and then see `a`
- for a data store to be considered causally consistent, it is necessary the store obeys the following conditions:
	- *writes that are potentially causally related must be seen by all processes in the same order*
	- *concurrent writes may be seen in a different order on different machines*

![[Pasted image 20250510082713.png]]
- In this system, `P1` creates a causal relationship between `W(x)a` and `W(x)c`
	- namely, that `W(x)a` occurs before `W(x)c`
- similarly, `P2` establishes a relationship between `W(x)a` and `W(x)b`
	- that `W(x)a` occurs before `W(x)c`
- a system is causally consistent is all causal relationships are respected
	- on `P3`, both of these relationships are respected
		- `R(x)a` comes before `R(x)c`
		- `R(x)a` comes before `R(x)b`
	- same for `P4`
	- in this case, the system is not sequentially consistent because the same sequential order is not represented across all processes
		- i.e. on `P3` `R(x)c` comes before `R(x)b`, but on `P4`, `R(x)b` comes before `R(x)c`
![[Pasted image 20250510084309.png]]
- this system is not causally consistent
	- `P2` establishes a causal relationship between `a` and `b`
		- namely, `a`->`b`
			- this relationship is not seen on `P3`, thus making the system not causally consistent

### Eventual consistency
- concurrency is not always common
	- e.g. a database typically has many more reads than writes
	- e.g. on the web, pages are typically updated by one person and users typically tolerate cached data (proxies/browsers)
- **in the absence of write-write conflicts**, the state will eventually converge across replicas
- great for building performant systems

#### Why is there no "absolute global consistency"
> "All replicas in a distributed system see exact the same data, at exactly the same time, and all operations are applied instantaneously and atomically across all nodes"

This would require:
- instant communication
- perfect synchronization
- the absence of network partitions
- atomic operations across machines

- consider the eight fallacies of distributed computing

#### Summary
 - replication is necessary for performance and fault tolerance
 - replication leads to the challenge of keeping data consistent
	 - aim is to ensure that conflicting operations are done in the same order everywhere
- we can loosen consistency guarantees to improve performance
	- **sequential consistency** - all processes see the same order of all operations
	- **causal consistency** - writes that are potentially causally related must be seen by all processes in the same order. concurrent writes may be seen in a different order on different machines
	- **eventual consistency** - in the absence of write-write conflicts, all replicas will eventually converge toward identical copies of each other

# Client-centric consistency
## From data to client-centric consistency
- data-centric consistency aims to provide system-wide consistency across a data store
	- this is hard and we may be able to relax consistency requirements
- we consider a type of data store where we don't have many concurrent updates
	- when updates do happen, they can be easily resolved as they don't affect one another
		- mostly read-centric workloads
- we can apply various weak consistency models (e.g. eventual consistency) from the perspective of the client
![[Pasted image 20250510085246.png]]

### Brand new notation
- X$_i$ is the i`th` version of data item `x`
- W$_1$ (x$_2$): write operation by process P1 that leads to version x$_2$ of `x`
- W$_1$(x$_i$:x$_j$): process `P1` produces version x$_j$ based on x$_i$
- W$_1$(x$_i$|x$_j$): process `P1` produces version x$_j$ concurrently to version x$_i$
	- we don't know which follows which or if there's any dependency between them
- **L is a local data store**, rather than a process. Any process can access that data store
![[Pasted image 20250510090000.png]]


### Goal
- eventual consistency guarantee is
	- "given no updates, all clients will see exactly the same state of a system in sometime"
- if we stop doing writes, the system will (eventually) converge to some consistent state
	- this is a very weak constraint that is probably simpler to implement and could be very performant
		- not always possible

#### Monotonic reads
>if a process reads the value of a data item x, any successive read operation on x by that process will always return that same or a more recent value
- the read operations performed by a single process `P` at two different local copies of the same data store
![[Pasted image 20250510090729.png]]
- why example b is not a monotonic-read:
	- After P$_1$ has read x$_1$, it later reads x$_2$
	- the previous write W$_2$(x$_1$|x$_2$) produces an x$_2$ that is independent from x$_1$
		- thus, P$_1$'s read at L$_2$ is known not to include the effect of its own write when it reads at R$_1$ 
##### Examples and counterexamples
- monotonic:
	- calendar always has all events that you have previously seen
- not monotonic:
	- access to instant messenger has messages disappear

#### Monotonic writes
> a write operation by a process on a data item x is completed before any successive write operation on x by the same process
- if we have two successive operations W$_k$(x$_i$)` and `W$_k$(x$_j$) by process P$_k$, then regardless of where W$_k$(x$_j$) takes place, we also have W(x$_i$;x$_j$)
![[Pasted image 20250510091854.png]]
- example `b` is not monotonic because:
- P$_2$ produces a concurrent version to x$_1$ after it produces x$_3$ but concurrently to x$_1$
##### Examples and counterexamples
- monotonic writes:
	- all versions of replicated files are in the correct order everywhere
- non-monotonic writes:
	- message queue has messages in the wrong order based on what the process wrote

#### Read your writes
> the effect of a write operation by a process on data item x will always be seen by a successive read operation on `x` by the same process
![[Pasted image 20250510092321.png]]
- in example `b`, P$_2$ produces a version concurrently to x$_1$
	- the effects of the previous write operation by P$_1$ have not necessarily propagated to L$_2$ when x$_2$ was produced
		- when P$_1$ reads x$_2$, it will not see the effects of its own write operation at L$_1$
##### Examples and counterexamples
- read your writes?
	- you make an update to a web page, the consistency model ensures that when you reload the page it is the newest version
- read your writes?
	- you delete and email message but when you refresh the email the message returns
> "The effect of a write operation by a process on data item x will always be seen by a successive read operation on x by the same process"

#### Writes follow reads
> a write operation by a process on a data item x following a previous read operation on x by the same process is guaranteed to take place on the same or a more recent value of x that was read
![[Pasted image 20250510092530.png]]
- example `b` does not follow writes follow reads because:
	- P$_3$ produces a version x$_2$ concurrently to that of x$_1$
		- when P$_2$ updates x after reading x$_1$, it will be updating a version it has not read before
			- x$_2$ squashed x$_1$
##### Examples and counterexample
- writes follow reads?
	- imagine a user reads a news article and then posts a comment on it
	- the system guarantees that the *article (read)* will be visible *before* or *alognside* the *comment (write)* to all other users
	- no one should see the comment without also seeing the article it responds to - the comment depends on the article being visible
- writes follow reads?
	- imagine you go to a news article, and you go to the comments section
	- you read by some client A - e.g. asking a quesiton
		- you know the answer and respond
	- client C goes to the comments and sees your answer, but not the question from A

# Replica management and consistency protocols
### Replica management
- important to think about where, when and by whom replicas should be placed
- two related problems:
	- *where* should we place replica servers?
	- *how* should we place content on replicas?

### Choosing replica locations
- figure out the best `K` places out of `N` possible locations
	- select best location out of `N` for which the average distance to clients is minimal
		- then choose the next best location
			- computationally expensive
	- select the K$^{th}$ largest autonomous system (set of nodes **managed by the same organization**) and place a server at the best connected host
		- computationally expensive
	- position nodes in a `d`-dimensional geometric space, where distance reflects latency
		- identify the `K` regions with highest density and place a server in each
- not so much a concern these days as we have many cloud datacenters to easily place replicas

#### what and where to replicate?
- how do we determine what and where to replicate?
![[Pasted image 20250510093414.png]]

### Permanent replicas
- fixed replicas that are distributed when initially deployed
- ex. elastic load balancing for a website
	- each request is sent to a different replica
- ex. website mirroring
	- geographic distribution across the internet
	- clients choose from a list of mirrors

### Server-initiated replicas
- replicas are created when needed to improve performance
	- typically created by the owner/data store
- ex. web server replicas
	- a server that receives a burst of requests far from the server might want to deploy replicas in regions close to the requests
	- a heavily loaded server might add additional instances to reduce load
- each server keeps track of access counts per file, aggregated by considering server closest to requesting clients
	- number of accesses drops below deletion threshold D -> drop file
	- number of accesses exceeds replica threshold R -> replicate file
	- number of access between D and R the file may only be migrated
![[Pasted image 20250510093503.png]]
### Client initiated replicas
- commonly known as *caching*
	- local storage used for temporarily storing a copy of requested data
	- improve access to data that is *likely to be used again*
	- could be on the local machine or could be a nearby machine
		- institution cache
	- most often the server has no knowledge of the cache, and provides no influence on caching decisions
- big question is how long to keep data in a cache (when to invalidate)
	- either because data is stale or to make room for other data
- many approaches: LRU, FIFO, size-based

#### What content should replicate?
- how do we not determine what content to replicate? and how do we decide how it should be replicated?
- trade-offs
	- state vs operations
	- pull vs push

#### State vs operations
- what should be propagated
	- notification of update
		- just that the data has changed
		- common with cache invalidation models
		- uses little bandwidth, good when many more updates than reads
	- transfer data from one copy to another
		- most common approach
		- good when many more reads than writes
		- must update before the next read happens
	- send the update operation to other copies
		- each replica managed by process that keeps data up to date
		- minimal bandwidth, but more work on replica

#### Push vs pull
- **pushing updates**: server-initiated approach, in which update is propagated regardless whether target asked for it
	- efficient when many more reads than writes, i.e. we expect the replica to be read
- **pulling updates**: client-initiated approach, in which the client requests to be updated
	- efficient when many more writes than reads
![[Pasted image 20250510093945.png]]

##### Hybrid approach: leases
- dynamically switch between pulling and pushing using *leases*
	- a contract in which the server promises to push updates to the client until the lease expires
- make lease expiration time dependent on system's behavior
	- adaptive leases
	- **age-based leases**: an object that hasn't changed for a long time, will not change in the near future, so provide a long-lasting lease
	- **renewal-frequency based leases**: the more often a client requests a specific object, the longer the expiration time for that client will be
	- **state-based leases**: the more loaded a server is, the shorter expiration times become

### Primary-based protocols
- most applications apply easy to understand consistency models
	- issue is that developers will struggle to implement correct applications
- for sequential consistency most often primary-based protocols are used
	- each data item in the data store has an associated primary date
- two main models:
#### Remote write protocols
![[Pasted image 20250510094127.png]]
#### Local-write protocols
![[Pasted image 20250510094142.png]]
#### Replicated write protocols
- write operations carried out by multiple replicas at the same time
	- active replication: operation forwarded to all replicas
	- consistency protocols: majority voting
- read and write quorums
![[Pasted image 20250510094202.png]]
