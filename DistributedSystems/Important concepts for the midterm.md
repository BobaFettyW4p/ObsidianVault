## The Eight fallacies of distributed computing
- Many distributed systems are needlessly complex caused by mistakes that require patching later. Many false assumptions are often made...
	- the network is reliable
	- the network is secure
	- the network is homogenous
	- the topology does not change
	- latency is zero
	- bandwidth is infinite
	- transport cost is zero
	- there is one administrator

## Middleware: a distributed operating system
- represents all of the things that should be done across a distributed system

![[Pasted image 20250422193420.png]]

- characteristics of a distributed system
	- more than one node
	- independent nodes
	- no global clock
	- independent failures
	- a single coherent system
- we manage a distributed system using middleware
	- middleware provides services that are necessary for the applications that are hosted on a distributed system

-  A distributed system should:
	- make remote resources accessible (sharing)
	- hide that resources are distributed (transparency)
	- Be open such that components can easily used by others (openness)
	- scale to increasing numbers of users, resources, locations (scalable)
	- able to deliver reliable and correct processing (dependable)
	- resources, computations, and users are protected (secure)

### Databases
- Transactions
	- all or nothing semantics (everything succeeds, or transaction fails)
- ACID Properties
	- Atomic: to outside world, the transaction happens indivisibly
	- Consistent: The transaction does not violate system invariants (rules)
	- Isolated: Concurrent transactions do not interfere with each other
	- Durable: Once a transaction commits, the changes are permanent
![[Pasted image 20250422200227.png]]

### Sockets
- communication endpoint that an application can read/write data that will be sent/received from the network
- simple abstraction over a network port for a specific transport protocol
![[Pasted image 20250422201456.png]]
### Socket operations
- **socket** - create a new communication endpoint
- **bind** - attach a local address to a socket
- **listen** - tell OS max number of pending connection requests
- **accept** - block caller until a connection request arrives
- **connect** - actively attempt to establish a connection
- **send** - send data over the connection
- **receive** - receive some data over the connection
- **close** - release the connection
![[Pasted image 20250422201641.png]]

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

event-based systems
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

## Clients
- **thick clients**
	- remote server has a separate client that contacts the server over the network
		- Word, phone calendar, mySQL workbench
![[Pasted image 20250422205003.png]]
- **thin clients**
	- direct access to remote services with a single user interface
		- Google Docs, Dropbox, YouTube, Google Colab, Chrome
![[Pasted image 20250422205023.png]]

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
	- HTTPS == TCP 443
- can have a method for the client to discover the port

### Discovering a server
- daemon process used to register servers and clients must first ask for the port
![[Pasted image 20250422205223.png]]
- *superserver* manages creation of server on port within predefined range
![[Pasted image 20250422205500.png]]

### Transport layer: Transmission Control Protocol (TCP)
- TCP is a reliable stream delivery service which guarantees that all bytes received will be identical and in the same order as those sent
	- reliable, ordered, and error-checked
- TCP is a connection-oriented protocol
- TCP accepts data from a data stream, divides it into chunks, and adds a TCP header
	- creates a TCP segment
- Advanced features like flow control, congestion control, timeout-based retransmission
- TCP segments are placed within an IP datagram
![[Pasted image 20250410182136.png]]
### Transport layer: User Datagram Protocol (UDP)
- connectionless communication model with minimal protocol mechanisms
- provides integrity verification (checksums) of header and payload
- provides no other guarantees to upper layer protocol
- Features:
	- *transaction-oriented* - suitable for simple query-response protocols (DNS)
	- *provides datagrams* - suitable for modeling other protocols (IP tunneling, RPC)
	- simple and easy to build on without full protocol stack (DHCP)
	- *stateless* - suitable for large numbers of clients (streaming)
	- the lack of retransmission delays makes it suitable for real-time (VOIP)
	- Multicast uses it to broadcast info (service discovery)

RPC
- messaging over a network doesn't provide a great deal of transparency to most programs
- RPC aims to provide a familiar procedure call model by making calls look like they're happening locally
- one of the biggest challenges is marshaling parameters such that the two sides can understand the contents of the procedure call
![[Pasted image 20250410184827.png]]
### Parameter passing
- the client stub takes parameters, packs into a message and sends to the server stub
- The challenges:
	- server receives a stream of bytes from the client and may not have any info on their meaning
	- client/server might have different data representations (e.g. byte order)
	- client and server must agree on how they encode data:
		- how are basic data values represented?
		- how are complex data values represented?
