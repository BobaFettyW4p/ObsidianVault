- final is cumulative
	- but the vast majority come from the second half of the class

- project due midnight anywhere on Earth

## Why do we distribute data?
- size: too large for a single node
- performance: satisfy the needs of many clients
- reliability: redundancy in case of failure

### Desirable properties when distributing data
- consistency
	- all entities see the same data at the same time
- Availability
	- every request receives a non-error response for every operation
- Partition tolerance
	- the system continues to operate even when connections between nodes are down/slow

#### Cap Theorem
CAP Theorem - pick 2 out of 3 of these

##### Takeaways
- network partitions are inevitable, in reality, a case of C vs A
	- C: return an error if we can't guarantee data is up to date
	- A: process the operation even though it might be wrong/lead to inconsistency
- These properties are continuous (rather than binary) so we don't have to gorego all the time
- network partitios are rare so we only have to make the tradeoff when there is failure

### ACID Transactions
- Atomicity
	- transaction executes wtih an "all or nothing" manner
		- a transaction can be a single operation or a series of operations
	- a transaction either succeeds and the database state is changed, or the database remains unchanged and all operations are dismissed
- Consistency
	- consistency as the transition from one valid state to another
		- never leaves behind an invalid state when executing transactions
			- e.g. banking app, no negative balanced
- Isolation
	- guarantees that no transaction sees premature operations of other running transactions
		- prevents conflicts between concurrent transactions
- Durability
	- assures persistence of executed transactions
		- once committed, the outcome of the transaction is kept, even in the case of a crash or other failures

#### C vs A
- Databases (C over A)
	- ACID
- NoSQL (A over C)
	- Basically
	- Available
	- Soft state
	- Eventual consistency

#### RDBMS -> NoSQL
- basic idea: there is a class of workload that doesn't need ACID, so we can loosen consistency to improve performance and availability
	- simple scale out, faster read/writes, maybe easier to use?

### Relational Database Management System
- schema-oriented
- data organized into tables
	- defined columns
	- constraints(PRIMARY KEY, UNIQUE)
	- types
- supports keys and foreign keys
- supports joins between tables
- Structured Query Language (SQL)
- highly optimized query planners

#### Scaling RDBMS
- maintaining ACID makes it difficult to scale a database
	- therefore, we typically scale RDMBS up
		- i.e. increase resources allocated
- distribution approach depends on needs
	- **read-heavy** - add replicas
		- add as many replicas needed to handle the workload
		- balance queries across the replicas in round robin fashion
	- **write-heavy** - partition database across many servers
		- put diffferent tables on different machines
		- shard one table across many machines
			- horizontal fragmentation: splutrows
			- vertical fragmentation: split columns
### Amazon Aurota
- MySQL and PostgresSQL-compatible relational databases in the cloud
- Aurora uses horizontal fragmentation by default
	- replicas, fully queryable DB
	- data copy for fault tolerance/durability

## NoSQL: address changing workload requirements
- data are large and unstructured
- many random reads and writes
- joins are infrequent
- Goals:
	- speed
	- avoid single point of failure
	- reduce ownership costs
		- cheap hardware
		- no $ spent organizing schema
	- fewer system administrators
	- elastic scalability
	- scale out rather than up

### Not Only SQL (NoSQL)
- unstructured
- no schema
	- schemaless
- Sparse
	- column/row values might not be all set
- no foreign keys and therefore joins may not be supported
- doesn't use SQL as a query language

#### Advertised benefits
- simplicity (from a design/interaction perspective)
	- with no defined schema, the application is now responsible for enforcing schema
- horizontal scaling
	- given this is done through sharding, this can create complex partitioning strategies performed from the application level
- high availability
	- eventual consistency
- performance
	- less so when the data could be optimized as relational queries

### Rows vs. Columns
- Row store
	- store all attributes of a tuple together
		- easy to add/modify records
		- may read unnecessary data
			- if you only need one column, the storage layout forces you to physically read the entire row (even if other columsn are unused)
- Column store
	- store all rows for an attribute together
		- only read relevant attributes
		- read/write requires multiple access
			- if you need multiple columns for a row, you must access each columns' storage separately, resulting in multiple I/O operations

#### NoSQL variations
- key-value stores
	- store simple, flat lists of key-value pairs
	- expose GET or PUT operations
	- Redis, memcached
