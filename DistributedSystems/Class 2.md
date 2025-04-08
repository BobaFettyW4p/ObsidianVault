# Recap
- what is a distributed system?
- intro - middleware - the OS of a distributed system
- design goals for distributed systems
- the eight fallacies of distributed computing
# Types of distributed systems
- high performance distributed computed (HPR)
	- cluster
		- a group of high-end systems connected through a LAN
			- homogenous: same OS, near identical hardward
			- single managing node
	- Supercomputers
		- special class of computer
			- "a cluster with sauce"
			- may be clusters on massive scale (10k+ nodes)
			- networking between computer is extremely fast
		- measure by FLOP (floating point operations per second)
	- Grid computing
		- lots of nodes from everywhere
			- heterogeneous:
				- why is this advantageous?
			- dispersed across several organizations
			- span a wide-area network
		- grids generally use virtual organizations. This is a grouping of users that will allow for authorization or resource allocation
	- Cloud computing
		- the next step is to simply outsource the entire infrastructure that is needed for applications
- Distributed Information systems
	- one of the main examples of distributed systems is in the management of info:
		- distributed databases with distributed transactions
		- enterprise application communication
	- distributed systems need to be able to exchange info between nodes
### Databases
- Transactions
	- all or nothing semantics (everything succeeds, or transaction fails)
- ACID Properties
	- Atomic: to outside world, the transaction happens indivisibly
	- Consistent: The transaction does not violate system invariants (rules)
	- Isolated: Concurrent transactions do not interfere with each other
	- Durable: Once a transaction commits, the changes are permanent
### Enterprise application communication
- methods for allowing application components to communicate directly with one another
- Message Passing Interface (MPI) enables different messaging models in a network environment
- Remote Procedure Call (RPF)
	- one component can send a request to another by performnign a local procedure call

### Distributed systems for pervasive computing
- mobile computing, sensor networks
- pervasive systems naturally blend into the environment
	- as a result of mobile and embedded systems
- nodes are no longer permanent, they fail, move, leave
- different challenges than the more permanent systems discussed previously
	- separation between user/system is blurred
	- lack of single dedicated interface
	- often has many sensors to pick up aspects of user behavior
	- actuators to provide information/feedback
- ubiquitous computing, mobile computing, sensor networks

#### EX. Ubiquitous computing
- system is pervasive and continuously present
	- users interact with the system without even being aware of the interaction
- Requirements:
	- Distribution: devices are networked, distributed, and accessible in a transparent manner
	- Interaction: interaction between users and devices is highly unobtrusive
	- Context awareness: the system is aware of a user's context in order to optimize interaction
	- Autonomy: devices operate autonomously without human intervention, and thus highly self-managed
	- Intelligence: the system as a whole can handle a wide range of dynamic actions and interactions
#### Ex. Mobile computing
- phone, tablet, watch, car, GPS devices
- the battery is the most annoying part
- the location of a device is assumed to change over time
		- what does this mean?
		- how do we work out how to communicate with a device?
		- how do we discover services? how do we announce our presence to others? how do we locate other devices?

### Wireless mobile ad hoc network (MANET)
- form an ad hoc network amongst nodes
- how to route messages?
	- pass messages along a path of nodes, inevitably will break as intermediate nodes will move out of neighbor's range
- rely on two ideas
	- flooding messages that propagate throughout the network (significant overhead)
### Sensor networks
- thousands of small nodes with sensing devices
- often wireless communication and battery powered
- challenges include limited resources, communication, constrained power
- scope-based communication
	- e.g. neighbors, systemwide, individual node

### Internet of Things
- the internet of things is a network of devices such as behicles, and home appliances that contain electronics, software, sensors, actuators and connectivity which allows them to connect, interact and exchange data

### Edge computing
- bring computing closer to where it is needed
- often used to mean computation is primarily conducted on distributed devices
	- some consider anything not in a data center to be the edge
- common in IoT and sensor entworks
- increasingly relied upon in distributed computing as data sizes increase, more
### Summary
- there are many classes of distributed systems:
	- designed for computation, HPC clusters
	- designed for information sharing, distributed databases
	- designed for pervasiveness, mobile computing and sensor networks
