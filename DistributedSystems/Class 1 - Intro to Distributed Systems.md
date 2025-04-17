- A distributed system is a network that consists of autonomous computer that are connected using a distribution middleware.
	- they help in sharing different resources and capabilities to provide users with a single and integrated coherent system

### What is a distributed system?
- it depends on who you ask!

## Why are distributed systems important?
- performance/scalability
	- End of Moore's law
- Efficiency
	- increasing specialization (hardware heterogeneity)
	- Workloads are often sporadic
- Availability/Fault Tolerance
	- avoid single point of failure
	- Nodes/software fail (often) and take time to recover
	- Power outage, natural disasters, etc. can affect entire data centers
- Durability
	- storage decays over time

# Course Goals
- know what a distributed system is
- understand where distributed systems are used
- understand the challenges on distributed systems
- understand solutions for common distributed systems problems
- gain practical knowledge of systems, tools, libraries, algorithms that can help you build real systems

## 1) More than one computing element
- may contain few to many computing elements
	- called a "node"
		- may be a personal computer, laptop, HPC node, small device, etc.
- Implication: nodes must communicate with each other
	- via messages where a node reacts to an incoming message, processes, then communicates with other nodes

## 2) Independent computing elements
- each node is independent from the others (autonomous)
	- each node may operate concurrently with the others
- Implication: how do we manage independent elements?
	- how do we even track which nodes are part of the distributed system?

## 3) No global clock
- each node has its own notion of time
	- may be different, run at different rates, unpredictable communication latency
- Implication: no common reference for time
	- how do we agree on anything if we don't agree on time?
	- how do we know what happened first?

## 4) Independent failures
- independent nodes lead to independent failures
- Implication: If one node fails, what happens to the rest of the system?
	- ideally, it should not result in system failure
	- Challenge: how do we tell if a node failed? Was it just slow to reply?
- Hiding failures and recovery is challenging

## 5) Single Coherent System
- processes, data, and control may be distributed across a network
- ideally we would like to make it look like a single system, but this is hard
- Implication: we have to cut corners...
	- appearing to be coherent. The system behaves according to the expectations of its users.
	- Strive for distributed transparency
		- users shouldn't be able to tell where a process executes or where data is stored
	- Partial failures: at any time part of the system may fail
		- hiding partial failures/recovery is challenging

## Middleware: a distributed operating system
- represents all of the things that should be done across a distributed system

# Design goals for building a distributed system
- how do we decide if it's worthwhile?
- A distributed system should:
	- make remote resources accessible (sharing)
	- hide that resources are distributed (transparency)
	- Be open such that components can easily used by others (openness)
	- scale to increasing numbers of users, resources, locations (scalable)
	- able to deliver reliable and correct processing (dependable)
	- resources, computations, and users are protected (secure)

## 1) Resource sharing
- make it easy for an application to access and share remote resources
	- peripherals, storage facilities, data, files, services, and networks
- Why share?
	- economics: cheaper to share resources
	- collaboration: central view of resources
	- necessary: sending files and resources to others, accessing an instrument
- Ex.
	- cloud storage
	- P2P networks
	- outsourced mail services
## 2) Distribution Transparency
- hide the fact that processs and resources are physically distributed across multiple computers
- Aiming for full distribution transparency may be too much
	- there are some communication latencies that cannot be hidden
	- completely hiding failures or networks and nodes is (theoretically and practically) impossible
		- cannot distinguish a slow computer from a failing one
		- you can never be sure that a server performed an operation before a crash
	- Full transparency will cost performance (and expose distribution of the system)
		- keeping replicas exactly up to date with the master takes time
		- making reliable requires immediately flushing write operations to disk
## 3) Openness (interoperability)
- an open distributed system is a system that offers components that can easily be used by, or integrated into, other systems
	- systems should conform to well-defined interfaces
	- systems should interoperate
	- systems should support portability of applications
	- systems should be easily extensible
## 4) Scalability
- what do we mean by scalability of a system?
	- Size: add more users and resources without loss of performance
	- Geographic: users and resources may be far apart, but latency delays are not noticed
	- Administrative: easily managed even if it spans many administrative organizations
- when a system needs to scale, there are many different types of problems that need to be solved
## 5) Dependable
- umbrella term that covers the trustworthiness of the system
- Available: system is ready for immediate use
- Reliable: system runs continuously without failure
- Safe: if failure happens, no catastrophic events occur
- Maintainable: a broken system can be reasonably impaired
- Dependability is metrics-driven via SLAs
	- uptime %, mean time to resolution, data durability
## 6) Secure
- Confidentiality and integrity of humans and processes are important in distributed systems:
	- authorization: determines whether an process or human is allowed to access a resource
	- authentication: verifying the correctness of a named identity

- size scalability problems
	- issues with centralized servers
		- computational capacity, limited cby CPUs
		- storage capacity, limited by I/O rate
		- network between the user and the centralized service
- geographic scalability issues
	- cannot simply go from LAN to WAN
		- LAN-based systems assume synchronous interactions: client sends request and waits for an answer
	- WAN links are inherently unreliable
	- Lack of multipooint communication, so a simple search broadcast cannot be deployed
		- develop separate naming and directory services, they might have their own scalability problems

- administrative scalability issues
	- most difficult and open question
		- conflicting policies with respect to resource usage, management, and security
## How can we address scaling issues?
### Scale up/out
- scale up: expand their capacity (increase CPUs, mem)
	- cost effective, maximizes existing hardware, easy to implement
	- limited to size of hardware, migration to new hardware
- scale out: expand the distributed system by deploying more nodes
	- break free from individual capacity/performance constraints, no limits, can continue to scale in the futre
	- hides communication latency, distributed work, state replication
### Hide communication latency
- try to avoid waiting for responses to remote-service requests as much as possible
	- make use of asynchronous communication
	- have separate handler for incoming response
- Probme: not every application fits this model (interactive apps)
	- must reduce communication
		- caching
		- moving compute to client
### Distribute work
- take a component, split into smaller parts, and subsequently spread those parts across the system
### Replication
- replication and cacheing - make copies of data available at different machines
	- replicated file servers and databases
	- mirrored web sites
	- web caches
	- file caching
- Applying replication is easy except for one thing...
	- having multiple copies leads to inconsistensies
		- modifying one copy makes it different from the rest
		- keeping copies consistent requires global synchronization
		- global synchronization precludes large-scale solutions

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
