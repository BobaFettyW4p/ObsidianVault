- Interprocess communication is at the heart of all distributed systems
	- makes no sense to study distributed systems without carefully examining the ways processes on different machines exchange information
	- traditionally based on low-level message passing in the underlying network
		- expressing this is more difficult than using primitives based on shared memory
			- this is what is normally used in nondistributed systems
- the first model for communication in distributed systems is the *Remote Procedure Call (RPC)*
	- aims to hide most of the intricacies of message passing
	- ideal for client/server communication
- many distributed systems do not adhere to a client/server model
	- in these cases, it is more appropriate to think in terms of messages
	- when the low-level communication facilities of computer networks are not suitable, a high-level queuing model can be utilized
## Foundations
### Layered protocols
- all communication in distributed systems is based on sending and receiving low level messages
#### OSI Reference model
- Open Systems Interconnection Reference model
	- OSI model for short
		- designed to allow open systems to communicate
			- an open system is one that is prepared to communicate with any other open system using standard rules that government the format, contents, and meaning of the messages sent and received
				- these rules are formalized into *communication protocols*
					- a protocol is said to provide a *communication service*
						- before exchanging data the sender and receiver establish a connection and negotiate parameters of the protocol to be used
							- when finished, the connection is terminated
						- a *connectionless service* requires no setup in advance
![[Pasted image 20250415194011.png]]
- *physical layer* - deals with standardizing how two computers are connected and how 1s and 0s are represented
- *data link layer* - provides the means to detect and correct transmission errors, as well as protocols to keep a sender and receiver in the same place
- *network layer* - contains the protocols for routing a message through a computer network, as well as protocols for handling congestion
- *transport layer* - contains protocols for directly supporting applications, such as those that establish reliable communication, or support real-time streaming of data
- *session layer* - provides support for sessions between applications
- *presentation layer* - prescribes how data is represented in a way that is independent of the hosts on which communicating applications are running
- *application layer* - everything else

- the collection of protocols used in a particular system is called a *protocol suite* or *protocol stack*

#### Middleware protocols
- middleware logically lives mostly in the OSI application layer, but contains many general-purpose protocols that live in other layers
- *Domain Name Service (DNS)* - used to look up a network address associated with a name
	- such as the address of a *domain name*
	- "lives" in the application layer, but provides a general-purpose application-independent service
		- can be argued to form part of the middleware
- there are various ways to establish *authentication*
	- provide proof of identity
	- authentication protocols are not closely tied to any specific application but can be integrated into a middleware system as an integrated service
- *authorization* protocols
	- the process by which authenticated users and processes are granted access to the resources they have been permissioned for, tend to have a general, application independent nature
- *distributed commit protocols* establish that, in a group of processes, either all processes carry out a particular operation, or the operation is not carried out at all
	- known as *atomicity*
		- widely utilized in transactions
- *distributed locking protocol* - a resource can be protected against simultaneous access by a collection of processes distributed across multiple machines
	- again, belong in middleware
![[Pasted image 20250415204200.png]]
### Types of Communication
- *persistent communication* - a message will be stored by middleware for as long as it takes to deliver to the receiver
	- not necessary for the sending application to persist past sending the message
- *transient communication* - the message is stored by the communication system only as long as the sending and receiving application are executing
	- if the middleware cannot deliver a message due to transmissions interrupt or because the recipient is not active, the message will be discarded
- *asynchronous communication* - a sender continues immediately after it has submitted its message for transmission
	- the message is stored by the middle ware upon transmission
- *synchronous communication* - the sender is blocked until its request is known to be accepted
	- three points where synchronization can take place:
		- the sender may be blocked until the middleware notices it will take over transmission
		- the sender may synchronize until its request has been fully processd
		- the sender may waut until its request has been fully processed
## Remote Procedure Call
- many distributed systems have been based on explicit message exchange between processes
	- send and receive messages do not conceal communication at all
		- important for access transparency
- a *remote procedure call* allows programs to call procedures located on other machines
### Basic RPC operation
- the goal is to make an RPC look as much like a local one as possible
	- the calling procedure should not be aware the caller is a different machine
	- RPC achieves this by offering a *client stub* to the client
		- when the message arrives at the server, the server passes it to a *server stub*
			- a server stub is a piece of code that transforms requests coming in over the network into local procedural calls
				- the server stub unpacks the parameters from the message then calls the server procedure in the usual way
					- from the server's perspective, it's as if it's being called directly
					- the server stub will then pack the result and send it back to the client
