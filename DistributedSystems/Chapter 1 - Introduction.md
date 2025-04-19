# From networked systems to distributed systems
What does distribution, or decentralization, actually entail?
## Distributed vs. decentralized systems
- many different opinions on distributed vs. decentralized
![[Pasted image 20250401202424.png]]
	a) centralized b) decentralized c) distributed
- often stated that centralized organizations do not scale well
- distributed ones are said to be more robust against failures
	- neither are generally true

If we think of a networked computer system as a collection of computers connected in a network, how did they even become connected in the first place?
- the *integrative view*
	- there was a need to connect computer systems to each other
		- typically happens when services running on a system needed to be available to users and applications previously not thought of
- the *expansive view*
	- an existing system needed an extension through additional computers
		- most related view to distributed systems
	- entails expanding a systems with computers to hold resources close to where those resources are needed
		- may also need to improve dependability
- these two views lead to a natural distinction between two types of networked computer systems
	- a *decentralized system* is a networked computer system in which processes and resources are **necessarily** spread across multiple computers
	- a *distributed system* is a networked computer system in which processes and resources are **sufficiently** spread across multiple computers

### Decentralized systems are mainly related to the integrative view of distributed systems
- they come into being because we want to connect systems, but may be hindered by administrative boundaries
	- AI can't necessarily exfiltrate data to the high performance computer to train the model, so instead the system has to be distributed enough to go to the data
		- federated learning
- another example is the distributed ledger, or the blockchain
	- participating parties do not trust each other enough to set up simple collaboration schemes
		- thus, transactions are made fully public and verifiable utilizing an extend-only ledger that keeps a full record of all transactions
			- all participants have access to the full ledger, and participate in validating transactions before adding them to the ledger
- you also need to consider geographically dispersed distributed systems
	- generally related to systems intrinsically connected to physical locations
		- power plant, building, etc.
		- the systems monitoring the location may be placed in a different location than the one being monitored
### Content Delivery Networks
- the content of a website is copied and spread across various servers of the CDN
	- when a user accesses the website, they are transparently redirected to a nearby server that holds all or part of the content for that website
	- the CDN dynamically ensures that the server the user accesses will have the content readily available, as well as update that content when needed
		- can also remove content when it's not being used
	- all of this occurs with the user having no knowledge of it

### Network Assisted Storage
 - a typical NAS consists of 2-4 hard drive disks and operates as a file server
	 - it is available over the network like any authorized device
		 - offers services like shared storage, automated backups, streaming media, etc.
- can be best seen as a single computer optimized for storing files, and offering the ability to easily share those files
	- add multiple users, and we have a distributed system

### Are centralized solutions bad?
 - there is a stubborn misconception that centralized solutions can't scale
	 - almost always associated with a single point of failure
		 - however, one must differentiate between logical and physical designs
- Logically, DNS is arranged as a huge tree, where each path from root to a leaf represents a FQDN
	- it would be a mistake to assume there is a single server implementing this
		- in actuality, there are 13 root servers, each compromising a cluster of smaller servers
- Centralized solutions are not bad because they're centralized
	- can be much easier to manage
	- can be hardened against many different types of security attacks
## Why making the distinction is relevant
It is important to realize that centralized solutions are generally simpler, and also simpler along different criteria
- decentralization is a decision that must be considered carefully
- distributed and decentralized solutions are inherently difficult:
	- often many unexpected dependencies that hinder understanding system behavior
	- suffer almost continuously from partial failures
	- participating nodes, processes, resources etc. come and go
		- distributed systems tend to be highly dynamic
	- distributed systems, due to their networked nature, tend to be particularly vulnerable to security attacks

An important distinction is between sufficiency and necessity for spreading processes and resources across multiple computers
> "Decentralization can never be a goal in itself, and that it shoudl focus on the sufficiency for spreading processes and resources across computers."

- it is also important to consider solutions that are as simple as possible to meet the requirements

## Studying distributed systems
- since they are inherently difficult, it's important to take a systematic approach toward studying them
	- many explicit and implicit dependencies in distributed systems
		- our approach will be to approach them from a limited number of different perspectives