- client/server need to properly interpret messages, transforming them into machine and network independent representations

### How do we deal with pointers?
- forbid them? (since machines don't share memory, pointers don't make a lot of sense)
	- often not feasible
- often references to fixed size data types or dynamic data types where size can be computed at runtime
	- we can copy the referenced data structure to a "flat" param
- more complicated types (e.g. user-defined classes) are more difficult
	- ideally the language system can handle marshaling/unmarshaling of data
	- complex, big, nested objects might make this impossible or impractical

### RPC Process: 1) agree on message format
`void someFunction(char x; float y; int z[5])`
- assume all data types are 4 bytes
![[Pasted image 20250410185811.png]]

### RPC process: 2) agree on representation
- machines have different representation
	- ints are represented in two's complement
	- characters in 16-bit Unicode
	- floats in IEE 754
	- everything else in little endian
  ![[Pasted image 20250410185958.png]]

### RPC process: 3) agree on communication
- we must agree on the underlying protocols to be used:
	- TCP/IP
	- UDP + application specific reliability model
### Sockets
- transport layer provides a good base for messaging
- a *socket* is a communication endpoint that an application can write/read data that will be sent/received from the network
- simple abstraction over a network port for a specific transport protocol
- TCP or UDP
![[Pasted image 20250410190603.png]]
### ZeroMQ
- abstracts low-level socket implementations
	- same interface irrespective of the underlying OS
- provides a higher-level model by pairing sockets:
	- one for sending messages at process `P`
	- a corresponding one at process `Q` for receiving messages
- TCP-based, all communication is connection-oriented
- Allows many-to-one communication with sockets
	- server can listen to multiple ports
- all communication is asynchronous
	- The "MQ": enqueue connection requests and messages on sender side
#### Ex. ZeroMQ request/reply
- request-reply pattern
	- traditional client-server communication
	- a client application uses a **request socket**(of type REQ) to send a request
	- the server uses a **reply socket** (of type REP) to reply
- advantages:
	- simplifies development by avoiding need to call listen or accept
	- when a server receives a message, a subsequent call to send is automatically targeted toward the original sender
	- when a client calls the recv operation (for receiving a message) after having sent a message, ZeroMQ assumes the client is waiting for a response from the original recipient
#### Ex. ZeroMQ Pub-Sub
- publish-subscribe
	- client subscribes to messages that are published by the server
		- server defines topics that the client can filter on
	- supports multicast from server to many clients
	- server uses PUB socket, the client uses SUB sockets
![[Pasted image 20250410191049.png]]


### Message-Oriented Middleware (MOM)
- unlike previous point-to-point communication, message-oriented middleware systems support asynchronous communication using intermediate storage
	- sender/receiver need not be online at the same time
	- message delivery is therefore targeted at larger latency (minutes/hours rather than milliseonds)
- asynchronous persistent communication is provided through middleware-level queues
	- queues correspond to buffers at communication servers
#### General Model
- applications communicate by inserting messages in specific queues
- messages forwarded over a series of connected servers
	- in practice, src-dst are normally connected and the queue is on the dst
- generally, sender guaranteed that the message will eventually be inserted in destination's queue
	- nothing about when/if it will be read
- message stays in the queue until removed
![[Pasted image 20250410191846.png]]
#### General architecture
- queues are managed by *queue managers*
	- separate process or implemented as a library linked with the application
- application can only put in local queue or retreive from local queue
	- messages must contain destination address
	- queue managers are responsible for routing messages
![[Pasted image 20250410192057.png]]

###### Ex. Advanced Messaging Queuing Protocol (AMQP)
- many different message queuing systems:
	- IBM MQ, Rabbit MQ, Active MQ, Qpid
- given message queues are used for interoperation it is sub-optimal if you're locked into a message queue system
- AMQP is an effort to create a standard protocol
![[Pasted image 20250410192939.png]]
### AMQP Communication model: Transferring a message
- Sender:
	- message assigned unique ID and recorded locally in unsettled state
	- stub transfers the message to the server (receiver)
	- AMQP stub also records it as being in an unsettled state
	- Receiver server-side stub passes it to the queue manager