![[Pasted image 20250416071134.png]]
### Parameter passing
- the client stub takes parameters, packs them into a messages, and sends it to the server stub
	- packing parameters into a message is called *parameter marshaling*
		- messages must be able to be correctly interpreted by the server
			- just a series of bits, must be decoded before they can be used
		- also need to account for the difference in placement of memory
			- Intel processors number their bytes from right to left
				- this is called *little endian*
			- ARM processor number their bytes from left to right
				- this is called *big endian*
			- big endian is typically used to send bytes across the network
				- but mostly, data is transformed to a machine and network-independent format and both parties expect to translate the data before sending/receiving
- How are pointers passed?
	- only with the greatest of difficulty if at all
		- meaningless on a remote system that doesn't have access to the block of memory the pointer "points" to
	- it's possible to forbid pointers and reference parameters
		- they're so important this is generally undesirable
		- also often not necessary
			- many pointers point to fixed-size data types where it's trivial to calculate their size at runtime
				- means we can copy the entire data structure
### RPC-based application support
- RPCs require the caller and callee to agree on the format of the messages they exchange
	- if they don't, the RPC will not work properly
- there are two ways RPC-based app development can be supported:
	- let the developer specify exactly what needs to be called remotely
	- embed RPC calling as part of a programming-language environment
#### Stub generation
![[Pasted image 20250416073823.png]]
- defining the message format is one aspect of an RPC protocol, but we also need the client and server to agree on the representation of even simple data structures
- if encoding rules can be pinned down to the last bit, we would have to have the caller and callee agree on the actual exchange of messages
	- can use a connection-oriented transport service like TCP/IP
		- can also use a unreliable datagram service and let client and server implement an error control scheme
- once the protocol has been fully defined, the client and server stubs need to be implemented 
	- stubs for the same protocol, but different procedures, normally only differ in their interface to the applications
		- an interface is a collection of procedures that can be called by a client, which are implemented by a server
			- an interface is usually available in the same programming language as the one in which the client or server is written
				- this is not strictly necessary
				- interfaces are often defined through an *Interface Definition Language (IDL)* to simply matters
![[Pasted image 20250416074752.png]]
- this is the situation for compiled languages, but interpreted languages operate very similarly
- it is also possible for the client and server code to be written in different languages
#### Language-based support
- our discussions have been largely-programming language agnostic up until now, but RPCs can also be directly embedded into a programming language
	- main benefit of this is that app development becomes much simpler
	- also much easier to achieve a high degree of access transparency
- a well known example of this is fully embedded Java, where an RPC is referred to as a *remote method invocation*
### Variations on RPC
- when a client calls a remote procedure, the client blocks until a message is received
	- unnecessary when there is no result to return
	- can be inefficient when multiple RPCs are involved
#### Asynchronous RPC
- designed to support situations where there is no result to return to the client
- the server sends a reply back to the client when the RPC is received, then processes it locally
	- the reply is an acknowledgement that it was received and will be processed
		- the client can proceed as soon as this is received
- also useful when a reply will be returned, but the client is not prepared to wait and do nothing until the reply is received
![[Pasted image 20250416200440.png]]
- this may be useful for a computer that needs to query many servers at once
	- can reach out to each one independently and once the reply is received, they can query the next, and collect all the output at the end
		- also known as a *deferred synchronous RPC*
- there are also asynchronous RPCs where the client does not wait for the server to respond
	- referred to as *one-way RPCs*
#### Multicast RPC
- asnychronous and deferred synchronous RPCs facilitate another alternative
	- executing multiple RPCs at the same time
- sends an RPC to a group of servers:
![[Pasted image 20250416200817.png]]
- several issues:
	- the client application may not be aware that the RPC is being forwarded to more than one server
	- need to consider how responses will be handled
		- will the client proceed after all responses are received? or just wait for one?
## Message Oriented communication
- RPCs and remote object invocations contribute to hiding communication in distributed systems
	- enhance *access transparency*
	- cannot assume either of these methods will be suitable in all situations
		- when they are not, need to find another way
