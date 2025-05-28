# Why do we need distributed computation? Big problems
- simulation
	- simulating something that can't be done on a single computer
		- tens, hundreds, thousands of computer
		- may even need new models of computing
![[Pasted image 20250526200628.png]]
- big data
	- TB/PB of data won't fit on one computer
		- have to put the data and compute on multiple computers to hold it all
![[Pasted image 20250526200651.png]]

# Why do we need distributed computation? Scaling changes
- Moore's law
	- number of transistors packed into a unit of space will double every year
		- slowing down, revised to every 2 years
- reaching physical limits
	- high temperature of transistors makes it impossible to create smaller circuits

## Parallelizing computations
- multiple cores
	- 4, 8, 64, more?
- multiple nodes
	- clusters, clouds, grids

### sequential to parallel computing
- Moore's law is coming to an end
	- can't expect speeds to double every 18-24 months
		- we're seeing the **number of processing units increase**
			- multi-core chips
- implications:
	- no more automatic increase in speed for software
	- heterogeneity and parallelism is now the norm
		- lots of processors connected over a network and coordinating to solve problems
### Challenges
- parallel computing is hard
	- "chunking" computation
	- dealing with low level programming libraries
	- addressing concurrency, deadlock, bugs
	- efficiently scaling (i.e. avoiding scaling overheads)
- distributed computing adds challenges
	- sending data to/from nodes
		- coordinating activities across multiple nodes
		- dealing with failure
		- locality-aware analyses
		- distributed debugging

### Task vs. Data parallelism
- task parallelism
	- execute different operations in parallel
	- each process is responsible for doing something different
	- e.g. computing average and standard deviation of data
- data parallelism
	- execute the same operation on different parts of the data set
	- e.g. cracking passwords
![[Pasted image 20250526201804.png]]

### General programming approaches
- shared memory programming
	- processes share memory address space 
		- e.g. using multiple threads
- transparent parallelism
	- compiler converts sequential programs
- directive-based parallelism
	- developers provide hints to the compiler e.g. pragma in OpenMP
- Message passing
	- explicit communication between approaches

# Part II: Parallel Computing
- parallel programming models
	- OpenMP
	- MPI
	- CUDA
	- Batch computing
## Open Multi-Processing (OpenMP)
- one of the most common parallel programming models
	- designed for shared-memory systems and uses threads
	- extensions for clusters and distributed shared memory platforms
- a set of compiler directives an dlibrary routines for parallel application programmers
	- designed to be simple to use: annotate functions in Fortran, C, C++

### Core Syntax
- OpenMP relies on developers annoting code with compiler directives
	- `#pragma omp parallel`
	- `#pragma omp parallel num_threads(4)`
- Constructs are applied to "structured blocks"
	- a block of code with one or more statements and a clear entry and exit point
- requires compiling by linking to OpenMP libraries
#### Other features
- threads communicate by sharing variables (shared address space)
- synchronization primitives for developing code
	- e.g. barriers, mutual exclusion, locks, atomic operations
	- `#pragma omp barrier`
- Worksharing constructs
	- loops to split up iterations amongst threads
		- various scheduling options define how to allocate work
	- nested loops
		- `#pragma omp parallel for collapse(2)`
	- tasking model
![[Pasted image 20250526202330.png]]
![[Pasted image 20250526202341.png]]


## MPI
- we introduced MPI in the discussion of communication models
- MPI provides a collection of communication primitives for developing distributed programs
	- better than each application creating its own messaging model
- Recall: communication takes place within a known group of processes
	- each process is assigned an identifier (rank)
	- each process can be identified by (group ID, rank)
- Note: MPI is not a language or compiler specification or even a specific implementation
- you don't need to know exact code, but you need to know the general ideas

- OpenMP parallelizes within a node, MPI is designed to parallelize between nodes

## Programming GPUs
- GPUs are heterogeneous multi-processors
	- very high compute density (1000s of cores)
- inherently designed for high throughput parallel computations (on multiple sets of data)
- limited instruction set (oriented around images)
- GPUs don't replace CPUs
	- CPU still manages execution of main program and system
![[Pasted image 20250526203014.png]]


