- Distributed systems are often complex pieces of software with components dispersed across multiple machines
	- systems must be organized well to master their complexity
- The organization of a distributed system is mostly about the software components that constitute the system
	- *software architectures* tell us how the various software components are organized and how they should interact
		- the final instantiation of software architectures are also called *system architecture*

# Architectural Styles
- an *architectural style* is formulated in terms of components
	- the way the components are connected
	- the data exchanged between components
	- how these elements are jointly configured into a system

- a *component* is a modular unit with well defined required and provided *interfaces* that is replaceable within its environment
	- a component can be replaced and the system will continue functioning
- a *connector* is a mechanism that mediates communication, coordination, or cooperation among components

## Layered architectures
- components are organized in a layered function where a component at a layer can make a *downcall* to a component at a lower-level layer and expects a response from that lower layer

![[Pasted image 20250408195708.png]]

### Layered communication protocols
- in *communication protocol stacks*, each layer implements *communication services* that allows data to be sent from a destination to one or several targets
	- each layer offers an *interface* specifying the functions that can be called
		- in principle, the interface should hide the actual implementation of a service
![[Pasted image 20250408195912.png]]
- a good example is *Transmission Control Protocol (TCP)*

Ex. 2 connecting parties
![[Pasted image 20250408202343.png]]

### Application layering

- many people have advocated for 3 logical layers:
	- application interface level
	- processing level
	- data level
![[Pasted image 20250408203152.png]]

## Service-oriented architectures
- one of the drawbacks of a layered architectural style is the strong dependency between layers
	- a response has been an architectural style reflecting a more loose organization into a collection of separate, independent entities
		- each of these encapsulates a service
			- can be called *services*, *objects*, or *microservices*

### Object-based architectural style
![[Pasted image 20250408203629.png]]
- each object corresponds to what we would consider a component
	- these components are connected via a procedure-call mechanism
- object based architectures are attractive because they provide a natural way of *encapsulating* data (the *state* of an object)
- the *interface* offered by an object conceals implementation details
	- can consider an object fully independently of its environment
		- allows us to place an interface on one machine, while the object resides on another
			- *distributed object/remote object*
![[Pasted image 20250408204227.png]]
- when a client binds to a distributed object, the remote interface, called a *proxy* is loaded into the client's address space
	- analogous to a *client stub* in RPC systems
		- also called a *skeleton* as it provides the bare means for letting server middleware access user-defined objects

### Microstructure architectural style
- key work here: *encapsulation*
	- the service as a whole is realized as a self-contained entity, can also make use of other services
- if you separate services so they can operate fully autonomously, you pave the road towards *service-oriented architectures (SOAs)*
	- each microservice runs as a separate network process
		- each can be a remote object, but not a requirement

### Coarse-grained service composition
- in a service-oriented architecture, a distributed application or system is essentially constructed as a composition of many service
	- may not all belong to the same administrative organization
- one of the big issues is that of *service composition*, and ensuring those services operate in harmony
	- each service MUST offer a well-defined interface

### Resource-based architectures
- one of the problems with service composition is that connecting various components can easily become an integration nightmare
- alternatively, you can view a distributed system as a huge collection of resources that are individually managed by components
- resources may be added or removed by remote applications and likewise can be retreived or modified
	- *Representational State Transfer (REST)*
		- there are four key characteristics of RESTful architectures:
			- resources are identified through a single naming scheme
			- all services offer the same interface consisting of at most 4 operations
			- messages sent to or from a service are fully self-described
			- after executing an operation at a service, that component forgets everything about the caller
				- *stateless execution*
![[Pasted image 20250408205537.png]]
eg. AWS S3
- supports two resources:
	- *objects*, which are essentially files
	- *buckets*, which are essentially directories
		- there is no concept of placing an object into a bucket
			- an object `ObjectName` that lives in a bucket `BucketName` is referred to by a *Unform Resource Identified (URI)*
				- `https://s3.amazon.aws/BucketName/ObjectName`
		- creating a bucket or object is essentially just a PUT request with the URI of the object/bucket
		- retrieving objects within a bucket is just a GET request