### Simple transient messaging with sockets
- many distributed systems are built directly on top of the message-oriented model offered by the transport layer
- a *socket* is a communication end point to which an application can write data to be sent over the underlying network and from which incoming data can be read
- introduced in the 70s at Berkely, adopted as a POSIX standard
	- it is an abstraction over the actual port used by the OS for a specific transport protocol
		- socket operations:
![[Pasted image 20250416201609.png]]
- servers typically execute the first four operations
- `listen` is used only in the case of connection-oriented communication
	- nonblocking call that allows the local OS to reserve buffers for a specified number of pending connection requests
- a call to `accept` blocks the caller until a connection request arrives
	- when it does arrive, the local OS creates a new socket with the same properties as the original one, and returns it to the caller
- on the client side:
	- a socket must be created with the `socket` operation
		- explicitly binding the socket to a local address is not necessary
	- `connect` requires that the caller specifies the transport-level address to which a connection request is to be sent
		- the client is blocked until the connection has been set up successfully
			- then both sides can start exchanging information
- socket communication is symmetric
### Advanced transient messaging
- the standard socket-based approach is very basic and therefore brittle
	- easy to make mistakes
- sockets only support TCP or UDP
	- any extra facility for messaging needs to be implemented separately
#### Using messaging patterns: ZeroMQ
- many messaging applications can be effectively organizaed according to a few simple communication patterns
	- by providing enhancements for these patterns, it may become easier to develop a networked, distributed application
- ZeroMQ provides sockets through which all communication takes place
	- actual message transmissions takes place over TCP
		- all communication is connection-oriented
	- setting up and maintaining communication happens under the hood
	- a socket may be bound to multiple addresses
		- allows a server to handle messages from multiple different sources through a single interface
	- ZeroMQ sockets support *many-to-one* communication
		- they also support *one-to-many* communication
- an essential feature of ZeroMQ is that communication is *asynchronous*
	- a sender will continue after having submitted a message to the underlying communication subsystems
	- a process can request a connection setup and send a message even if the recipient is not yet ready to receive messages
		- they get queued on the sender's side while a separate thread sets up the connection and sends the messages to the recipient
- ZeroMQ establishes a higher level of abstraction by pairing sockets
	- a type of socket used for sending messages is paired with a socket used for receiving messages
- the three most important communication patterns supported by ZeroMQ:
	- *request-reply* is used in traditional client-server communication
		- a client uses a request socket (type `REQ`) to send a request message to a server and expects the latter to respond
		- The server uses a reply socket (type `REP`)
		- this simplifies matters for developers by avoiding the need to call `listen` and `accept`
		- when a server receives a message, a call to `send` is automatically targeted at the original sender
		- when a client calls `recv`, ZeroMQ assumes the client is waiting for a response from the original recipient
	- in a *publish-subscribe pattern*, clients subscribe to specific messages that are published by servers
		- only the messages to which the client has subscribed will be transmitted
		- the server is assumed to use a socket of type `PUB` while each client uses a socket of type `SUB`
		- each client socket is attached to the server socket
		- each client subscribes to no specific message by default
			- if no explicit subscription is provided, a client will not receive a message published by the server
	- the *pipeline* pattern is characterized by the fact that a process wants to *push out* its result, assuming there are not other processes that wany to *pull in* those results
		- a pushing process doesn't care what process pulls it in
			- similarly, the pulling process doesn't care what process is pushing to it
#### The Message-Passing Interface (MPI)
- developers have been looking for message-oriented operations that would allow them to write highly efficient applications
	- operations should be at a convenient level of abstraction
	- sockets were insufficient for two reasons:
		- sockets operate at the wrong level of abstraction
			- only support send and receive operations
		- sockets had been designed to communicate across network stacks using general-purpose protocol stacks such as TCP/IP
			- not suitable for high-speed interconnection networks
			- these situations need an interface that supports more advanced features
				- buffering and synchonization
- most high-performance supercomputers and interconnection networks were shipped with proprietary communication libraries
	- offer a wealth of high-level and generally efficient communication operations
		- all libraries were mutually incompatible
			- led to a portability problem