# Intro
- we established last class that distributed systems are complicated
	- notdes are distributed and independent
	- nodes may fail (and it's hard to know that they failed)
	- no global clock, and so coordination is difficult
- how shoudl we go about managing/organizing a distributed system?
	- we'll take cueues from software architecture

# Distributed architecture styles
## Layered architectures
- components are organized into layers and higher layers make calls to lower layers
	- most applications are implemented in layers
	- network communication architectures
### Common layering approach
- 3 classes of layer
	- application interface layer - the user interface
	- processing layer - logic and function of the application
	- data layer - state manipulated by application components
- Ex. search engine
	- user interface - google homepage
	- processing level - converts search string to a query
	- data level - query database for webpages
	- processing level - convert and return database info to user
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
- components are objects, connected to each other through procedure calls
- objects may be placed on different nodes, calls can execute across a network
- Ex. distributed objects
- Ex. service oriented architecture
	- the service is realized as a self-contained entity, that can make use of other services
		- distributed application is composed of many different services
	- simple object access protocol (SOAP)
		- strict protocol for exchanging information across SOA
## Microservice (resource based) architectures
- SOAP as a predecessor of REST
	- rich APIs with wide range of methods
		- complicated, difficult to understand the interface, hard to version etc.
- a simpler way is to think about resources and create a fixed set of resources
### What is a microservice?
- small independent services responsible for a specific business function (one service does one thing)
	- anything can talk to anything
- services communicate over lightweight channels such as REST, gRPF or message queues
- each service can be scaled, deployed and maintained indepdently
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
- PUT - create a new resources
- GET - retrieve the state of a resource
- DELETE - delete a resource
- POST - modify a resource by transferring a new state
- Ex. AWS S3 (Simple Storage Service)
	- two resources:
		- objects - bundle of bytes representing something
		- buckets - namespace for organizing objects
	- objects are referenced using a URI
	- simple operation to create, update, delete, and retreive objects

## Event-based systems
- as systems scale (more nodes join and leave) it's important to decouple dependencies between processes
	- separation between coordination and processing
### Direct and mailbox
- direct
	- referential couplign appears in form of explicit reference in communication
	- temporal couple occurs as both processes are online at the same time
- mailbox
	- temporal decoupling - processes are not executing at the same time
	- communication takes place by putting messages in a mailbox, others can retreive
### Event-vased and data spaces
- event-based
	- processes do not know each other explicitly, instead they publish a notification describing the occurrence of an event
	- other processes may choose to subscribe to that event, in which case they receive notifications
- data space
	- shared data space where processes communicate via tuples
		- tuple - data records with a number of fields
	- process puts tuple in tuple space, another process can search using patterns
	- Ex. Linda
# Processes and threads
## What is a process?
-  a program in execution
	- an instance of a computer program that is being executed
## What is in a process?
- machine code that is to be run
- memory allocation
- resources
- processor context
- this is stored in a *process control block (PCB)*
## How does an OS work?
- processor
	- physical hardware capable of executing instructions
- virtual prodessor (vCPU)
	- logical processor managed by an OS or hypervisor
	- allows multiple programs or VMs to share hardware
	- can be oversubscribed (many vCPUs mapped to one physical core)
	- managed by hypervisors or contains
- process context & context switching
	- process context: CPU register values, memory maps, open files, etc.
	- Context switching: switching between processes by saving/loading contexts
### Why do we need multiple processes?
- when a blocking system call is executed the entire process blocks
- multiprocessor/multicore machines allow independent functions to be executed concurrently
	- if functions can be run in parallel then overall execution is faster
- Large application design: collection of cooperating programs that communicate with one another

### Summary
- a program in execution
	- +ve: OS ensures processes are independent
	- -ve: Creating a process == OS must create a new address space
		- when context switching we must save and reload context
- processes are heavyweight, but provide strong isolation
## Threads
- a piece of code executing independently of other threads
	- executes its own piece of code (like a process)
	- independent of other threads
- there is little focus on concurrency transparency:
	- only maintains minimal context that allows the CPU to be shraed
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
- many to one model in which many threads are mapped to one kernel process
- Advantages
	- cheap: all administration is kept in user address space
	- efficient: all operations handled within a single process, all services provided by the kernel are done on behalf of that process
	- context switching: can be done in a few instructions (only values of CPU registers need to be stored reloaded)
- Disadvantages
	- threads are mapped to a single schedulable entity (at the OS level)
		- invocation of a blocking system call will immediately block the entire process to which the thread belongs

### Threads in kernel space
- the kernel implements the thread package so all operations return as system calls
	- one to one model: every thread is a schedulable entity
- operations which block a thread are no longer a problem
	- the kernel schedules another available thread within the same process
- handling external events is simple - hte kernel schedules the thread associated with the event
Disadvantage: each thread operation has to be carried out by the kernel
	switching contexts might now be as expensive switching process context

### Threads in distributed systems
- how do threads in the OS apply to distributed systems
	- multi-threaded clients
	- multi-threaded servers
### Multi-threaded cleints
- good at hiding network latencies
	- e.g. web browser scans HTML page to find elements that need to be fetched
	- each file is fetched by a separate thread, each doing a blocking HTTP request
	- as files come in, the browser displays them
- multiple request-response calls to other machines (RPC)
	- a client does several calls at the same time, each one by a different thread
	- it waits until all results have been returned
	- Note: if calls are to different servers, we may have a linear speed-up

### Multi-threaded servers
- improve performance
	- having a single-threaded server prohibits simple scale-up to a multiprocessor system
	- as with clients: hide network latency by reacting while the previous one is being replied
- better structure
	- most servers have high I/O demands
		- using simple, well-understood blocking calls simplifies the overall structure
	- multithreaded programs tend to be smaller and easier to understand due to simplied flow of control

# Virtualization
- enables yout o run a virtual computer system using an abstraction later above the hardware
	- extend/replace an existing interface to mimic the behavior of another system
- Important for many reasons:
	- hardware changes faster than software
	- ease of protability and code migration
	- isolation of failing or attacked components
## virtualization in distributed systems
- initially, allowed legacy software to run on mainframe hardware
- increasingly common to provide a stable interface for slow changing software
- later, a way to provide elastic computing capacity in the cloud and manage large numbers of heterogeneous servers
- now, useful as a means of porting applications

### virtual machines
- emulation of a computer system
- allows users to run multiple operating systems on a single OS
- *hypervisor*: a program used to run or manage one or more VMs on a computer
	- can reallocate resources between VMs
- hardware assisted virtual machines provide architectural support for VM monitor and allow guest OSes to be run in isolation

### container
- originally designed to segregate namespace in Linux OS for security
- lightweight, isolate environment that packages an application and its dependences that share the host's OS kernel
- linx environment included partitions(jails) which questionable applications could be executed safely
- much lower overhead than traditional VMs

### Docker
- developed for linux
	- cgroups to limit resource usage
	- kernel namespaces limit app view of the operating environment
	- union-capable file system

### Virtualization in cloud computing
- arguably the most important technology for realizing cloud computing
- Infrastructure as a Service (IaaS)
	- cloud providers rent out VM
	- may share phjysical machine with others
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
- thick clients
	- remote server has a separate client that contacts the server over the network
		- Word, phone calendar, mySQL workbench
- thin cleints
	- direct access to remote services with a single user interface
		- Google Docs, Dropbox, YouTube, Google Colab, Chrome
### Client side distribution transparency
- clients often maintain state and perform processing as a means of distributing load or abstracting distribution
- how does a client provide transparency?
	- access transparency
	- location/migration transparency
	- replication transparency
	- failure transparency
## Server
- a process implementing a specific service on behalf of a collection of clients
- General design issues:
	- concurrent v. iterative
	- how to contact a server
	- interrupting server
	- stateless v. stateful
### Concurrency
- itertive server: server handles the request before attending the next
- concurrent server: using a dispatcher, which picks up an incoming request that is then passed on to a separate thread/process
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
- superserver manages creation of server on port within predefined range

### Interrupting a server
- is it possible to interrupt a server once it has accepted a service request?
	- a user uploads a large file to FTP server, once they realized it's the wrong file, how can they stop?
		- put computer in airplane mode, wait for server to time out
		- use a seperate port for urgent data
			- client can communicate on this port
		- use facilities of the transport layer
			- TCP allows for this