- RESTful architecture has become popular, in part, due to its simplicity
	- e.g. AWS S3's now-deprecated SOAP interface
![[Pasted image 20250408210047.png]]

### Publish-subscribe architectures
- as systems grow and processes can join or leave, it becomes important to have an architecture where processes become as loose as possible
	- strong separation between *processing* and *coordination*
		- in this model, coordination encompasses the communication and cooperation between processes
			- the "glue" that binds the activities performed by processes into a whole
		- when processes are temporally and referentially coupled, coordination takes place directly
			- *direct coordination*
![[Pasted image 20250408210317.png]]
- when processes or temporally decoupled, but referentially decoupled, this is called *mailbox coordination*
	- processes are able to leave messages for each other to be processed, like a mailbox
- referentially decoupled, but temporally coupled systems model *event-based communication*
	- processes do not know each other explicitly, but can publish a notification describing the occurrence of an event
- referentially and temporally decoupled processes lead to what is known as a *shared data space*
	- processes communicate entirely through tuples
		- structured data records consisting of several fields

![[Pasted image 20250408210801.png]]
- important aspect of publish-subscribe systems is that communication takes place by describing the events that a subscriber is interested in
	- thus, naming plays a crucial role
	- events are described by a series of *attributes*
		- a notification describing an event is said to be *published* when it is made available for other processes to read
			- a *subscription* needs to be passed to the middleware
				- contains a description of the event the subscriber is interested in
					- consists of *(attribute, value)* pairs
						- common for *topic-based publish-subscribe systems*
- the alternative is a *content-based publish-subscribe system*
	- subscriptions consist of *(attribute, range)* pairs
		- the attribute takes on all values within the range
			- creates the need to match subscriptions against notifications
![[Pasted image 20250408211139.png]]
- it's possible to *lease* a data item so when the lease expires the data item is deleted

## Middleware and distributed systems
- distributed systems are often organized to have a separate layer of software that is logically placed on top of the operating systems of the components of the distributed system
	- this is called *middleware*
![[Pasted image 20250409195014.png]]

- middleware, in addition to resource management, offers services that can also be found in most operating systems, including:
	- facilities for interapplication communication
	- security services
	- accounting services
	- masking of and recovery from failures

### Middleware organization
- 2 important types of design patterns applied to middleware:
	- *wrappers*
		- legacy components rarely have interfaces that are directly suitable for all applications
			- a wrapper is a special component that offers an interface acceptable to a client application, and translates them to interfaces acceptable to the component
		- an important job of middleware is reducing the number of wrappers in use
			- typically handled through a *broker*, which is logically a centralized component  that handles all accesses between different applications
				- *message broker* - a type of broker that allows all components to send their messages to it, and it wraps and forwards traffic as needed
	- *interceptors*
		- an interceptor is a software construct that will break the usual flow of traffic and allow application specific code to be executed
			- they are a primary means for adapting middleware to the specific needs of an application
				- important role to make middleware open
		- are supported in many object-based distributed systems
			- the workflow occurs in 3 steps:
				- Object A is offered a local interface that is the same as the interface offered by object B. A calls the metho available in that interface
				- The call by A is transformed into a generic object invocation, made possible through a general object-invocation interface offered by the middleware at the machine where A resides.
				- The generic object invocation is transformed into a message that is sent through the transport-level network interface as offered by A's operating system:
![[Pasted image 20250409200638.png]]

### Modifiable middleware
- wrappers and interceptors offer means to extend and adapt middleware
	- allows an environment in which changes are executed continuously
		- the middleware adapts to these changes, not the applications or components
			- *modifiable middleware*
				- middleware may not only need to be adaptive, but we should have the ability to modify it without taking it down

## Layered-system architectures
 - selecting software components, their interaction, and their placement leads to an instance of their *system architecture*