- the need to be hardware and platform independent led to the definition of a standard for message passing
	- called the *Message-Passing Interface (MPI)*
		- designed for parallel applications
			- tailored to transient communication
				- makes direct use of the underlying network
					- assumes that serious failures such as process crashes or network partitions are fatal and do not require automatic recovery
		- MPI assumes communication takes place within a known group of processes
			- each group is assigned an identifier
				- each process is assigned a local identifier
					- thus a `(groupID, processID)` pair uniquely identifies the source or destination of a message'
						- used instead of a transport-level address
						- may be several overlapping groups of processes involved in a computation that are all executing at the same time
		- the core of MPI are messaging operations to support transient communication
			- the middleware maintains its own buffers and synchronization
				- this is often related to when necessary data has been copied from the calling application to the middleware
![[Pasted image 20250416210205.png]]
- transient asynchronous communication is supported by the `MPI_BSEND` operation
	- the sender submits a message for transmission, which is first copier to a local buffer in the MPI runtime system
		- when the message has been copied, the sender continues
		- the local MPI runtime system will remove the message from its local buffer and take care of transmission as soon as a received has called a receive operation
	- `MPI_SEND` is a blocking send operation
		- semantics are implementation dependent
			- may either block the caller until the message has been copied to the MPI runtime system at the sender's side, or until the received has initiated a receive operation
	- when a sender calls `MPI_SENDRECV`, it sends a request to the receiver and blocks until a reply is received
		- corresponds to a normal RPC
	- `MPI_SEND` and `MPI_SSEND`have variants that avoid copying messages from user buffers to buffers internal to the local MPI runtime system
		- correspond to a type of asynchronous communication
			- sender automatically continues
		- to prevent overwriting, MPI offers operations to check for completion, or to block if required
	- `MPI_ISSEND` a send passes a pointer to the MPI runtime system
		- when it has processed the message, the sender is guaranteed that the sender has accepted the message and is now working on it
	- when `MPI_REVC` is called to receive a message, it blocks the caller until a message arrives
		- asynchronous variant - `MPI_IRECV`
	- MPI semantics are not straightforward and different operations can be interchanged without affecting the correctness of a program
		- gives MPI implementers enough leeway to maximize performance
### Message-oriented persistent communication
- message-oriented class of middleware services
	- known as *message-queueing systems* or *message-oriented middleware (MOM)*
	- message queuing systems provide support for persistent asynchronous communication
		- these systems offer intermediate-term storage capacity for messages, without requiring either the sender or received to be active during message transmission
			- message-queuing systems are typically targeted to support message transfers that take minutes instead of seconds or milliseconds
#### Message-queuing model
- basic idea: applications communicate by inserting messages in specific queues
	- these messages are then forwarded over a series of communication servers and eventually delivered to their destinations
		- each application has a private queue to which other applications can send messages
			- a queue can be read only by its own application, but multiple applications can share a queue
	- a sender is generally only given the guarantee that its message will be inserted into the queue, not that the application will read it
		- permits communication to be decoupled in time
			- once a message has been deposited in a queue, it will remain there until it is received
![[Pasted image 20250416211712.png]]
a) both the sender and receiver are in execution during the entire transmission of a message
b) only the sender is in execution, while the receiver is passive
- message delivery is not possible, but sending is
c)the sender can read messages that were sent to it, but the senders are not in execution
d) the system is storing messages while both the sender and receiver are passive
- if this is supported, the message-queuing system provides persistent messaging

- messages can contain any data
	- from the middleware's perspective, it's more important that messages are properly addressed
		- provide a systemwide unique name for the destination queue
	- message size may be limited, but the underlying system may also take care of fragmenting and assembling
	- this leads to the interface being offered to applications having the potential to be simple:
![[Pasted image 20250416212219.png]]
#### General architecture of a message-queuing system
- first, queues are managed by *queue managers*
	- a queue manager is either a separate process or is implemented through a library that is linked with an application
	- as a rule of thumb, an application can only put messages into a local queue
		- similarly, it's only possible to extract messages from a local queue
	- if all queue managers are linked into their respective applications, it's no longer a persistent asynchronous messaging system
- if applications can only put messages into local queues, then each message will have to carry info regarding its destination
	- the queue manager's task is to ensure messages do reach their destinations