- Architectural perspective: what are the common organizations, what are the common styles?
- Process Perspective: understanding the different forms of processes that occur in distributed systems
- Communication Perspective:  concerns the facilities that distributed systems use to exchange data between facilities.
- Coordination Perspective: look at the fundamental coordination tasks that allow distributed systems to work together
- Naming Perspective: focus on resolving a name to the access point of the named entity
- Consistency and Replication Perspective: concentrates on the trade-offs between consistency, replication, and performance
- Perspective of Fault Tolerance: dives into the means for masking failures and their recovery
- Security Perspective: focuses on how to ensure authorized access to resources

# Design goals

## Resource sharing
- an important goal of a distributed system is to make it easy for users and applications to access and share remote resources
	- it's cheaper to have a single high-end storage facility and share it than to give each process their own
- connecting users and resources makes it easier to collaborate and share information
	- *groupware* - software for collaborative editing, teleconferencing, etc.
- Peer to Peer sharing networks
	- a la Bittorrent
	- allow users to share files over the internet
- Seamless integration of resource-sharing facilities is not commonplace
	- a group of users utilizing a single shared folder managed by a third party

## Distribution Transparency
- important goal is to hide that processes and resources are physically distributed across multiple computers
	- achieving distribution transparency is realized through what is known as **middleware**
	- ![[Pasted image 20250401211029.png]]
- several different types of distribution transparency:
- Access transparency deals with hiding differences in data-representation and the way that objects can be accessed
	- we want to hide differences in machine architectures, but it's more important to reach an agreement on how this 
- Location transparency refers to the fact that users cannot tell where an object is physically located in the system
	- naming plays an important role
- Migration transparency is when a distributed system supports the mobility of processes and resources initiated by users, without affecting ongoing communication and operations
	- mobile phones,online tracking, teleconferencing
- Replication transparency - several copies of a resource exist, or that several process operate in lockstep, ready to take over if one fails
	- all replicas must have the same name to hide this from users

Cooperative v. Competitive resource sharing
- in many cases, resources are shared cooperatively, but there are some where it is more competitive
	- when it occurs, it is important that one user does not notice the other is using the same resource
		- this is called concurrency transparency
			- an important issues is that concurrent access to a shared resource leaves it in a stable state
- failure transparency means that a user or application does not notice that some piece of the system fails to work properly
	- the system also automatically recovers from that failure
		- main issue is differentiating between a failed system and a merely slow one

- Distribution transparency is generally considered preferable, but blindly attempting to hide all distribution aspects is not a good idea

## Openness
- an open distributed system is a system that offers components that can easily be used by or integrated into other systems
- components should adhere to standard rules that describe the syntax and semantics of what those components have to offer
	- can define services through interfaces using an Interface Definition Language
- Interoperability characterizes the extent by which two implementations of systems or components can coexist and work together
- Portability characterizes to what extent an application developed for a distributed system on a different one with the same interfaces
- Extensible - describes how easy it is to configure the system from different components

### Separating Policy from Mechanism
- to achieve flexibility, it is crucial to organize the system as a collection of relatively small and easily replicable or adaptable components
	- we should provide definitions for both high level interfaces, as well as internal system parts
		- both need to be considered when changing a system as there are usually many different parameters to be considered
- what is needed is a separation between policy and measurement
	- i.e. a web server should provide facilities for storing documents (the mechanism), and then allow users to set which files are retained and for how long (the policy)

## Dependability
- refers to the degree that a computer system can be relied upon to operate as expected
	- can be relatively intricate due to partial failures, a component that fails while the system as a whole continues functioning
		- the ability to mask failures is referred to as fault tolerance
- dependability covers several important concepts:
	- Availability
		- the property that a system is ready to be used immediately
		- a highly available system is mostly likely working at a specific point in time
	- Reliability
		- that a system can run continuously without failure
	- Safety
		- when a system fails to operate correctly, no catastrophic event occurs
	- Maintainability
		- how easily a failed system can be repaired
			- Mean Time to Failure
			- Mean Time to Repair
			- Mean Time Between Failures
- The cause of a error is caused a fault
- building dependable systems correlates strongly to controlling faults
- Transient Faults occur once and then disappear
	- it will probably work the second time
- Intermittent Faults occur, vanish on their own accord, then reoccur and repeat
- Permanent Faults occur and then continue to exist until fixed

- Confidentiality is the property that information is disclosed only to authorized parties
- Integrity ensures that alterations to assets can only happen in an authorized way