### Simple client-server architecture
- in a client-server model, a distributed system is divided into two groups
	- a *server* is a process implementing a specific service, e.g. a file system service or database service
	- a *client* is a process that requests a service from a server by sending a request and waiting for a reply
		- known as *request-reply behavior*
![[Pasted image 20250409201726.png]]
- client-server communication can be implemented by a simple connectionless protocol if the network is fairly reliable (remember, networks are not always reliable, and cost is not zero)
	- when a client requests a service, it packages a message for the server and then sends it
		- the server will wait for a request, process it, then send the results back
	- a connectionless protocol has the advantage of being efficient
		- as long as a message doesn't get lost or corrupted, it will be processed just fine
		- not a trivial request
		- can lead to issues with stateful operations, i.e. bank account transactions
- when an operation can be repeated multiple times without hard, it is said to be *idempotent*
- alternatively, many client-server systems use a reliable connection-oriented protocol, such as TCP/IP

### Multitiered architectures
- the simplest organization is to have only two types of machines
	- a client machine containing the programs implementing the user-interface level
	- a server machine containing the rest, i.e. the programs implementing the processing and data level
	- this is referred to as a *(physically) two-tiered architecture*
![[Pasted image 20250409202514.png]]
- a limitation of two-tiered architectures is that sometimes a server may need to act as a client
	- this can lead to a *(physically) three-tiered architecture*
![[Pasted image 20250409202851.png]]
- programs that form part of the processing layer are executed by a separate server, but may be partly distributed across the client and server machines
	- ex. transaction processing
		- a separate process, the transaction processing monitor, coordinates all transactions across different data servers

### Ex. The Network File System
- many distributed files systems are organized like client-server architectures
	- Sun Microsystem's *Network File System (NFS)*
- basic idea behind NFS is that each file server provides a standardized view of its local file system
	- it doesn't matter how the local file system is implemented, each NFS server supports the same model
- underlying model is that of a *remote file service*
	- clients have transparent access to a file system that is managed by a remote server, but they are unaware of the actual location of files
		- clients have an interface to a file that is similar to the interface offered by a conventional file system
			- the server is responsible for implementing the operations from the interface
				- *remote access model*
![[Pasted image 20250409203721.png]]
- in contrast, the *upload/download model* allows a client to access a file locally after having downloaded it from the server
	- when the client is finished, it is uploaded back to the server
- in NFS, all communication is handled through *remote procedural calls (RPCs)*
	- an RPC is essentially a standardized way to let a client on a machine A make a call to a procedure that is implemented on machine B

### Eg. The Web
- many web-based systems are still organizaed as relatively simple client-server architectures
	- a website is formed by a process that has access to a local file system storing documents
		- the simplest way to refer to a document is by a reference called a *uniform resource locator (URL)*
			- specifies the document location by embedding the DNS name of its server with the file name by which the document can be looked up
- a client interacts with web servers through a *browser*, which displays the document
	- it also accepts input from the user
- communication between a browser and web server is standardized and adheres to the *HyperText Transfer Protocol (HTTP)*
![[Pasted image 20250409204712.png]]
- web documents are typically marked up using the *HyperText Markup Language (HTML)*

### Multitiered architectures
- the web started out as the relatively simple two-tiered client-server system, but over time, it has adapted to support more sophisticated infrastructures
	- one of the first enhancements was suppot for simple user interaction by the *Common Gateway Interface (CGI)*
		- CGI defines a standard way by which a web server can execute a program
			- can be much more sophisticated than documents, as sophisticated as the developer wants
![[Pasted image 20250409205117.png]]

# Symmetrically distributed system architectures
- multitiered client-server architectures are a direct consequence of dividing distributed applications into a user interface, processing components and data management components
	- different tiers correspond with the logical organization of applications
		- this type of distribution is referred to as *vertical distribution*
			- it is achieved by placing logically different components on different machines
	- in many modern systems, the distribution of clients and servers tends to matter more
		- this is called *horizontal distribution*
			- clients and servers may be physically split up into logically equivalent parts, but each part operates on its own share of the complete data set
				- load balancing
				- *peer to peer systems*