- the destination queue
	- to enhance location transparency, it's preferable that queues have logical, location-independent names
		- if a queue manager is implemented as a separate process, each name should be associated with a *contact address*, such as a `(host, port)` pair and that name-to-address mapping is readily available to a queue manager
			- in practice, a contact address will contain more info
![[Pasted image 20250416212734.png]]

- we need to consider how name to address mapping is actually made available
	- a common approach is to simply implement mapping as a lookup table and copy that table to all managers
		- leads to a maintenance problem
			- every time a new queue is added, tables have to be updated
- problems related to efficiently maintaining name-to-address mappings
	- we assume that the contact address of each queue manager should be known to all others
		- when dealing with large message-queuing systems, we have a scalability problem
			- solution is to create special queue managers that operate as *routers*
				- routers forward incoming messages to other queue managers
			- over time, a message-queuing system will frow into a complete application-level *overlay network*
#### Message brokers
- important application is integrating existing and new applications into a single, coherent distributed information system
	- integration requires applications can understand the messages they receive
		- the sender must have its outgoing messages in the same format as that of the receiver, but its semantics must also match
			- sender and receiver must "speak the same language"
	- the main concern is each time an application is added with its own protocol, each other application needs to learn to communication
		- in a system with N applications, we need to translate `N*N` protocols
		- alternatively, all systems can agree on a common messaging protocol
			- this will not work for message-queuing systems due to the level of abstraction at which they operate
			- only makes sense if the collection of processes have enough in common to leverage it
		- the general approach is to learn to live with differences, and try to provide the means to make conversions as simple as possible
			- these conversions are handled by special nodes in a queuing network, called *message brokers*
				- a message broker acts as an application-level gateway in a message-queuing system
					- its main purpose is to convert incoming messages so they can be understood by the destination application
					- to a message-queuing system, a message broker is just another application
![[Pasted image 20250416213804.png]]
- message brokers can be as simple as a simple reformatter for messages
	- can also be a full application-level gateway in more advanced applications
- a message broker is used for advanced *enterprise application integration (EAI)*
	- a broker is repsonsible for matching applications based on the emssages being exchanged
		- in a publish-subscribe model, applications send messages in the form of publishing
			- they may publish a message on topic X, which is sent to the broker
				- applications subscribed to topic X will receive those messages from the broker
	- the heart of a message broker is a repository of rules for transforming messages between formats
		- problem of defining the rules and developing the plugins
### Ex. Advanced Message Queuing Protocol (AMQP)
- message-queueing systems were developed to allow legacy applications to interoperate, but we often hit a wall
	- message-queuing systems are in large part proprietary solutions
		- led to the foundation of *Advanced Message Queuing Protocol*
#### Basics
-  AMQP revolves around applications, queue managers, and queues
	- distinguish between AMQP as a *messaging service*, the *messaging protocol* and the *messaging interface* offered to applications
		- easiest to consider having only a single queue manager running as a single separate server forming AMQP as a service
![[Pasted image 20250416214545.png]]
#### AMQP communication
- AMQP allows an application to set up a *connection* to a queue manager
	- a connection is a container for a number of one-way *channels*
		- the lifetime of a channel can be highly dynamic, but connections are relatively stable
- bidirectional communication is established through *sessions*
	- a logical grouping of 2 channels
- to actually transfer messages, a *link* is needed
	- a link keeps track of the status of messages that are being transferred
		- provides fine-grained flow between an application and a queue manager
- when a message is to be transferred, the application passes it to the local AMQP stub
	- each message is associated with one specific link
	- transfer proceeds in three steps:
		- at the sender's side, the message is assigned a unique identifier and is recorded locally to be in an *unsettled state*
			- the sub transfers the message to the server, where the AMQP stub records it as being in an unsettled state
			- the receiving application is assumed to handle the message and reports back to the stub that it is finished. This info is passed back to the original sender, where it's AMQP stub enters a *settled state*
			- the AMQP stub of the original sender tells the stub of the original receiver that message transfer has been settled. The receiver's stub can discard anything about the message
- AMQP enables true end to end communiation reliability
#### AMQP messaging
- messaging in AMQP logically takes place one layer above the one handling communication
	- an application can indicate what needs to be done with a message, but also can see what has happened so far
- messaging formally takes place between two *nodes*, of which there are three types:
	- producer
	- consumer
	- queue