## Security
- authorization is the process of checking whether an identified identity has proper access rights
- authentication is verifying the correctness of a claimed identity
- Trust: permission one to make the desired changes
- security is all about encrypting and decrypting data using security keys
- ![[Pasted image 20250401214907.png]]
	- only the holder of the key can decrypt information
- proving an entity has seen specific data occurs frequently in distributed systems

### Scalability
- system scalability can be measured along multiple dimensions:
	- size scalability - we can easily add more users and resources to the system without loss of performance
		- many services in a distributed system can be considered centralized in the sense that they are implemented by a single server within the system
			- obvious issues: bottlenecks with the single server can become bottlenecks for the entire system
				- computational capacity - if there is only a single machine available to perform a service, as requests scale, even a high end system will eventually run out of threads to run them on
				- I/O bound - even with advanced indexing techniques, processing time will eventually be limited by the relatively slow disk access
				- network capacity - you're only as fast as the slowest link between the user and a service
	- geographical stability - users and resources may lie far apart geographic, but communication delays are insignificant
		- it's difficult to scale distributed systems geographically because many of them are based on synchronous communcation
			- a party (the client) requests the servers and blocks until a reply is sent back from the server that implements the service
				- this works fine in LAN when lag is measured in microseconds
					- in geographically larger systems when response time is measured in milliseconds, this may not be acceptable
			- another issues is the inherent drop in reliability in communication across geographically disparate systems
			- additionally, wide area systems generally have limited facilities for multipoint communication
	- administrative scalability - can still be easily managed even if it spans many independent administrative organizations
		- many components of a distributed system that reside within a single domain can often be trusted by users within that domain
			- if a system expands to other domains, security measures must be implemented
				- the system must protect itself against malicious attacks from the new domain
				- in addition, the new domain must protect against attacks from the distributed system

#### Scaling Techniques
- in most cases, scalability problems appear as performance problems caused by limited capacity of servers and network
	- simply improving their capacity (by increasing memory, upgrading CPUs or replacing network modules) is referred to as *scaling up*
	- *scaling out* is expanding the distributed system by deploying additional machines

#### Hiding communication latencies
- applicable in cases of geographical scalability
	- basic principle: try to avoid waiting for responses to remote-service requests as much as possible
		- if a request is sent to a server, while waiting for a response, do something else
			- asynchronous communication
				- not always feasible - in this case move some communication from the server to the user

#### Partitioning and distribution
- involves taking a component or resources, splitting into smaller parts, then spreading those parts across the system
	- DNS is hierarchically organized into a tree of domains, which are divided into nonoverlapping zones
		- each zone is handled by a single name server
			- we can think of each path through the the tree as a valid host name on the internet

#### Replication
- since scalability problems often appear in the form of performance degradation, it's generally a good idea to replicate components or resources across a distributed system
- replication increases availability and helps balance the load between components
	- this leads to better performance
	- in addition, in geographically disparate systems, having a local copy can hide many communication latency issues associated with them
- *caching* is a special form of replication
	- caching results in make a copy of a resource (similar to replication)
		- this decision is made by the client of a resource and not the owner of a resource (different compared to replication)
- replication has one major drawback: once multiple copies of a resource exist, changes to one copy of the resource has to be replicated to all of the copies to maintain consistent performance across replicas
	- otherwise you have *consistency* problems
	- thus, replication often requires a global synchronization method of some kind

## A simple classification of distributed systems
- the boundaries between centralized, decentralized and distributed systems are not strict between the classifications
	- easy to think of combinations of the different types
### High performance distributed computing
- in *cluster computing*, the underlying hardware consists of a collection of similar compute nodes interconnected by a high speed network alongside a more common local-area network for controlling the nodes
	- *grid computing* makes this very different
		- consists of decentralized systems that are often constructed as a federation of computer systems
			- each system may fall under a different administrative domain
				- may be very different when it comes to hardware, software, and deployed network technology
	- cluster computing became popular when the price/performance ratio of personal computers and workstations improved
		- it eventually became financially attractive to create a supercomputer by connecting a collection of relatively simple computers in a high-speed network
			- generally used for parallel programming, where a single program is run in parallel on multiple machines
![[Pasted image 20250402200604.png]]
- a management node is generally responsible for collecting jobs from users to distribute among the nodes of the cluster
- the role of the operating system in cluster computing has gradually minimized, moving towards lightweight kernels that ensure the least possible overhead
	- the downside of this is clusters become increasingly specialized