- at a high level, the processes that constitute a peer to peer system are all equal
	- functions that need to be carried out are represented by every process that constitutes the distributed system
		- much of the interaction between processes is symmetric
			- each process will act as both a client and a server at the same time
				- acting as a *servant*
- peer to peer architectures revolve around how to organize the processes in an *overlay network*
	- a network in which the nodes are formed by the processes and the links represent the possible communication channels
		- a node may not be able to communicate directly with an arbitrary other node, but is required to send messages through the available communication channels

### Structured peer-to-peer systems
- in a structured peer to peer system, the nodes are organized in an overlay that adheres to a specific, deterministic, topology
	- ring
	- binary tree
	- grid
	- etc.
		- these topologies are used to efficiently look up data
- it is common to associate data items with a key, and this key is used as an index
	- also common to use a hash function like so:
`key(data item) = hash(data item's value)`
- the P2P system as a whole is now responsible for storing these key, value pairs
	- each node is assigned an identifier from the set of hash values
		- thus, the system is seen to be implementing a *distributed hash table (DHT)*
	- this reduces the essence of peer to peer systems to being able to look up a data item by its key like so:
`existing node = lookup(key)`

### Unstructured peer-to-peer systems
- an unstructured peer to peer system is one where each node maintains an ad hoc list of neighbors
	- the resulting overlay resembles a *random graph*
		- an edge `{u, v}` between two nodes `u` and `v` only exists with a certain probability `P[{u,v}]`
			- ideally, this probability would be the same for all pairs of nodes
				- in practice, it varies wildly
	- when a node joins, it often contacts a well-known node to obtain a starting list of other peers in the system
		- this list will be used to find more peers and ignore others
			- a node generally changes its local list almost continuously
- looking up data cannot follow a predetermined route, but there are a couple different ways to search for data:
	- *flooding* - a issuing node passes a request for data to all of its neighbors
		- if the neighbor has seen that request before, it will ignore it, otherwise it searches locally for the data
			- if the data is found, it is sent back
				- if the data is not found, the request will be forwarded to all of its own neighbors
		- flooding can be expensive
			- requests often have an associated *time to live (TTL)*, a maximum number of hops the request can be forward
	- *random walks* - an issuing node tries to find a data item by asking a randomly chosen neighbor
		- if that neighbor doesn't have it, it will ask a random neighbor, and so on and so on until the data is found
		- a random walk imposes much less network traffic than flooding
			- may take much longer to find data
				- it's possible to issue multiple random walks at once to alleviate this
	- *policy-based search methods*
		- a medium between flooding and random walks
			- a node may keep track of peers who responding positively
				- this makes them preferred neighbors for successive queries
### Hierarchically organized peer-to-peer networks
- in unstructured P2P networks, finding data can be problematic as the network grows
	- no deterministic way to find data, it must be searched for
	- the solution is to use special nodes that maintain an index of data items
		- *Content Delivery Network (CDN)* - nodes offer storage for hosting copies of web documents
			- this lets them be stored nearby to clients, and allow the clients to access the geographically closer copies, minimizing latency
				- using a broker that collects data on resource usage and availability lets them select a node quickly with sufficient resources
		- nodes that maintain an index or acting as a broker are generally referred to as *super peers*
			- often organized in a P2P network, creating a hierarchical organization
			- a *weak peer* is every non-super peer node
			- the connection between super and weak peers is often fixed: when a weak peer joins the network, it attaches to a super peer and becomes attached until it leaves the network
![[Pasted image 20250409211725.png]]

## Hybrid system architectures
- real world distributed systems will combine a myriad of architectures
	- many distributed systems cross organizational boundaries
		- leads to truly decentralized solutions

