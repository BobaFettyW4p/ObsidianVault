
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
- as soon as we replicate we then face teh challenge of keeping replicas *consistent*
	- when one copy is updated, we need to update the others
	- in some cases we don't need *absolute* consistency, it might be ok to read a stale copy

# Data centric consistency models
- consistency is typically discussed int he context of read/write operations on shared data in a distributed data store
	- how much staleness can we tolerate and still get a good result?

## Basic idea
- to keep replicas consistent, we need to ensure that all **conflicting operations** are done in the same order everywhere
- Conflicting operations:
	- read-write conflict: a read operation and a write operation act concurrently
	- write-write conflict: two concurrent write operations
- guaranteeing global ordering on conflicting operations may be a costly operation (and reduce scalability)
	- solution: weaken consistency requirements so that hopefully global synchronization can be avoided

### Consistency model
- a *consistency model* is a contract between processes and the data store
	- it says that if processes agree to obey certain rules, the store promises to work correctly
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
- *there's a diagram, grab it*

### Sequential consistency
> "The result of any execution is the same as if the o"

- Note: no mention of time, just that processes see the same order/interleaving of operations

#### Sequentially consistent?
*there's a chart, grab it*


### Causal consistency
- remember we often don't care about sequential ordering, what we care about is ordering of events that are *causally related*
	- if event `a` is caused by event `b`, causality means everyone should see `b` first and then see `a`
- for a data store to be considered causally consistent, it is necessary the store obeys the following conditions:
	- writes taht are potentially causally related must be seen by all processes in the same order
	- concurrent writes may be seen in a different order on different machines

### Eventual consistency
- concurrency is not always common
	- e.g. a database typically has many more reads than writes
	- e.g. on the web, pages are typically updated by one person and users typically tolerate cached data (proxies/browsers)
- **in the absense of write-write conflices**, the state will eventually converge across replicas
- great for building performant systems

#### Why is there no "absolute global consistency"
> "All replicas in a distributed system see exact the same data, at exactly the same time, and"

This would require:
- instant communication
- perfect synchronizaiton
- the absense of network partitions
- atomic operations across machines

- consider the eight fallacies of distributed computing

#### Summary
 - replication is necessary for performance and fault tolerance
 - replication leads to the challenge of keeping data consistent
	 - aim is to ensure that conflicting operations are done in the same order everywhere
- we can loosen consistency guarantees to improve performance
	- **sequential consistency** - all processes see the

## Client-centric consistency
## From data to client-centric consistency
- aims to provide system-wide consistency across a data store
	- this is hard and we may be able to relax consistency requirements
- we consider a type of data store where we don't have many concurrent updates
	- when updates do happen, they can be easily resolved as they don't affect one another
		- mostly read-centric workloads
- we can apply various weak consistency models (e.g. eventual consistency) from the perspective of the client

*there's a chart, grab*

### Brand new notation
- `Xi` is the i^th version of data item `x`
- `W1(x2)`: write operation by process P1 that leads to version `x2` of `x`
- `W1(xi:xj)`: process `P1` produces version `xj` based on `xi`
- `W1(xi|xj)`: process `P1` produces version `xj` concurrently to version `xi`
	- we don't know which follows which or if there's any dependency between them
- **L is a local data store**, rather than a process. Any process can access that data store

### Goal
- eventual consistency guarantee is
	- "given no updates, all clients will see exactly the same state of a system in sometime"
- if we stop doing writes, the system will (eventually) converge to some consistent state
	- this is a very weak constraint that is probably simpler to implement and could be very performanyt
		- not always possible

#### Monotonic reads
- if a process reads the value of a data item x, any successive read operation on x by that process will always return that same or a more recent value
- the read operations performed by a single process `P` at two different local copies of the same data store

##### Examples and counterxamples
- monotonic:
	- calendar always has all events that you have previously seen
- not monotonic:
	- access to instant messenger has messages disappear

#### Monotonic writes
- a write operation by a process on a data item x is completed before any successive write operation on x by the same process
- if we have two successive operations `wk(xi)` and `Wk(xj)` by process `Pk`, then regardless of where `Wk(xj)` takes place, we also have `W(xi;xj)`

##### Examples and counterexamples
- monotonic writes:
	- all versions of replicated files are in the correct order everywhere