#### A brief history of GPUs
- 1999 - NVIDIA release GeForce 256 to accelerate graphics rendering in computer games
- Early 2000s - Developers gained programmatic access to GPUs and can control game rendering and visualization tasks
- 2006 - CUDA unleashes a storm of general-purpose GPU programming; finance, physics simulations, data processing
- 2008 - OpenCL: CUDA becomes cross platform, vastly increased for scientific computing
- 2012-2016 - deep learning boom; GPUs training neural networks
- 2020s - AI and HPC acceleration

### CUDA
- Compute Unified Device Architecture
	- parallel computing model and API for NVIDIA GPUs
- a *kernel* is the code executed by a single CUDA thread
	- threads are grouped into *warps* (32 threads) - the basic unit of execution
![[Pasted image 20250526203318.png]]

## Batch submission
- most clusters use batch schedulers to manage allocation of work on the cluster
	- Slurm, torque, cobalt, PBS, Grid engine
- basically looks like a big queue of jobs to run:
	- Slurm submits a **job** to the queue, requesting a specific number of nodes and cores
	- The job may use **MPI** to launch multiple processes across the allocated nodes
	- each **MPI process** runs user-defined code, which may use **OpenMP** to create multiple threads within the node

# Big-Data Frameworks
## today's frameworks
- MapReduce/Hadoop
	- **Programming model** for processing big data across independent workers
	- available in most programming languages
	- popular open-source framework: Hadoop
		- can be used to access data on the Hadoop Distributed File System (HDFS)
	- introduced by: Google
- Spark
	- unified, general data processing engine
	- excels at processing iterative map/reduce algorithms with many data accesses
	- interfaces for Java, Scala, R and Python
	- developed at UC Berkeley's AMPLab
		- now an open-source project by Apache

### Map (Functional programming)
- apply a function to each element in a collection and return the result
![[Pasted image 20250526203526.png]]

### Reduce (Functional Programming)
- recursively combine elements in a collection using the provided combine function
![[Pasted image 20250526203536.png]]

### MapReduce
- use *map* and *reduce* primitives to abstract complexities of distributed computing
![[Pasted image 20250526203548.png]]
### Shuffling
- intermediate step between map and reduce
- responsible for distributing generate map outputs to reducer tasks
- Steps:
	- Map task outputs are split into partitions using a partitioning function
		- default: `hash:(key) % reducers`
	- locations of the partitioned pairs are communicated to the leader process
	- leader forwards locations to reduce tasks who then use RPCs to fetch the data

There will be a question on MapReduce/Spark in the drafts

![[Pasted image 20250526204233.png]]

## Apache Spark
- extends MapReduce model to support applications that reuse a working set of data
	- iterative jobs (e.g. machine learning::convergence, gradient descent,e tc.)
	- interactive analytics
- presents a distributed data structure: Resilient Distributed Datasets (RDD)
- Avoids intermediate disk writes through in-memory computing

### Ex. Word Count
- **Objective**: Count the number of occurrences of unique words in a documen
- **Input data**: Text document
- **Map task**
	- Input: a shard of the document
	- Function: iterates over each word in the document
	- Returns: a tuple for each word in the form *(word, 1)*
- **Reduce task**
	- Input: sorted key-value pairs in the partition
	- Function: Sums all the values associated with the same key
	- Returns: all unique words and their respective counts
![[Pasted image 20250526204549.png]]

### MapReduce detailed steps
- step 1: Split data into chunks
	- data is by default broken into M shards of 64 MB
![[Pasted image 20250526204641.png]]
- step 2: Fork processes
	- start up **many copies** of the program on a cluster of machines
	- **Leader** process: scheduling and task coordination
	- Leader assigns **idle workers** Map or Reduce tasks
![[Pasted image 20250526204724.png]]
- Step 3: Run map tasks
	- read contents of assigned input shard
	- Generates **key-value pairs** from input shard's data using user's **Map** function
	- Output key-value pairs are **buffered in-memory**
![[Pasted image 20250526204844.png]]
- Step 4: partitioning
	- intermediate **key-value pairs** generated by map tasks periodically **written to disk**
	- Key-value pairs are **partitioned in **R** regions according to a partitioning function
		- **default** `hash(key) % R`
	- Reduce worker later **reads their partition** from **every** Map worker
![[Pasted image 20250526205220.png]]
- Step 5: Shuffle and Sort
	- Leader notifies **Reduce worker** of locations of its assigned **partition**
	- **Shuffle**: use RPCs to read partitions from **Map** workers
	- **Sort**: reduce worker **groups all data** belonging to the **same key** together
