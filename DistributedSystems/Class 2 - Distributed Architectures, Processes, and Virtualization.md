# Recap
- what is a distributed system?
- middleware - the OS of a distributed system
- design goals for distributed systems
- the eight fallacies of distributed computing

# Intro
- we established last class that distributed systems are complicated
	- nodes are distributed and independent
	- nodes may fail (and it's hard to know that they failed)
	- no global clock, and so coordination is difficult
- how should we go about managing/organizing a distributed system?
	- we'll take queues from **software architecture** - define how software components are organized and how they should interact

# Distributed architecture styles
## Layered architectures
- components are organized into layers and higher layers make calls to lower layers
	- responses are returned from lower to higher levels
	- most applications are implemented in layers
	- network communication architectures
### Common layering approach
- 3 classes of layer
	- **application interface layer** - the user interface
	- **processing layer** - logic and function of the application
	- **data layer** - state manipulated by application components
![[Pasted image 20250422202233.png]]

![[Pasted image 20250422202256.png]]

- Ex. search engine
	- user interface - google homepage
	- processing level - converts search string to a query
	- data level - query database for webpages
	- processing level - convert and return database info to user
![[Pasted image 20250422202311.png]]

- pros
	- simple implementation
	- widely used
	- intuitive
- cons
	- strong dependency between layers
		- bottlenecks
		- modularity
		- layer changes
## Object based/service-based architecture
- what are the advantages of object-oriented programming?
	- modularity, reuse, extensibility, abstraction, **encapsulation**, polymorphism
	- **encapsulate data objects** and an **interface for methods on that data**
		- an object-to-object interface can be transparent to the user
	- faster development, easier maintenance, etc.
- components are objects, connected to each other through procedure calls
- objects may be placed on different nodes, calls can execute across a network
![[Pasted image 20250422202508.png]]
- Ex. distributed objects
![[Pasted image 20250422202520.png]]
- Ex. service oriented architecture
	- the service is realized as a self-contained entity, that can make use of other services
		- distributed application is composed of many different services
			- may cross between administrative domains
	- **simple object access protocol (SOAP)**
		- strict protocol for exchanging information across SOA
## Microservice (resource based) architectures
- SOAP as a predecessor of REST
	- rich APIs with wide range of methods
		- complicated, difficult to understand the interface, hard to version etc.
- a simpler way is to think about **resources** and create a **fixed set of interfaces** for manipulating those resources
### What is a microservice?
- small independent services responsible for a specific business function (one service does one thing)
	- anything can talk to anything
- services communicate over lightweight channels such as **REST**, gRPC or message queues
- each service can be scaled, deployed and maintained independently
![[Pasted image 20250422202655.png]]
## Representational State Transfer (REST)
- a distributed system as a collection of resources, individually managed by components
- resources may be added, removed, retrieved, and modified by remote applications
- REST is not a protocol, but an architecture (flexible)
- Characteristics:
	- resources are identified through a single naming scheme
	- all services offer the same interface
	- messages sent to or from a service are fully self-described
	- after executing an operation at a service, that component forgets everything about the caller

### REST relies on HTTP operations
- **PUT** - create a new resources
- **GET** - retrieve the state of a resource
- **DELETE** - delete a resource
- **POST** - modify a resource by transferring a new state
- Ex. AWS S3 (Simple Storage Service)
	- two resources:
		- objects - bundle of bytes representing something
		- buckets - namespace for organizing objects
	- objects are referenced using a URI
	- simple operation to create, update, delete, and retreive objects

## Event-based systems
- as systems scale (more nodes join and leave) it's important to decouple dependencies between processes
	- separation between coordination and processing
![[Pasted image 20250422202748.png]]

### Direct and mailbox
- *direct*
	- referential coupling appears in form of explicit reference in communication
		- a process can communicate if it knows the name of the other process
	- temporal couple occurs as both processes are online at the same time
- *mailbox*
	- temporal decoupling - processes are not executing at the same time
	- communication takes place by putting messages in a mailbox, others can retreive
### Event-based and data spaces
- *event-based*
	- processes do not know each other explicitly, instead they **publish** a notification describing the occurrence of an event
	- other processes may choose to **subscribe** to that event, in which case they receive notifications
- *data space*
	- shared data space where processes communicate via tuples
		- tuple - data records with a number of fields
	- process puts tuple in tuple space, another process can search using patterns
	- Ex. Linda
# Processes and threads
## What is a process?
-  a program in execution
	- an instance of a computer program that is being executed
![[Pasted image 20250422203252.png]]

## What is in a process?
- machine code that is to be run
- memory allocation
- resources
- processor context
- this is stored in a **process control block (PCB)**
## How does an OS work?
- **processor**
	- physical hardware capable of executing instructions
- **virtual processor (vCPU)**
	- logical processor managed by an OS or hypervisor
	- allows multiple programs or VMs to share hardware
	- can be **oversubscribed** (many vCPUs mapped to one physical core)
	- managed by hypervisors or contains
- **process context & context switching**
	- **process context**: CPU register values, memory maps, open files, etc.
	- **Context switching**: switching between processes by saving/loading contexts
### Why do we need multiple processes?
- when a blocking system call is executed the entire process blocks
- multiprocessor/multicore machines allow independent functions to be executed concurrently
	- if functions can be run in parallel then overall execution is faster
- Large application design: collection of cooperating programs that communicate with one another

### Summary
- a program in execution
	- OS ensures processes are independent
	- Creating a process == OS must create a new address space
		- when context switching we must save and reload context
- processes are heavyweight, but provide strong isolation
## Threads
- a piece of code executing independently of other threads
	- executes its own piece of code (like a process)
	- independent of other threads
- there is little focus on concurrency transparency:
	- only maintains minimal context that allows the CPU to be shared
	- thread context => nothing more than processor context (+ minimal info for thread management)
	- isolation is left entirely to developers
		- threads can access shared data in the process
	- result is better performance
		- fast to create
		- fast to context switch
### Context switching
- storing state of process/thread so it can be later restored and execution resumed from that state
- process context switching involves the OS - kernel interruptiion
- threads share address space so context switching can be independent of the OS
- creating destroying threads is cheaper than process
	- tradeoff performance v. isolation
### How are threads implemented?
- via threading package
	- operations to create and destroy threads
	- synchronization variables (mutables, locks, barriers)
- should the OS implement threads? or should they be implemented at the user level?
### Threads in user space
- **many to one** model in which many threads are mapped to one kernel process
- Advantages
	- cheap: all administration is kept in user address space
	- efficient: all operations handled within a single process, all services provided by the kernel are done on behalf of that process
	- context switching: can be done in a few instructions (only values of CPU registers need to be stored reloaded)
- Disadvantages
	- threads are mapped to a single schedulable entity (at the OS level)
		- invocation of a **blocking system call** will immediately block the entire process to which the thread belongs
![[Pasted image 20250422204029.png]]
### Threads in kernel space
- the kernel implements the thread package so all operations return as system calls
	- **one to one model**: every thread is a schedulable entity
- operations which block a thread are no longer a problem
	- the kernel schedules another available thread within the same process
- handling external events is simple - the kernel schedules the thread associated with the event
Disadvantage: each thread operation has to be carried out by the kernel
	switching contexts might now be as expensive switching process context
![[Pasted image 20250422204109.png]]

### Threads in distributed systems
- how do threads in the OS apply to distributed systems
	- multi-threaded clients
	- multi-threaded servers
### Multi-threaded clients
- good at hiding network latencies
	- e.g. web browser scans HTML page to find elements that need to be fetched
	- each file is fetched by a separate thread, each doing a blocking HTTP request
	- as files come in, the browser displays them
- multiple request-response calls to other machines (RPC)
	- a client does several calls at the same time, each one by a different thread
	- it waits until all results have been returned
	- **Note**: if calls are to different servers, we may have a linear speed-up

### Multi-threaded servers
- improve performance
	- having a single-threaded server prohibits simple scale-up to a multiprocessor system
	- as with clients: hide network latency by reacting while the previous one is being replied
- better structure
	- most servers have **high I/O demands**
		- using simple, well-understood blocking calls simplifies the overall structure
	- multithreaded programs tend to be smaller and easier to understand due to simplied flow of control

# Virtualization
- enables you to run a virtual computer system using an abstraction later above the hardware
	- extend/replace an existing interface to mimic the behavior of another system
- Important for many reasons:
	- hardware changes faster than software
	- ease of portability and code migration
	- isolation of failing or attacked components
## Virtualization in distributed systems
- initially, allowed legacy software to run on mainframe hardware
- increasingly common to provide a stable interface for slow changing software
- later, a way to provide elastic computing capacity in the cloud and manage large numbers of heterogeneous servers
- now, useful as a means of porting applications

### Virtual machines
- emulation of a computer system
- allows users to run multiple operating systems on a single OS
- *hypervisor*: a program used to run or manage one or more VMs on a computer
	- can reallocate resources between VMs
- hardware assisted virtual machines provide architectural support for VM monitor and allow guest OSes to be run in isolation
![[Pasted image 20250422204730.png]]

![[Pasted image 20250422204636.png]]

![[Pasted image 20250422204702.png]]
### Container
- originally designed to segregate namespace in Linux OS for security
- lightweight, isolate environment that packages an application and its dependences that share the host's OS kernel
- Linux environment included partitions(jails) which questionable applications could be executed safely
- much lower overhead than traditional VMs

![[Pasted image 20250422204849.png]]

### Docker
- developed for Linux
	- cgroups to limit resource usage
	- kernel namespaces limit app view of the operating environment
	- union-capable file system

### Virtualization in cloud computing
- arguably the most important technology for realizing cloud computing
- Infrastructure as a Service (IaaS)
	- cloud providers rent out VM
	- may share physical machine with others
	- allows for isolation, resource sizing
- container services (ECS)
	- provide interfaces for running containers
- function as a service?

#### Summary
- virtualization enablles you to run a virtual computer system using an abstraction layer above the hardwre
- virtual machines allow for multiple operating systems to be run on a single machine
- containers provide lightweight virtualization on top of an OS
	- enables application portability
	- fast deployment
	- easy isolation
# Client server organization
- client server model
## Clients
- **thick clients**
	- remote server has a separate client that contacts the server over the network
		- Word, phone calendar, mySQL workbench
![[Pasted image 20250422205003.png]]
- **thin clients**
	- direct access to remote services with a single user interface
		- Google Docs, Dropbox, YouTube, Google Colab, Chrome
![[Pasted image 20250422205023.png]]
### Client side distribution transparency
- clients often maintain state and perform processing as a means of distributing load or abstracting distribution
- how does a client provide transparency?
	- access transparency - clients stubs for RPC
	- location/migration transparency - client tracks actual location
	- replication transparency - multiple servers, client will handle replication invocations to each server
	- failure transparency - client must support if masking server/communication failures
## Server
- a process implementing a specific service on behalf of a collection of clients
- General design issues:
	- concurrent v. iterative
	- how to contact a server
	- interrupting server
	- stateless v. stateful
### Concurrency
- iterative server: server handles the request before attending the next
- **concurrent server**: using a dispatcher, which picks up an incoming request that is then passed on to a separate thread/process
	- multi-threaded servers
### Contacting a server
- server listens on a specific port
- some common services have well-known ports
	- FTP == TCP 21
	- HTTP == TCP 80
	- GTTPS == TCP 443
- can have a method for the client to discover the port

### Discovering a server
- daemon process used to register servers and clients must first ask for the port
![[Pasted image 20250422205223.png]]
- *superserver* manages creation of server on port within predefined range
![[Pasted image 20250422205500.png]]
### Interrupting a server
- is it possible to interrupt a server once it has accepted a service request?
	- a user uploads a large file to FTP server, once they realized it's the wrong file, how can they stop?
		- *put computer in airplane mode, wait for server to time out*
		- *use a separate port for urgent data*
			- client can communicate on this port
		- *use facilities of the transport layer*
			- TCP allows for this
### State
- **stateless server* 
	- does not keep information on the state of clients
	- can change state without informing clients
	- in practice, most servers store at least some state, but if that's lost, it won't impact clients
	- *Advantages*
		- independence - clients/servers are independent
		- inconsistency - if client/server crashes, there is no need to sync
	- *Disadvantages*
		- performance - server can't anticipate client behavior
### Stateful servers
- servers maintain client-specific information across requests unless explicitly deleted
	- *advantages*
		- better performance via caching and prefetching
	- *disadvantages*
		- lower reliability if state data is lost or corrupted