- while cluster computing relies on the homogeneity of the nodes within it, grid computing does not make these kinds of assumptions
	- a key issue is resources from different organizations are brought together to form a federation of systems
		- a *virtual organization*
		- processes belonging to the same virtual organization have access to the resources that are provided to that organization
			- compute servers, storage facilities, databases, etc.
	- much software for grid computing revolves around providing access to resources from different administrative domains, and to only those users and applications within that domain
![[Pasted image 20250402201605.png]]
- the architecture of grid computing consists of four layers:
	- the *fabric layer* provides interfaces to local resources at a sppecific site
		- they are tailored to allow sharing of resources within a virtual organization
	- the *connectivity layer* consists of communication protocols for supporting grid transactions that span the usage of multiple resources
		- needed to transfer data between resources or to access a resource from a remote location
		- it will also contain security protocols to authenticate users and resources
			- in general, human users are not authenticated, but programs acting on their behalf are
	- the *resource layer* is responsible for managing a single resource
		- uses the connectivity layer functions and directly calls the fabric layer interfaces
		- the resource layer is seen to be responsible for access control, and relies on the authentication methods performed by the connectivity layer
	- the *application layer* consists of the applications that operate within a virtual organization and which make use of the grid computing environment
	- all of these can be seen as comprising the grid middleware layer and jointly provide access to and management of resources that are dispersed across multiple sites

### Distributed information systems
- another important class of distributed systems is one where an organization has a wealth of networked applications, but interoperability is painful
	- there are several levels at which integration can take place
		- a networked application is often just a  server running an application, making it available to remote programs (called *clients*)
			- clients send a request to the server to execute a specific operation, to which a response is sent back
				- integration allows clients to wrap requests for multiple different servers together into a larger request
					- this can be executed as a *distributed transaction*
					- over time, it became apparent that applications should just be allowed to communicate with each other
						- *enterprise application integration*

#### Distributed transaction processing
- in practice, operations on a database are carried out in the form of *transactions*
	- this requires special primitives that must be supplied by the underlying distributed system or the language runtime system
		- in particular, *remote procedural calls* to remote servers, are often encapsulated in a transaction
			- *transactional RPC*
![[Pasted image 20250402202856.png]]
- transactions adhere to the so-called *ACID* properties:
	- **Atomic** - to the outside world, the transaction happens indivisibly
	- **Consistent** - the transaction does not violate system invariants
	- **Isolated** - concurrent transactions do not interfere with each other
	- **Durable** - once a transaction commits, the changes are permanent
- within distributed systems, transactions are often constructed as a number of subtransactions
	- all subtransactions together form a *nested transaction*
![[Pasted image 20250402203105.png]]
- subtransactions can lead to a subtle, but important problem
	- Imagine one transaction within a subtransaction commits and makes its results visible to the parent transaction
		- if the parent aborts after further actions and returns the system to the orginal state, the subtransaction must be reversed
			- the durability in ACID only applies to the top-level transactions in a system
- Nested transactions are important in distributed systems for they provide a natural way of dividing transactions across multiple machines
	- they follow a logical division of the work of the original transaction
		- if you have a transaction in 3 parts, it's trivial to split that work up amongst 3 different systems
			- the component that divides this is called a *transaction-processing monitor (TP monitor)*
				- it essential coordinates the commitment of subtransactions following a standard protocol known as a *distributed commit*
![[Pasted image 20250402203658.png]]
#### Enterprise application integration
- the more applications become decoupled from the databases they were built upon, the more evident it became that facilities were needed to integrate applications independently of their databases
	- application components should be able to communicate directly with each other and not merely by the means of the request/reply behavior that was supported by transaction processing systems
![[Pasted image 20250402204054.png]]
- Several types of communication middleware exist
	- with RPCs, an application component can send a request to another application component by doing a local procedure call
		- this results in the request being packaged as a message and sent to the callee
			- the result will be sent back as a result of the procedure call
	- as time went on, techniques were developed to allow calls to remote objects
		- *remote method invocations(RMIs)*
			- the same as an RPC, except it operates on objects instead of functions
	- RPCs and RMIs have the same disadvantage that the caller and callee must both be online during the time of communication
		- they also need to know exactly how to refer to each other
			- has led to the rise of *message-oriented middleware (MOM)*
				- applications send messages to logical contact points, often described by a subject
					- applications can indicate their interest for a specific type of message, after which the communication middleware will take care that those messages are delivered to those applications
						- *publish/subscribe* systems