- Document stores
	- each key is paired with a whole document (e.g. JSON)
	- MongoDB, CouchDB
- Column family
	- each row is addressed by a key and contains one or more columns
	- columns are key-value pairs
		- tuple: name, value, timestamp
		- no nulls, but indexes are important in presence of new columns
	- Cassandra, HBASE
- Graph
	- Neo4J
### Organizing NoSQL stores
- most NoSQL stores group key-value pairs into something that looks like a table
	- Cassandra: column families
	- mongoDB: collections
	- HBase: Tables
	- DynamoDB: Tables
- NoSQL stores typically store a column (or group of columns together)
	- index entries in a column so they are easier to retreive
	- supports range queries as you don't need to get entire database

#### Eventual consistency
- consistency or availability... when we have a partition
	- RDMS: strong consistency over availability
	- NoSQL: availability over consistency (eventual consistency)
- if writes stop (to a key), then all values (replicas) will converge eventually
	- converse: if writes continue, we will keep trying to converge
	- maintain set of updated values lagging the latest values sent by clients
- Implication:
	- may return stale values to clients
		- e.g. if many back to back writes
	- works well when periods of low writes - system converges quickly

### Tunable consistency levels
- client can choose a consistency level for each operation
	- read/write
- Strong consistency if R+W > N
- eventual consistency if R+W <= N
	- ANY: write written to at least one node
		- fastest: node will cache write and reply quickly to client
	- ALL: write written to all nodes
		- ensures strong consistency, but slow
	- ONE: at least one replica
		- faster than ALL, but cannot tolerate failures
	- QUORUM: writtent o a configurable quorum across nodes
## MongoDB
- document-oriented database
	- schema-less
	- essentially stores arbitrary documents
		- documents are given a unique ID
		- documents arbitrary keys/values
	- its own query language
	- primary-secondary replication across replica sets
	- no joins/transactions

### MongoDB Architecture
- data is sharded across machines
	- scale and performance
- documents are distributed across shards using an immutable "shard key"
	- knowledge of data/queries can optimize use
- supports different sharding strategies
	- e.g. hashing and range-based
- query router sends queries to appropriate shards

### BSON
- Mongo wanted to support users
	- JSON is persuasive
		- seems like a good choice for representing data structures
- Challenges
	- JSON has limited data types
		- no date, binary
	- JSON objects don't have fixed length
- BSON (Binary JSON)
	- adds data types and fixed lengths
	- anything in JSON can be stored as BSON
	- binary encoded == faster to parse -> types already encoded
- MongoDB stores data in BSON internally and over the network

## Elastisearch
- scalable text-based search enginer service
	- HTTP web interface
	- Sceham-free JSON document index
- relies on Apache Lucene
	- a java library for tokenizing and searching text
- Elastic search provides
	- simple REST API
	- Easy to use (supports non-Java)

### Ex problems
- searching a number of product descriptions for the best match for a phrase and returning the best results
- given the previous example, breaking down the various departments where a phrase appears
- searching text for words that sound like another word
- auto-completing a search box based on partially types

#### Distributed search engine
- indices are divided into shards
	- shards may have 0+ replicas
		- replicas never on same node as primary shard
- nodes host 1+ shards
- nodes act as coordinators to delegate operations to the correct shard
- rebalancing and routing are done automatically
- a variey of managed services now exist: Elastic, Amazon, Google


# Distributed file systems and shared memory
## File systems
- traditional file system
	- implemented by a single system
- remote file systems
	- file system is served by a remote resource
- parallel file system
	- file system is distributed across many nodes
- distributed file system
	- files are divided and replicated across nodes

### Remote file system
- files are stored on a remote system
- clients can perform system calls using RPC to the server
- Benefits
	- data sharing between users
	- location transparency
	- backups for fault-tolerance

### Network File System (NFS)
- developed by Sun in 1984
- client-server model
	- server implements the shared file system and storage
	- clients use RPC to perform common operations (list, read, write, etc.)
	- caching at client and server for performance
- in linux, users mount the remote file system so it looks like another path in their directory
- weak consistency
	- client flushes updates on close()
- clients periodically check if data has changed -> periods of inconsistency
- no promises when multiple writes

### Andrew File System (AFS)
- named after the founders of CMU
- designed for institutional deployments - provide a file space to all client workstations
- assumptions:
	- most files are small
	- accessed by a single user
	- reads happen much more often than writes