### Cloud computing
- *utility computing* - a customer could upload tasks to a data center and be charged on a per-resource basis
	- provided the basis for what is now referred to as *cloud computing*
- cloud computing is characterized by an easily usable and accessible pool of virtualized resources
	- which resources are used and how they are used can be configured dynamically
		- the basis for scalability - if more work needs to be done, simply acquire more resources
	- cloud computing generally based on a pay-per-use model
		- guarantees are offered by customized *service level agreements (SLAs)*
	- clouds are organized into four layers:
		- *Hardware* - formed by the means to manage necessary hardware
			- processors
			- routers
			- power and cooling
				- lowest layer
		- *Infrastructure* - forms the backbone
			- deploys virtualization techniques to provide customers an infrastructure consisting of virtual storage and computing resources
				- cloud computing evolves around allocating and managing virtual storage devices and virtual servers
		- *Platform* - provides to cloud computing what the OS provides to an application
			- the means to easily develop and deploy applications that need to run in a cloud
			- a developer is offered a vendor-specific API, which includes calls to uploading and executing a program in that vendor's cloud
			- provides higher layer abstractions for storage and such
		- *Application* - applications run in this layer and are offered to users for further customization
![[Pasted image 20250409212759.png]]
### The edge-cloud architecture
- as more network-connected devices came into being, we started to need more than just cloud computing
	- *edge computing* was born
		- deals with placement of services "at the edge" of the network
			- the edge is typically formed by the boundary between enterprise networks and the actual internet as provided by an *Internet Service Provider (ISP)*
				- as we move away from the edge network and get closer to a traditional cloud infrastructure, the term *fog computing* starts to be used
![[Pasted image 20250409213040.png]]
#### Latency and bandwidth
- edge infrastructures are often considered to be close to end devices
	- can be measured by latency and also bandwidth
#### Reliability
- for situations which rely on 24/7 availability, cloud computing may not be sufficient for the application's needs

#### Security and privacy
- cloud infrastructure raises new security concerns
	- if a cloud solution is not secure, there is no reason to think an edge solution would be
- however, certain types of data are prohibited at an organizational level from being stored in the cloud
	- proprietary data, sensitive medical data, etc.

### Blockchain architectures
- blockchain systems enable the registration for transactions
	- also called *distributed ledgers*
- a transaction is validated, effectuated, then stored for auditing purposes
	- there is a very large set of participants who jointly register transactions among them in a publicly available ledger
![[Pasted image 20250409213620.png]]
- only a single logical chain of blocks with validated transactions
	- each block is immutable
		- thus a block is an ideal fit for massive replication
			- each block is broadcast to every participating node in a blockchain
- big differentiation is how blockchains decide which node may carry out validation tasks
	- we have to figure out which nodes may actually carry out validation tasks
		- who is allowed to append nodes to the chain?
			- requires *(distributed) consensus*
				- in general, 3 options:
					- a centralized solution, where a trusted third party validates transactions
					- a distributed solution, in which a small, preselected group of processes acts as the trusted third party
					- a fully decentralized solution, where all participating nodes jointly reach consensus
					- in all of these, each node participating in a blockchain is assumed to have a full copy locally available
![[Pasted image 20250409214102.png]]
## Summary
- distributed systems can be organized in many ways
	- distinction between software architecture and system architecture
- "architectural style"
	- the basic principles followed in organizing the interaction between the software components of a distributed system
- client-server architectures are often highly centralized
	- in a decentralized architecture, we see an equal role played by the processes that constitute a distributed system
		- also known as a peer to peer system
			- processes are organized into an overlay network
				- a logical network where every process has a local list of other peers that it can communicate with
				- can be structured, which cause deterministic schemes to be deployed for routing messages between processes
- hybrid architectures combine elements from centralized and decentralized organizations
	- e.g. cloud computing
- edge infrastructures form several steps between end devices and clouds
	- demanding from the view point of organizing and configuring distributed systems