- non-monotonic writes:
	- message queue has messages in the wrong order based on what the process wrote

#### Read your writes
- the effect of a write operation by a process on data item x will always be seen by a successive read operation on `x` by the same process

##### Examples and counterexamples
- read your writes?
	- you make an update to a web page, the consistency model ensures that when you reload the page it is the newest version
- read your writes?
	- you delete and email message but when you refresh the email the message returns
> "The effect of a write operation by a process on data item x will always be seen by a successive read operation on x by the same process"

##### Writes follow reads
- a write operation by a process on a data item x following a previous read operation on x by the same process is guaranteed to take place on the same or a more recent value of x that was read

##### Examples and counterexample
- wrties follow reads?
	- imagine a user reads a news article and then posts a comment on it
	- the system guarantees that the *article (read)* will be visible *bfore* or *alognside* the *comment (write)* to all other users
	- no one shoudl see the comment without also seeing hte article it responds to - the comment depends on the article being visible
- writes follow reads?
	- imagine you go to a news article, and you go to the comments section
	- you read by some client A - e.g. asking a quesiton
		- you know the answer and respond
	- client C goes to the comments and sees your answer, but not the question from A

## Replica management and consistency protocols
### Replica management
- important to think about where, when and by whom replicas should be placed

- two related problems:
	- where should we place replica servers?
	- how should we place content on replicas?

### Choosing replica locations
- figure out the best `K` places out of `N` possible locations
	- select best location out of `N` for which the average distance to clients is minimal
		- then choose the next best location
			- computationally expensive
	- select the `Kth` largest autonomous system (set of nodes managed by the same organization) and place a server at the best connected host
		- computationally expensive
	- position nodes in a d-dimensional geometric space, where distance reflects latency
		- identify the `K` regions with highest density and place a server in each
- not so much a concern these days as we have many cloud datacenters to easily place replicas

#### what and where to replicate?
- how do we determine what and where to replicate?

### Permanent replicas
- fixed replicas that are distributed when initially deployed
- e.x. elastic load balancing for a website
	- each request is sent to a different replica
- ex. website mirroring
	- geographic distribution across the internet
	- clients choose from a list of mirrors

### Server-initiated replicas
- replicas are created when needed to improve performance
	- typically created by the owner/data store
- ex. web server replicas
	- a server that receives a burst of requests far from the server might want to deploy replicas in regions close to the requests
	- a heavily loaded server might add additional instances ot reduce load
- each server keeps track of access counts per file, aggregated by considering server closest to requesting clients
	- number of accesses drops below deletion threshhold D -> drop file
	- number of accesses exceeds replica threshhold R -> replicate file
	- number of access between D and R the file may only be migrated
### Client initiated replicas
- commonly known as *caching*
	- local storage used for temporarily storing a copy of requested data
	- improve access to data that is likely to be used again
	- could be on the local machine or coule be a nearby machine
	- most often the server has no knowledge of the cache, and provides no influence on caching decisions
- big question is how long to keep data in a cache (when to invalidate)
	- either because data is stale or to make room for other data
- many approaches: LRU, FIFO, size-based

#### What content shoudl replicate

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
- pushing updates: server-initiated approach, in which update is propagated regardless whether target asked for it
	- efficient when many more reads than writes, i.e. we expect the replica to be read
- pulling updates: client-initiated approach, in which the client requests to be updated
	- efficient when many more writes than reads

##### Hybrid approach: leses
- dynamically switch between pulling and pushing using *leases*
	- a contract in which the server promises to push updates to the client until the lease expires
- make lease expiration time dependent on system's behavior
	- adaptive leases
	- age-based leases: an object that hasn't changed for a long time, will not change in the near future, so provide a long-lasting lease
	- renewal-frequency based leases: the more often a client requests a specific object, the longer the expiration time for that client will be
	- state-based leases: the more loaded a server is, 

### Primary-based protocols
- most applications apply easy to understand consistency models
	- issue is that developers will struggle to implement correct applications
- for sequential consistency most often primary-based protocols are used
	- each data item in the data store has an associated primary date

#### remote write protocols

#### local-write protocols

#### replicated write protocols
- write operations carried out by multiple replicas at the same time
	- active replication: operation forwarded to all replicas
	- consistency protocols: majority voting
- read and write quorums
- 