- an important aspect of AMQP is its support for *persistent messaging*
	- achieved through several mechanisms:
		- a message can be marked as `durable`
			- indicates that the source expects any intermediate node to recover the message in case of failure
				- any intermediate node that cannot guarantee this has to reject the message
			- a source or target node can also indicate it's level of durability
- AMQP is a messaging protocol in the sense that it does not by itself support publish-subscribe primitives
	- those are handled by more advanced queue managers
- no reason why a queue manager cannot be connected to another queue manager
	- quite common to create an overlay network where messages are routing from producers to their consumers
#### AMQP in practice
- one of the most popular AMQP implementations is RabbitMQ
	- important difference between versions is that of *exchanges*
		- instead of directly manipulating queues, producers contact an exchange, which places messages in one or several queues
			- an exchange allows a producer to use a synchronous or asynchronous RPC to a queue manager
				- an exchange never stores messages, it simply directs them to the appropriate queue
## Multicast communication
 *multicast communication* - sending data to multiple receivers
 - major issue has always been setting up communication paths
	 - does not necessarily mean creating explicit communication pahs
### Application-level tree-based multicasting
- nodes are organized into an overlay network
	- nodes are then used to disseminate information to its members
- network routers are not involved in group membership
	- connections may cross several physical links, and as such, routing messages may not be optimal compared to network-level routing
#### Performance issues in overlays
- although building a tree to organize nodes is not difficult, creating an efficient tree structure is much more complicated
- the quality of an application-level multicast tree is measured by three-separate metrics:
	- *link stress* - defined per link and counts how often a packet crosses thee same link
	- *stretch* - also known as *relative delay penalty (RDP)* - measures the ratio of the delay between two nodes in the overlay, and the delay those nodes would experience in the underlying network
	- *tree cost* - global metric generally related to minimizing the aggregated link costs
- to simply matters, assume a multicast group has an associated and well-known node that tracks nodes that have joined the tree
	- when a new node joins, it contacts this *rendezvous node* to obtain a list of members
		- the new node will then select a node to serve as its parent node
### Flooding-based multicasting
- when a message needs to be received by every node in the overlay network, this is corresponds to *broadcasting*
- multicasting generally refers to sending a message to a subset of all nodes, i.e. a specific *group of nodes*
	- key design issue is to minimize the user of intermediate nodes for which the message is not intended
		- one way to avoid this is to construct an overlay network per multicast group
			- downside: if a node is a member of multiple groups, they need to maintain a different list of neighbors for each group
### Gossip-based data dissemination
- also known as *epidemic behavior*
	- goal is to rapidly propagate info among a large collection of nodes using only local info
#### Information dissemination models
- a node is considered *infected* if it holds data that it is willing to spread to other nodes
	- a node that has not received this data is considered *susceptible*
	- an node that is not willing or able to spread its data is said to have been *removed*
- a popular propagation model is that of *anti-entropy*
	- a node P picks a node Q at random and exchanges updates according to three models:
		- P only pulls in new updates from Q
		- P only pushes its own updates to Q
		- P and Q send updates to each other
- when it comes to rapidly spreading updates, only pushing turns out to be a poor choice
	- chances are some nodes will remain susceptible for a long time because no infected node ever chooses them
- the pull model works better when many nodes are infected
	- odds are much higher a susceptible node will choose an infected node to pull updates from
- if only a single node is infected, updates will rapidly spread via any method, but push-pull is the best strategy
- one variant is *rumor spreading*
	- if P has been updated, it contacts another node and tries to push the update
		- P will repeat this until it finds another node that has received the update
			- at which point it will then become removed
	- an excellent way to transfer information, but cannot guarantee all nodes will be updated
- one of the main advantages is scalability
- nodes that are connected to only a few other nodes are contacted with a relatively high probability
	- these nodes form a "bridge" to remote parts of the network and should be contacted as soon as possible
		- referred to as *directional gossipping*
#### Removing Data
- epidemic algorithms are great for spreading updates, but using them to delete data is hard
	- deletion of a data item destroys all info on that item
		- when a data item is simply removed, it will eventually be returned back
			- the trick is to record the deletion of a data item as just another update and keep a record of that deletion
				- record this by spreading *death certificates*
					- these do eventually need to be cleaned up