### Pervasive systems
- pervasive systems are intended to blend into our environment naturally
	- components are necessarily spread across multiple systems
		- thus, can be considered decentralized as opposed to distributed systems
			- do have components that are sufficiently spread throughout the system, to handle failures, etc.
	- are unique in that the separation between users and system components is much more blurred
		- often no dedicated interface
		- pervasive systems are typically equipped with many sensors that pick up various aspects of user behavior
			- may have a variety of *actuators* to provide info and feedback
			- devices in pervasive systems are often characterized as being small, battery-powered, mobile, and having only a wireless connection
				- *Internet of Things*
#### Ubiquitous computing systems
- in an ubiquitous computing system, the system is pervasive and continuously present
	- a user will be continuously interacting with the system
- core requirements of a ubiquitous computing system:
	- *Distribution* - devices are networked, distributed, and accessible transparently
	- *Interaction* - interaction between users and devices is highly unobtrusive
		- much interaction will be *implicit*
			- not primarily aimed to interact with a computerized system, but which a system understands as input
	- *Context awareness* - the system is aware of a user's context to optimize interaction
		- identified by the where, who, when, and what of actions within the system
		- important the data collected is lifted to a level of abstraction that can be used by applications
		- *shared data spaces* - where processes are decoupled in time and space
	- *Autonomy* - devices operate autonomously without human intervention
		- highly self-managed
		- no room for sysadmin to keep the whole system running
		- the system must have the ability to react to changes:
			- *Address allocation* - networked devices should have IP addresses dynamically allocated via DHCP
			- *Adding devices* - it should be easy to add devices to the system, such as via *Universal Plug and Play protocol (UPnP)*
			- *Automatic Updates* - devices should be able to regularly check in and download updates
	- *Intelligence* - the system as a whole can handle a wide range of dynamic actions and interactions

#### Mobile computing systems
- mobility often forms an important feature of pervasive systems
	- many aspects of them also apply to *mobile computing*
- the devices to comprise a mobile computing system may vary wildly
- the location of devices within the system geographically is expected to change over time
	- this may have a profound effect on communication
		- *mobile ad hoc networks (MANETs)*
			- a group of local mobile computers would jointly set up a local wireless network to share resources and services
				- never really caught on
- the need for so many devices to utilize remote services has led to the development of *Mobile Edge Computing (MEC)*, in contrast to *Mobile Cloud Computing (MCC)*
![[Pasted image 20250402205907.png]]

#### Sensor Networks
- these networks in many cases form part of the enabling technology for pervasiveness
	- many solutions for sensor networks return in pervasive applications
- more than just a collection of input devices
	- sensors are often able to collaborate to process the sensed data efficiently in an application-specific manner
- when considering sensor networks produce data, one can also focus on the data-access model
	- can directly send messages between the nodes, or move code between nodes to access data
		- can also let the sensor network provide a view of a single database
			- two extremes of this view:
				- first, sensors do not cooperate but send their data to a centralized database
				- alternatively, forward queries to relevant sensors and let each computer an answer
					- generally preferable to find a happy medium (perhaps utilizing a tree)
![[Pasted image 20250402210728.png]]

## Pitfalls
- developing a distributed system is a formidable task
- false assumptions many make when developing a distributed application for the first time:
	- the network is reliable
	- the network is secure
	- the network is homogeneous
	- the topology does not change
	- latency is zero
	- bandwidth is infinite
	- transport cost is zero
	- there is one administrator

## Summary
- a distributed system is a collection of networked computer systems in which processes and resources are spread across different computers
	- distinction between sufficiently (distributed systems) and necessarily (decentralized systems) spread
- spreading processes and resources cannot be considered to be a goal in and of itself
- most choices for coming to a distributed system come from the need to improve the performance of a system in terms of reliability, scalability, and/or efficiency
- design goals typically include sharing resources and openness, but designing secure distributed systems is increasingly important
- designers also aim to hiding many of the intricacies related to distributing processes
	- comes at a performance price, and may also not be able to be fully achieved
		- tradeoffs are an inherent part of distributed system design