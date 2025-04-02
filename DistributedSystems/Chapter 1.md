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
	- geographical stability - users and resources may lie far apart geographic, but communication delays are insignificant
	- administrative scalability - can still be easily managed even if it spans many independent administrative organizations