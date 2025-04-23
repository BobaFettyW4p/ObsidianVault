# Agenda
- shallow dive into networking
- communication techniques: RPC
- communication techniques: message passing
	- Sockets
	- ZeroMQ
	- MPI
	- Message queues
- Communication techniques: multicast
## What is a network?
- a computer network links nodes and enables electronic communication between those nodes
- a network may be built on physical or wireless links
	- twisted pair (cat6), fiber-optic, wifi, cellular
- a collection of protocols that make it possible for nodes to send messages to each other
	- routing, reliability, security, performance
### Part 1: Networking
- the OSI model
	- a conceptual model with 7 layers, each encapsulated in the lower one
- Network protocols are developed in layers, each packet goes inside the lower layer
- network layer - protocols for routing a message through a computer network, as well as protocols for handling congestion (IP)
- transport layer - protocols for directly supporting applications, e.g. those that establish reliable communication, or support real-time streaming of data

### Assembling messages in the OSI model
- encapsulated in a header
	- a packet is a header and a payload
![[Pasted image 20250410181311.png]]
- headers are consumed in the opposite order that they are constructed
### Network layer: Internet Protocol (IP)
- aim to deliver (route) packets from source to destination based on the **ip address** in the header
- encapsulates data into *datagrams*
	- supports fragmentation and reassembly
- Responsible for routing packets through a network
- best-effort delivery (unreliable)
	- connectionless protocol
		- doesn't establish a connection between the source and the destination
		- data corruption, packet loss, duplication possible
		- routing is dynamic, each packet may take a different path and may arrive out of order
		- checksum to indicate the packet header is not corrupted
			- data is fed into a hash function, if it matches the checksum, your data has not been compromised
### IPv4 and IPv6
- IPv4 uses 32 bit addresses
	- 4 8 bit octets
	- last top level address allocated in 2011
- IPv6 uses 128 bit addresses
	- not interoperable with IPv4, but both coexist together
	- addresses are represented as eight groups of four hex digits (128 bits)
	- new packet format
		- to minimize header processing
	- many other advantages
		- IPSec
		- multicasting
		- stateless address autoconfig
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
### Classification of message: receiver
- from the receiver's perspective, what happens to the message?
- **Persistent*
	- message is stored by communication middleware until receiver is ready to receive it
		- i.e. email
- **Transient**:
	- message must immediately be received by the sender once it reaches the communication middleware, or else it is discarded

### Classification of messages: sender
- the sender can have the following behaviors in the presence of either persistent or transient messages:
- **Asynchronous**
	- the sender sends the message and immediately unblocks to send another message
- **Synchronous**
	- the sender sends the message and blocks until:
		- message received by middleware
		- message received by receiver
		- message fully processed by receiver, and response received at sender

### Fastest way to move data
- Sneakernet
	- transfer data by physically moving media
- Amazon snowball
	- petabyte-scale data transport that uses specialized devices to transfer large amounts of data into the cloud

# Part 2: Communication Techniques: RPC

## RPC
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

## Communication techniques: message passing
### RPC is great, but it blocks
- RPC is inherently *synchronous* and *transient* by default
	- other techniques allow asynchronous and persistent behavior
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
### Message Passing Interface (MPI)
- supercomputers comprised of thousands of nodes with high-speed interconnects
	- each node is a separate machine (low latency to neighbors)
	- how do we effectively communicate amongst these machines?
- sockets?
	- level of abstraction is low (read/write), ideally higher-level primitives are needed for applications
	- not optimized for high-speed networks
	- opening sockets for thousands of nodes is often not allowed
	- sharing sockets is complicated
- HPC machines shipped with their own communication libraries
	- need a standard way to communicate == MPI (really need a way to build tightly couple applications)
- MPI
	- message passing on parallel architectures
		- each process has own memory space
	- high scalable for building large-scale distributed applications
	- optimized for cluster interconnects
	- little fault tolerance
	- requires specialized MPI executors
		- mpiexec, mpirun
![[Pasted image 20250410191535.png]]
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
#### Challenges
- how do we address a queue?
	- ideally use logical, location-independent names
	- need a mapping from logical name to physical address (host/port)
- how do we relay the name/address mapping to the queue manager?
	- copy the mapping table to all queue managers?
- does every queue manager need to know all the address of all other queue managers?
	- scalability and synchronization issues as size increases
	- routing models allow queue managers to know about their neighbor and they can forward
#### Challenges with MOM
- message queues are often used in situations where the applications are different
	- servers as a common layer for interoperability
- requirement of messaging is that all participants speak the same language
	- protocol and message format
	- how do we integrate new and old applications?
- why not just update each time we add a new application?
	- all other applications need to be updated to understand the message
	- with N messages, we have N * N protocol converters
- why not agree on a common message?
	- only makes sense if the collection of processes that use the protocol are very similar
- why not use a common representation of messages?
	- this is a good basis, but doesn't solve all problems (vocabulary)
	- messages in self-describing XML or JSON formats

##### Solution: Message broker
![[Pasted image 20250410192659.png]]
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

# Multicast
- many applications require sending data to multiple receivers
	- content distribution, P2P, location services, distributed analyses
- Unicast
	- directed data transfer from **point to point**
- Broadcast
	- data transferred from point to **all** other points
- Multicast
	- data sent from point to a **set** of other points
### Application level multicast
- basic idea is to organize nodes of a distributed system into an overlay network and use that network to disseminate data:
	- a tree, leading to unique paths between every pair of nodes
	- a mesh network, in which every node has multiple neighbors and there are multiple paths between every pair of nodes
		- provides better robustness but requires a form of routing
### Performance issues with overlay networks
- building an overlay network is not complicated...
	- building an efficient overlay network is a little harder
- e.g. overlay with 5 nodes connected to various routers
	- multicast overlay might duplicate path communications(A-B) while sending between nodes in the tree (should not be a link between B and E)
![[Pasted image 20250410193911.png]]
### Metrics to compare performance
- link stress
	- counts how often a packet crosses the same link
- relative delay penalty
	- ratio in delay between two nodes in the overlay to the delay in the underlying network
	- captures the inefficiency of the overlay network
- tree cost
	- global metric that aims to find a minimal spanning tree in which total time for dissemination to all nodes is minimal
### Setting up overlays
- if we are trying to multicast to a subnet of nodes, then tree overlays result in nodes sending and storing messages they have no interest in
	- one solution: construct an overlay for each multicast group and broadcast to that group
		- nodes belonging to several groups would need to maintain separate lists for each
	- how do we send messages?
		- naive approach: flooding
			- forward a message to each of your neighbors and ignore duplicates
### Gossip-based (epidemic) dissemination
- aim is to spread information
	- node is *infected* if it contains the data to be spread
	- nodes that have not yet seen the data is *susceptible*
	- only nodes who have not been infected yet can receive data
- General model:
	- update operations are performed at a single server
	- a replica passes updated state to only a few neighbors
	- update propagation is lazy (i.e. not immediate)
	- eventually, each update will reach every replica