![[Pasted image 20250526205314.png]]
- Step 6: Reduce
	- Reduce function given **key** and **set of intermediate values** for that key
		- `<key, (value1, value2, ...)>`
	- Applies aggregation of those values according to user function
	- Output is appended to a final output file for the partition
![[Pasted image 20250526205550.png]]
- Step 7: Return results
	- Leader wakes up user program when all tasks (map and reduce) have completed
	- Outputs available across *R* partition files
![[Pasted image 20250526205633.png]]

### Fault tolerance
- leader pings each worker periodically
- **No response** (within delay): worker's map and reduce tasks are all **reset to idle**
- tasks are then rescheduled to other worker

### Locality
- Data are **large** and compute is **small**
- **Data locality definition**: move compute to data rather than data to compute
- MapReduce uses distributed file systems that store data locally on nodes (e.g. HDFS)
- Leader attempts to schedule tasks on workers that have copies of needed chunks
- **Limitation**: Preserving data locality becomes difficult as cluster grows
### Limitations
- Batch oriented
- Unsuitable for near-real-time processes
- cannot start new phase (reduce) until prior phase (map) is completed
- prone to stragglers

### Apache Spark
- Extends MapReduce model to support applications that **reuse** a working set of **data**
	- iterative jobs
		- e.g. machine learning::convergence, gradient descent, etc
		- interactive analytics
- Presents a distributed data structure
	- **Resilient Distributed Datasets** (RDD)
- Avoids intermediate disk writes through **in-memory computing**

### resilient Distributed Datasets (RDDs)
- Read-only partitioned collection of records
- attributes:
	- immutable
	- created via table in **stable storage** or **transformations** applied to existing RDDs
	- can explicitly persist intermediate results to memory
	- retains attractive MapReduce properties: fault-tolerance, data locality, scalability
- **Lineage**: keep history of transformations applied to data in case of failure


#### RDD Operations
- two types of operations can be applied to RDDs:
	- **Transformations**
		- course-grained deterministic operations
		- take as input RDDs and output new RDDs
		- lazily evaluated
			- transformation not done right away, built into a logical and optimizable DAG
			- ex: `map` and `filter`
	- **Actions**
		- perform an aggregation on an RDD
		- return a value
		- invokes computation
		- ex. `count` and `collect`

#### Spark application architecture
- all spark applications have a driver program that connects to a cluster of workers
- Driver program:
	- defines RDDs
	- invokes actions on them
	- maintains RDD lineage
- Workers:
	- long-lived processes
	- store one of more RDD partitions in memory
	- receive one **or more** action tasks from the driver
	- send local reduce results back to driver
![[Pasted image 20250526210606.png]]

#### Mapping logical graphs to physical execution
- two types of dependencies between partitions:
	- narrow: **one to one** mapping between parent and child partitions (e.g. `map`)
	- wide: **one to many** mapping between parent and child partitions (e.g. `join`)
- **Wide dependencies** incur **shuffles**
- **Stages**: pipeline **narrow** dependencies, split at **wide** dependencies
- action invoked: scheduler builds DAG of stages from lineage
![[Pasted image 20250526210904.png]]
#### Shuffles
- caused by wide dependency transformations and actions (e..g `reduce`, `groupby`, `join`)
- redistributes data across partitions
- identical to MapReduce shuffles - hash keys to partitions
- want to **minimize shuffles** as they result in **writing to disk**

#### Fault tolerance and caching
- use lineage to recreate RDD when data is lost
- re-computation occurs from last truncation
- Lineage gets truncated when:
	- RDD is persisted to memory or disk
		- useful when the same RDD is reused multiple times
	- RDD is already materialized to disk due to shuffle
![[Pasted image 20250526211139.png]]
#### Summary
- MapReduce and Spark are two of the most well known distributed processing frameworks for big data processing
- Hadoop MapReduce is one of the most commonly-used MR applications
	- batch oriented: map and then reduce
	- can be slow and relies on disk storage
- Spark is much faster, moving much of the data processing and staging to memory
	- provides several transformations and operations

Final
- know what you did on your homeworks and the project

walking through the clocks, leadership election, Paxos, Raft

Consistency Availability Partition Tolerance (CAP)

task vs data parallelism

don't have to implement OpenMP/MPI/Batch
- just understand when and where they will be used

- want to be able to reason through Spark application
	- know why Spark is really good, and why MapReduce has some problems

one pseudocode, not Docker