- Receiver (queue manager):
	- handles the message and normally reports back to its stub that it is finished
		- it has actioned the message
	- the stub passes this information back to the original sender
	- Sender: message enters a settled state
- AMQP stub of the original sender now tells the stub of the original receiver that message transfer has been settled
	- the original sender will forget about the message as the receiver has processed
	- receiver's stub can now also discard anything about the message, formally recording it as being settled as well

### AMQP messaging
- layered on top of underlying communication protocols, i.e. TCP/IP
- messaging takes place between two nodes
	- producer, consumer, or a queue (store/forward messages)
- receiver can indicate to the sender whether its message was accepted or rejected
	- notification was sent to original sender
- Persistence
	- mark a message as durable: indicate that intermediate nodes (e.g. queue) can recover in case of failure (nodes that can't will reject the message)

## Why don't we just use addresses?
- we want *location independence*
	- a name that is independent from its address
		- flexibility and ease of use
			- addressing, migration (movement), replacement
## Central theme: resolve names to addresses
- in principle, a *naming system* maintains name-address bindings
	- simply a table (name to address mappings)
	- will this work in a distributed system?
		- unlikely when you consider spanning large and wide networks with many resources
- what we aim to do is distribute:
	- the name-address table
	- the mechanism by which names are resolved into addresses

## Constrained names: identifiers and pure names
- a *true identifier* is a name that has the following properties:
	- an identifier refers to at most one entity
	- each entity is referred to by at most one identifier
	- an identifier always refers to the same entity (it is never reused)
- a *pure name* is a name that has no meaning
	- a MAC address provides no indication of where it is
	- a DOI (should) provide no notion of what the name is
	- looser requirements, a pure name can be an identifier
- it is much easier to unambiguously refer to an entity

## Flat naming
- unstructured sequence of characters
	- no info is encoded in the name that can help us identify the entity
		- SSN, MAC address

### How do we resolve flat names? simple solution: Broadcasting
- if we happened to have efficient broadcast, we could:
	- send a message to each machine with the desired name
	- look up local name to see if the message is for me
	- only reply if I can offer an access point to that entity
		- Address Resolution Protocol works like this
- broadcasting is not efficient as network sizes grow
	- network bandwidth wasted
	- hosts interrupted by unnecessary requests, no global knowledge of the entire system
#### Simple solution: forwarding pointers
- when an entity moves from A -> B what should we do?
	- look up address each time
	- leave behind a reference to its new location
- how would this work with remote objects (using RPC)?
	- each forwarding pointer is implemented as a (client stub, server stub) pair
	- server stub is a local reference to an entity, or a reference to a remote client stub
	- when an object moves, it leaves behind a client stub on A and installs a server stub on B

![[Pasted image 20250423161744.png]]

### Pros and cons
- pros:
	- simplicity: as soon as the entity is located a client can look up the current address by following the chain of references
- cons:
	- chains can become long and expensive to resolve
	- intermediaries must maintain their part of the chain as long as needed
	- vulnerability to broken links as one missing link will mean the entity cannot be located

### How can we efficiently distribute name resolution? Distributed Hash Tables
- a DHT is a decentralized and fault-tolerant distributed system
	- stores key:value pairs, can be efficiently added/retrieved
- P2P model, all processes are equal
	- nodes are processes, edges are comms channels
	- nodes can be added/removed with minimal effort and content redistribution
- typically a structured overlay network, i.e. a ring
- nodes are assigned random m-bit identifiers
- we need to decide which node to store a piece of data on
	- each data items gets hashed to an m-bit key
	- `key(data) = hash(data_value)`
	- data is stored with key k on node with smallest `id >= k`
	- this node is called on the *successor* of k and denoted `succ(k)`
![[Pasted image 20250423162225.png]]
#### Name resolution in Chord: naive
- how can we efficiently resolve key `k` to the address `succ(k)`?
- easy solution: each node `p` should keep track of the successor node `succ(p+1)` and its predecessor `pred(p)`
- we can now apply a linear search
	- when receiving a request to resolve a key it will be forwarded to one of its two neighbors (unless the key belongs to that node)
- works, but is less efficient...
![[Pasted image 20250423162237.png]]
#### Finger tables
- each node maintains a finger table (shortcuts) of size s
	- where `s <= m` entries
		- ![[Pasted image 20250423162302.png]]
		- intuition: the `i`th entry points to the first node succeeding p by at least `2^(i-1)`
		- to look up a key `k`, node `p` will immediately forward the request to a node `q` with index `j` in `p`'s finger table:
			- ![[Pasted image 20250423162414.png]]
![[Pasted image 20250423162428.png]]

### Finding a key
![[Pasted image 20250423162454.png]]
- with the above finger table...
- let's assume node 1 is looking to identify a key `k` equal to 7....
![[Pasted image 20250423162541.png]]
- node `1` will first check whether it is storing a copy of the resource locally
- if it is not, it will forward it to the largest node in its finger table that does not exceed the value of `k`
	- in this instance, the highest node is `5`
	- once node `5` receives the query, it will perform the same process as node `1` and forward the data to node `7`, which stores the data
- to use a second example where the value of the key `k` is not the id of a node in the finger table:
![[Pasted image 20250423163105.png]]
- let's assume node `1` receives a request to locate a resource with key `k` equal to 12
	- 1 will check its own node, determine it does not have a copy of the resource, and will then reference its finger table to determine where to forward the request
		- `11` is the node with the highest id lower than the value of `k`, so this request is forwarded to node `11`
	- `11` will check whether it stores a local copy of the resource, determines that it does not, and will look up the successor in the finger table
		- in contrast to our previous example, it will see that there is no entry in the table for the value of `k`, nor is there a successor lower than the value of `k` to send the request to
			- in this case, the node will forward the request to the lowest node greater than the value of `k`, namely `13`
				- node `13` will contain the resource referred to by the key and will process the request from there
### Joining the network
- in large distributed systems, the collection of nodes can change frequently:
	- nodes may join or leave the network
	- nodes may fail (same as leaving) and recover (same as joining)
- complexity comes from **keeping finger tables up to date**
	- every node's `FT[1]` must be correct as it refers to the next node in the ring
		- if this ever becomes wrong, the DHT will not function properly
- joining is easy, if node *q* wants to join the system they:
	- contact an arbitrary node and request lookup for `succ(q+1)`
	- once identified, `q` can insert itself into the network, by telling `succ(q+1)` that it is now its predecessor
![[Pasted image 20250423164244.png]]
- in this example, node `10` inserts itself in the ring. It contacts a random node in the network, and asks for `succ(10+1)`, which in this case is 11
	- node `10` can then contact node `11` and notify it they these nodes are the successor and predecessor of each other
		- one flaw: this process does not notify node 7
			- it doesn't know it's `succ(q+1)` has been updated
### updating pointers and finger tables
- each node q frequently runs the following procedure:
	- contacts `succ(q+1)` and requests that node returns `pred(succ(q+1))`
		- this means the node is contacting it's successor and asking for its predecessor
			- this *should* be node `q`
	- if `q = pred(succ(q+1))` then we know information is consistent
	- if not, then a new node has entered the system
		- `q` can update its finger table to point to the new node
![[Pasted image 20250423164854.png]]
- in this example, node `7` undergoes this process
	- it contacts `succ(q+1)`, and requests its `pred`
		- if node `10` has fully inserted itself into the network, node `11` 's successor will be `10`
			- when node `7` learns that the predecessor of its successor is `10`, it will be able to deduce that it has a new successor (in this case, `10`) and will update its finger tables accordingly
### leaving the network
- easy, no need to do anything
- each node `q` will regularly check if its predecessor is alive
	- if it doesn't, it sets the `pred(q) = 'unknown'`
	- when the node checks its link to the next node, it won't reply
	- then check `succ(q+1)` where predecessor is unknown, then notify `succ(q+1)` that it suspects it to be the predecessor
![[Pasted image 20250423165308.png]]
- in this example, node `7` leaves the Chord
- node `10` will regularly check in and ensure it's predecessor is alive
	- the next time it attempts to check in on `7`, it will discover `7` is no longer active and set it's predecessor is unknown
	- the next time node `5` checks its successor, it will not receive a response (since `7` has left the network)
		- when this happens, the node `5` will contact `succ(q+1)`, in this case `10` that it suspects that it is that node's predecessor
			- nodes `5` and `10` will then update their corresponding predecessors and successors to re-establish the full ring

## Structured naming
- structured names include some info that can help us locate entities
- systems generally support structured names that are composed from simple, human-readable names
	- contain structural separators
	- filesystem, DNS names
### Namespaces
- names are organized into *namespaces*
- namespaces can be represented as a *naming graph*, a directed graph with two types of nodes:
	- *directory node* has outgoing edges (labeled with a name)
		- stores a *directory table* with outgoing edges represented as a pair
	- *leaf nodes* represent the entity (no outgoing edges)

### Name Resolution
- a path name is used to refer to a node in the naming graph
- a path name is represented by a sequence of edge labels separated by a slash
	- an absolute path name starts from the root node and begins with a slash
	- a relative path name does not start at the root node and does not begin with a slash
- given a path name we can look up information in order:
	- start at root node, lookup labels in order
![[Pasted image 20250423170913.png]]

### Alias
- an *alias* is another name for the same entity
- several ways to implement
	- **hard link**: allow multiple absolute path names to refer to the same node
	- **soft link**: allows a leaf node to contain the name of a differnet node
### Mounting a namespace
- name resolution can be used to merge different namespaces by **mounting** the node identifier of one namespace in a node in the current namespace
	- *foreign name space* - the name space that needs to be accessed
	- *mount point* - the node in the current namespace containing the node identifier of the foreign name space
	- *mounting point* - the node in the foreign namespace where to continue name resolution
- in order to mount over a network, we need:
	- the name of an access protocol
	- the name of the server
	- the name of the mount point

### Namespace implementation
- implementing the naming model is easy, how do we implement the naming system?
	- *availability* the most important, followed by *performance*
- **aim**: distribute the name resolution process across several machines called *name servers* by distributing nodes of the naming graph
- how to implement the name resolution process?
	- two basic approaches: **iterative** and **recursive**

#### Iterative name resolution
- resolver hands over complete name to the root name server using a well-known address
- root server will resolve path as far as it can and return to the client
	- the client will then contact the next name server
- **pros**: intuitive, traceable, load balanced
- **cons**: heavy work for the client, caching is difficult (as it's the clients, not the server's responsibility)
![[Pasted image 20250423171124.png]]
#### Recursive name resolution
- the client requests resolution from the root name server
	- the root name server calls the next name server, which calls the next name server after performing the same process and so on, until the final name server returns the result
- **Advantage**: caching is more effective, communication costs reduced
- **Disadvantage**: additional load on name servers
![[Pasted image 20250423171243.png]]

## Domain Name System
 - hierarchically organized and structured namespace with each node having exactly one incoming edge
	 - labels are case-insensitive strings of max length 63 characters
		 - longest path name is 255 characters
	- label's separated by dots
	- a subtree is called a *domain*
	- a path name to a domain's root node is called a *domain name*

## DNS Resource records
- contents of a node is formed by a collection of resource records
	- nodes most often represent **several** entities at the same time
![[Pasted image 20250423171328.png]]

## DNS Implementation
- each zone is implemented by a name server
	- typically replicated for availability
	- updates for the zone are normally handled by a **primary** name server
		- hosts a local database
	- secondary name servers request the primary server to transfer its content
		- called a **zone transfer**
- a DNS databased is implemented as a collection of files, one of which contains all the resource records for nodes in that zone

## LDAP (Lightweight Directory Access Protocol)
- common approach for distributed directory services is to combine structure naming with attribute-based naming
	- most systems build on LDAP (or utilize it directly)
- LDAP derived from the OSI X.500 directory service
### Conceptual model
- contains records (or directory entries)
	- similar to a DNS resource record
- Records are made up of a collection of (attribute, value) pairs
	- attributes have an associated type
	- single-value vs. multi-valued attributes
![[Pasted image 20250423171737.png]]

### LDAP Naming
- each record is **uniquely named** so it can be looked up
- expressed as a series of naming attributes
	- each attribute is a relative distinguished name
	- e.g. Country, Organization, and Organizational Unit can form a unique name
		- `DN => /C=US/O=UChicago/OU=Computer Science`
- like DNS, you can form a naming tree by stepping through the hierarchy
- unlike DNS, you can query a specific record
	- `search(‘‘(C=US)(O=UChicago)(OU=*)(CN=Main server)’’)`