- more aggressive caching than NFS
	- lower server load than NFS but likely slower
- weak consistency
	- files are cached locally and permanently on disk
		- to survive failures
	- read/writes directed to the local cache
		- when file is closed, changed portions are copied to the file server
	- clients register with server that they have a copy of a file, server then sends cache invalidation messages if file changes (tradeoff server state vs better consistency)

### file System in User Space (FUSE)
- let user-space programs handle file system calls like:
	- open()
	- read()
	- write()
	- close()
- users simply write a handler program that implements common file system operations
- why it's useful?
	- no kernel development or root privileges needed
	- safer development
		- bugs don't crash the OS
	- good for cross-platform development
		- user-space code can be portable
		- FUSE or FUSE-style interfaces exist on multiple OS platforms

### Parallel file systems (HPC)
- parallel file systems store data across multiple networked servers
	- all servers are given a portion of the namespace
- typically used by HPC environments that require access to large files, many files, or simultaneously access by many nodes/users
	- ensure consistency across file system
- unlike general distributed file systems (e.g. NFS) they generally require specialized client drivers to access at high speed (e.g. on infiniband)
- Benefits
	- scale, performance, reliability

#### Parallel File System Examples
- IBM GPFS (Spectrum Scale)
	- block-based file system, with blocks of tunable width and dynamic metadata
	- POSIX interface
	- unified global namespaces across storage (e.g. SSD, HDD, tape...)
	- common in enterprise settings (e.g. finance, genomics, oil and gas)
- Lustre
	- open source, single namespace
	- POSIX interface
	- object-based storage
	- separated metadata stores
	- ANL 100PB Stores (8,480 drives)
		- >TB/s throughput
	- ORNL Orion: 679 PB file system

### Google File System (GFS)
- Google has unique usage requirements
	- applications: search, gmail, youtube, etc.
	- enormous amounts of data must be stored and queried
	- data creation and access rates are extreme
	- lead to unique read/write patterns
- Google has huge data centers (with commodity hardware)
- GFS is a specialized distributed fault-tolerant file system

#### GFS: Infrastructure
- use of commodity computers
	- standard x86 servers
	- no RAID
- each computer has many local-accessible commodity disks
- failures are to be expected
	- need to design accordingly

#### GFS: Assumptions
- modest number of large files
	- a few million files (>100 MB)
	- multi-GB files are common
	- small files should be supported, but we don't have to optimie for them
- workloads
- large streaming reads (hunders of KBs, or MBs)
	- reads from the same client normally contiguosu region of a file
	- small random reads (few KBs)
	- large sequential writes that append data to files (KBs-MBs)
- must support multiple clients that concurrently append to the same file
	- logs, mapReduce
- high sustained bandwidth is more important than low latency
#### GFS: Basic architecture
- files are divided into fixed size chunks (64 MB)
	- each is assign a unique 64-bit label
- nodes are divided into two types
	- master node
	- many chunk severs(storing the chunks)
- chunks are replicated several times
	- default 3, but configurable
		- popular files may have higher replication

#### GFS Master
- manages all metadata
	- namespace
	- mapping from files to chunks
	- current chunk locations
	- access control information
- stores metadata in RAM to provide fast operations
- garbage collects chunks (those no longer associated with files) and manages chunk migration across chunkservers (for balancing)

#### GFS Chunkserver
- simple server that stores 64MB cunks on commodity disks using standard file system
	- 64 MB - much larger than typical file system block
		- reduces client need to interact with the master when operating within the same chunk
		- reduce network overhead by keeping persistent connection with chunkserver
- read/write requests operate on a sepecific chunk ID

#### GFS Consistency
- relaxed consistency model to support highly distributed applications
- metadata changes are atomic (e.g. file creation)
	- managed by single master
		- using a log to establish order
- data changes on replicase are ordered following the order of its primary replica
	- avoid the use of stale replicas in an operation
		- master won't point to them
- client caching may result in some stale data within a defined window period

## Hadoop File System (HDFS)
- designed for running MapRedue applications
	- implemented in Java
	- Data store API (not POSIX)

### HDFS overview
- stores large files (GB-TB) across multiple machines
- files divided into 128 MB blocks
- blocks are replicated across multiple hosts
- designed for commodity hardware
- failures are expected
- designed for primarily immutable files
	- read once, write many times
- portable across hardware platforms and compatible with many OSes






