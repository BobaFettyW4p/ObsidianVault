- a process is a program in execution
	- from an OS perspective, the management and scheduling of processes are perhaps the most important issues to deal with
		- in distributed systems, other issues turn out to be equally as important
- threads play a crucial role in obtaining performance in multicore and multiprocessor environments, but also help structure clients and servers
## Threads
- although processes for a building block in distributed systems, a finer granularity in the form of multiple threads of control per process makes it much easier to build distributed applications and get better performance
### Introduction to threads
- to understand the role of threads in distributed systems, it is important to understand what a process is and how processes and threads relate
	- to execute a program, an operating system creates a number of *virtual processors*, each one for running a different program
		- to keep track of these, the OS has a *process table*
			- containing entries to store CPU register values, memory maps, open files, accounting info, privileges, etc
				- jointly, these entries form a *process context*
- a process context can be viewed as the software analog of hardware's *processor context*
	- consists of the minimal information that is automatically stored by the hardware to handle an interrupt, and to return to where the CPU left off
- a *process* is often defined as a program in execution
	- a program that is currently being executed on one of the operating system's virtual processors
		- the operating system takes great care to ensure that independent processes cannot maliciously or inadvertently affect the correct of another's behavior
			- the fact that multiple processes may concurrently share the same CPU is made transparent
				- this generally requires hardware support
				- this concurrency transparency comes at a price
					- each time a process is created, the OS must create an independent address space
					- similarly, switching a CPU between processes also carries some overhead
						- if more processes are placed on a CPU than it can support, it will have to swap them between main memory and disk
- similar to a process, a thread executes its own piece of code independently of other threads
	- in contrast to processes, no attempt is made to achieve concurrency transparency if this would affect performance
	- a thread system generally only maintains the minimum info to allow a CPU to be shared by several threads
		- a *thread context* often consists of nothing more than the processor context along with some other information for thread management
			- protecting data within a thread context against inappropriate access is almost exclusively left to application developers to handle
- two important implications of deploying threads:
	- will rarely be worse-performing than a single threaded application, and often better
	- since threads are not protected like processes, they require more intellectual effort to safely deploy
#### Thread usage in non-distributed systems
- in a single-threaded process, whenever a blocking system call is executed, the process as a whole is blocked
- it also becomes possible to exploit parallelism when executing a program on a multiprocessor or multicore system
	- each thread is assigned to a different CPU or core, while shared data is stored in shared memory
		- when designed well, this parallelism is transparent, the user is not aware their program utilizes multiple threads or processes
- also important for large applications
	- these are designed as a collection of cooperating programs, each operating on a separate process
		- cooperation is implemented through *interprocess communication (IPC)* mechanisms
			- on Unix systems, these are typically named pipes, message queues, and shared memory segments
				- major drawback is that IPC communication requires extensive context switching
![[Pasted image 20250420084233.png]]
- conversely, you can just use threads
	- thread switching can sometimes be done in user space, although the kernel schedules them in others
		- can lead to a dramatic performance impact
		- there is a pure software engineering reason to use threads: many applications are simply easier to structure as a collection of cooperating threads
			- applications that need to perform several independent tasks
- when variables are shared between threads, they share the actual variable, where each process will have its own copy of the variable to operate on
#### Thread implementation
- threads are often implemented in the form of a thread package
	- this package contains operations to create and destroy threads, as well as operations on synchronization variables such as mutexes and condition variables
		- two approaches to implement a thread package:
			- the first approach is to construct it entirely in user space
				- advantages:
					- it is cheap to create and destroy threads
					- all thread administration is kept in user space, the price of creating thread is determined by the cost to allocate memory to set up a thread stack
						- destroying a thread simply means reclaiming this memory
							- both of these operations are cheap
					- switching thread context in user space only takes a few instructions
						- only the CPU register values need to be stored and reloaded to switch thread context
				- drawback:
					- the *many to one* threading model
						- multiple threads are mapped to a single schedulable entity
							- invocation of a blocking system call will immediately block the entire process to which the thread belongs
						- can be resolved by implementing threads in the kernel
							- the *one-to-one* threading model
								- every thread is a schedulable entity
								- every thread operation is carried out by the kernel
									- therefore require a system call
										- makes switching thread contexts more expensive
			- have the kernel be aware of threads and schedule them
- using processes instead of threads has the advantage of separating the data space
	- each process works on its own part of the data and is protected from interference by others by the OS
### Threads in distributed systems
- threads provide a convenient means of allowing blocking system calls without blocking the entire process in which the thread is running
	- very attractive in distributed systems
		- allow one process to maintain many connections at once
#### Multithreaded clients
- to maintain distribution transparency, clients in distributed systems need ways to conceal long interprocess message propagation times
	- typical way forward is to initiate communication then immediately proceed with something else
		- i.e. a web browser sets up a connection, then fetches the HTML data initially before proceeding with the more complex CSS elements
			- multithreaded web servers also help in opening several communications simultaneously
				- if the server isn't multithreaded, the client being multithreaded won't matter
#### Multithreaded servers
- the main use of multithreading in distributed systems is found at the server side
	- multithreaded servers to simplify server code in practice, and makes it easier to attain high performance
- e.g. a fileserver that occasionaly has to block waiting for the disk
	- the server normally waits for a request, carries it out, and replies
	- you can have one thread, the *dispatcher*, read incoming requests for a file operation, and then sends the request to a *worker thread* to handle the request
		- the worker will then perform the blocking read
![[Pasted image 20250420104559.png]]
- the worker threads do the actual work, the dispatcher thread performs the logic that determines when it is correct for a worker to perform blocking work
- without threads, things get more complicated
	- when written as a single thread, there would be a lot of idle time as the server performs the read but is not available to receive subsequent requests until the read completes
		- also leads to CPU being idle while the disk is being read
![[Pasted image 20250420104940.png]]
- threads make it possible to retain the idea of sequential processes that make blocking calls, but still achieve parallelism
	- processes can server a similar purpose, but if processes need to communicate a lot, those communications will have an adverse effect on performance
## Virtualization
- the separation between having a single CPU and being able to pretend there are more leads to what is known as *resource virtualization*
### Principle of virtualization
- *virtualization* deals with extending or replacing an existing interface to mimic the behavior of another system
![[Pasted image 20250420105434.png]]
#### Virtualization and distributed systems
- one of the most important reasons for introducing virtualization was to allow legacy software to run on expensive mainframe hardware
	- in the modern day, hardware and low-level systems change much more frequently than software, which leads to this need remaining into the modern day
- equally important is that networking has become completely pervasive
	- this requires system administrators to maintain a large and heterogeneous collection of server computers, each one running very different applications
		- various resources need to be available to each of these applications
			- virtualization helps a lot: let each application run on its own VM, all of which in turn run on a common platform
				- provides a lot of flexibility and portability
#### Types of virtualization
- computer systems generally offer four different types of interfaces:
1. An interface between hardware and software, called the *instruction set architecture (ISA)*
	- forms the set of machine instructions and is divided into two subsets:
		- privileged instructions to be executed only by the OS
		- general instructions that any program can execute
2. an interface consisting of *system calls* as offered by an operating system
3. an interface consisting of library calls, generally forming an *application programming interface (API)*
![[Pasted image 20250420110352.png]]
- two different ways to implement virtualization:
	- build a runtime system provides an abstract instruction set to be used to execute applications
		- instructions can be interpreted but can also be emulated
			- if emulated, the emulator must mimic the behavior of system calls
		- called a *process virtual machine*
	- build a system implemented as a layer shielding the original hardware, but offering the complete instruction set as an interface
		- called a *native virtual machine monitor*
			- implemented directly on top of underlying hardware and can be offered simultaneously to different programs
				- enables multiple and different *guest operating systems* to run independently and concurrently on the same platform
			- has to provide and regulate access to various resource
				- a *hosted virtual machine monitor* will run on top of a *host operating system* to implement device drivers for these resources
### Containers
- virtual machines offer a means to run applications on a specific operating environment, but requires significant efforts to ensure portability and performance
- a *container* is a collection of binaries that jointly constitute the software environment for running applications
- applications and processes operating in different containers need to be isolated from each other
	- similarly, copying the entire environment is also not very efficient
	- containers accomplish this through several important mechanisms:
		- *namespaces* - a collection of processes associated with a container is given their own view of identifiers independent of other contains
			- important to give a process running inside a container the view that it is on its own
		- *union file system* - allows to combine several file systems into a layered fashion with only the highest layer allowing for `write` operations
			- very similar to mounting a file directory
				- successive calls to `mount` are performed with each layer (except the highest) being in a read-only mode
		- *control groups* (also known as *cgroups*), which allow resource restrictions to be imposed upon a collection of processes
			- the host OS restricts contains that use too many resources, ensuring they're available for other processes
![[Pasted image 20250420113320.png]]
### Comparing virtual machines and containers
- differences in resource utilization between containers and VMs can be relatively close, with very small differences in performance
	- may also have difficulties benchmarking, but it's more natural for applications to run side by side anyway
		- big question: to what extent does the performance of one application influence that of another?
	- big takeaway: innovations over the years have shown there is no real need for virtualization technologies to perform significantly slower than containerized ones
### Application of virtual machines to distributed systems
- cloud providers offer three different types of servers
	- *Infrastructure-as-a-service (IaaS)*
		- virtualization plays a key role
			- instead of renting out a physical machine, users rent a virtual machine and may be sharing a physical machine with other customers
				- not a concern due to almost total isolation between customers
	- *Platform-as-a-service (PaaS)*
	- *Software-as-a-service (SaaS)*
## Clients
### Networked user interfaces
- a major task of client machines is to provide the means for users to interact with remote servers
	- two ways to support:
		- give the client a counterpart that can contact each service over the network
		- provide direct access to remote services by offering a convenient user interface
			- client machine is only a terminal with no need for local storage
			- this *thin-clinet* approach has received a lot more attention with the rise of Internet connectivity and mobile devices
![[Pasted image 20250420114852.png]]
### Virtual Desktop Environment
- as cloud computing matured, it became opportune to turn the cloud into a *virtual desktop environment* for end users
	- still needed the client-side software to access the desktop environment
		- *Chrome OS*
			- principle is simple: let the browser provide the local desktop interface
				- multiple stand alone applications can be a part of it
#### The anatomy of a web browser
- Most content parsing done by a web browser is done by a separate component called the *Document Object Model (DOM)*
	- a DOM is a tree representation of the HTML file
- while the DOM represents structure, actual styling information is provided by a separate document
	- this info is parsed separated and added to the DOM, yielding a render tree
		- contains all info for where objects in the tree are rendered on screen
			- a series of paint operations are performed by the painting component
![[Pasted image 20250420120423.png]]
- every browser also has a separate component for handling scripts
	- *JavaScript* is most common, but *WebAssembly* is also common 
	- gives the user the illusion they are running a fully featured application ont he local desktop
- to improve security, each renderer in a browser runs in a separate *sandbox*
	- a protection mechanism that precludes direct communication with the underlying OS
#### Browsers and applications
- having a browser is sufficient to offer a virtual desktop environment
	- browsers can execute scripts as the client-side part of an otherwise remote application
		- can largely handle everything to provide the illusion the user working on a local machine
### Client side software for distribution transparency
- ideally, a client should not be aware that it's communication with remote processes
	- distribution is often less transparent to servers for reasons of performance and correctness
- *access transparency* is generally handled through creation of a *client stub* from an interface definition from the server
	- the stub provides the same interface as available on the server, but hides the differences in machine architectures and actual communication
- various methods to handle *location*, *migration*, and *relocation transparency*
	- convenient naming is crucial
		- so is cooperation with client-side software
	- many systems also implement *replication transparency* employing client-side solutions
![[Pasted image 20250420121936.png]]
- *failure transparency* is typically done through client middleware
- *concurrency transparency* can be handled through special intermediate servers (transaction monitors) and requires less support from client software
## Servers
### General design issues
- a server is a process implementing a specific service on behalf of a collection of clinets
	- each server is organized in the same way: it waits for an incoming request for a client, handles it, then waits for the next
#### Concurrent v. iterative servers
- in an *iterative server*, the server itself handles the request, and if necessary, returns a response to the requesting client
- a *concurrent server* passes the requests to a separate thread process and immediately begins waiting for another request
#### Contacting a server: end points
- another issue is where end clients contact a server
	- all clients send requests to an *end point* at the machine where the server is running
		- server listens on the end point waiting for requests
- how do clients know the end point of a service?
	- well-known services have globally agreed upon end points
		- assigned by the *Internet Assigned Numbers Authority (IANA)*
	- many services do not require a pre-assigned end point
		- client then has to look the endpoint up
			- can have a special daemon on each server that collects all the endpoints, and forwards traffic from a well-defined endpoint to these servers
	- common to associate an end point with a specific service, but actually implementing each service on a separate server may be a waste of resources
		- may be more efficient to have a single *superserver* that listens to each end point associated with a service
![[Pasted image 20250420124545.png]]
#### Interrupting a server
- important design consideration
	- generally a good idea to design the client and server so that it's possible to send *out-of-band data*, which is processed by the server before any other data from the client
		- the server can have a separate end point that receives out of band data only, while also having a second end point for normal client data
			- TCP also has the capability to support out of band data on the same interface as the normal data
#### Stateless v. stateful servers
- a *stateless* server does not keep information on the state of its clients, and can change its own state without notifying any client
	- a web server is stateless
	- the server does maintain client info in many stateless designs, but if this info is lost, the service offered is not disrupted
	- a *soft state* is where a server offers to maintain a specific state for a client, but only for a specified period of time
		- afterwards, the server will return to default behavior and update state as needed
- a *stateful* server generally maintains persistent information on its clients
	- information needs to be explicitly deleted by the server
	- can improve performance of read and write operations as perceived by the client
		- performance improvement is often an important benefit of stateful designs
			- main drawback is difficulties recovering from failure
				- it has to recover its full table or else it cannot guarantee it has processed the most recent updates
	- should make a distinction between *session state* and *permanent state*
		- session state is associated with a series of operations by a single user and should be maintained for some time but not indefinitely
			- often maintained in three-tiered client-server architectures
		- permanent state is typically information maintained in databases, such as customer info, keys associated with purchased software etc.
			- maintaining session state already implies a stateful design requiring special measures when failures do happen
- choosing between stateless or stateful design should not affect the services provided by the server
### Object servers
- the difference between a general object server and more traditional servers is that an object server does not provide a specific services
- specific services are implemented by the objects that live in the server
	- the server only provides the means to invoke local objects based on requests from remote clients
		- easy to add/remove servers by adding/removing objects
- an object consists of two parts: data representing its state and the code for executing the methods
	- these parts may be separated depending on client/server implementation
- for an object to be invoked, the object server needs to know which code to execute, on which data it should operate, whether it should start a separate thread
	- simple approach is to assume all objects look alike and are invoked in the same way'
		- plenty of limitations
	- much better to allow a server to support different policies
		- a *transient object* is an object that exists only as long as its server exists, and maybe less
			- needs the server's resources only as long as its actually needed
				- downside is an invocation may take some time to complete as the object must be created prior to being used
			- could also place each object in its own memory segment
				- objects share neither code nor data
- many policies regarding threading
	- simplest approach is to implement with a single thread of control
		- server may have several threads, one for each of its object
			- when an invocation request comes in, the server passes the request to the responsible thread
- decisions on how to invoke an object are commonly referred to as *activation policies*
	- emphasize that the object itself must be brought into the server's address space before it can be invoked
	- need a mechanism to group objects per policy
		- called an *object adapter* or *object wrapper*
			- can be thought of as software implementing a specific activation policy
			- has one or more objects under its controlling
			- object adapters are unaware of the specific interfaces of the objects they control
				- could not be generic otherwise
![[Pasted image 20250420140401.png]]
### Server clusters
#### Local area clusters
- a server cluster is nothing else but a collection of machines connected through the network
	- each machine runs one or more servers
#### General organization
- a server cluster is often logically organized into three tiers
	- the first tier is a logical switch through which requests are routed
	- many servers will also be dedicated to application processing
	- the third tier are the data processing servers
![[Pasted image 20250420141423.png]]
- when a server cluster offers multiple services, different machines may run different application servers
	- the switch has to be able to distinguish services to be able to forward to the correct machines
#### Request dispatching
- the first tier of a server cluster is referred to as the *front end*
	- important goal is to hide the fact there are multiple servers in the cluster
	- the front end offers a single access point to ensure access transparency
- in practice, there are two kinds of switches:
	- a *transport-layer switch* accepts incoming TCP connection requests, and hands off such connections to one of the servers
		- all client communications are routed through the switch
	- *application-layer switches* operate by inspecting the content of the request and making decisions accordingly
		- the more a switch knows about what is being requested, the better it can forward requests
#### Wide-area clusters
- cloud providers manage data centers placed at different locations worldwide
	- offer the end user the ability to build a wide-area distributed system consisting of a large collection of networked virtual machines scattered across the internet
		- key motivation is to be able to serve content locally: the closer the video server, the easier it is to provide high-quality streams
			- approach followed by cloud-based *Content Delivery Networks (CDNs)*
#### General organization of a CDN
- there is an *origin server* that hosts a website with all of its documents
	- each document is referred to by a URL containing the *domain name* of the origin server
		- to access a document, that name must be resolved to a network address, which is done by *Domain Name System (DNS)*
			- A CDN will ensure those names are resolved to an edge server part of the CDN
				- the client looks up the regular domain name, but is redirected to the CDN resolvers instead
					- the resolvers will look up the best edge server to serve the client and direct the client's requests there
![[Pasted image 20250420142254.png]]
#### Request dispatching
- by properly redirecting clients, a CDN can stay in control when it comes to client-perceived performance, but also considered global system performance
	- requests can avoid heavily-loaded servers, for example
		- *adaptive redirection policies* are used to determine this
	- Is request redirection transparent to the client?
		- it depends, but there are only three main techniques to do so:
			- TCP handoff
			- DNS redirection
				- transparent mechanism by which the client is unaware of documents are located
				- may carry an additional communication cost
				- may not be possible as the local DNS server is not being used at all
			- HTTP redirection
				- nontransparent method
				- client is given an alternative URL to which it is redirected
## Code migration
- sometimes, passing executing programs between nodes in a distributed system simplifies it
### Reasons for migrating code
- traditionally, code migration took place in the form of *process migration*
	- an entire process would be moved between nodes
		- this is a costly and intricate task
#### Performance
- overall system performance can be improved if processes are moved from highly loaded to lightly loaded machines
	- load is often expressed in terms of CPU queue length or CPU utilization, but other metrics can be used as well
- we can witness that code is moved to make sure a machine is sufficiently loaded
	- e.g. migrating virtual machines to lightly loaded machines to minimize the total # of nodes being used is common to optimize energy usage
		- migrating VMs requires more resources, but is simpler than migrating a process
- in general, performance improvement through code migration is in general less important than minimizing communication
#### Privacy and security
- moving code to where the data is often has to do with security
	- example found in *federated learning*
		- comes from the need to train machine-learning models
![[Pasted image 20250420143320.png]]
- a traditional approach is to collect the training data at a centralized location
	- could mean sensitive data being handled to an organization that is not trusted
		- better to bring the model to where the data is
			- this, while still complicated, is easier than fully migrating processes
#### Flexibility
- the traditional approach to building distributed applications is to partition the application into different parts and decide in advance where they all should live
	- if code can move between different machines, it's now possible to dynamically configure distributed systems
	- can also let the server provide the client's implementation no sooner than strictly necessary
		- requires code to be more sanitized
![[Pasted image 20250420143702.png]]
- biggest downside is security
	- blindly trusting downloaded code is risky
### Models for code migration
- traditionally, communication in distributed systems is concerned with exchanging data between processes
	- code migration depends on moving programs between machines
		- those programs have to be executed at the target
- a process consists of three segments:
	- *code segment* - the part that contains the set of instructions that make up the program being executed
	- *resource segment* - contains references to external resources needed by the process
		- files
		- printers
		- devices
		- etc.
	- *execution segment* - used to store the current execution state of a process
		- private data
		- the stack
		- program counter
- can distinguish between sender-initiated and receiver-initiated migration
	- *sender-initiated migration* is initiated at the machine were code resides or is being executed
		- typically done when uploading programs to a remote server
	- *receiver-initiated migration* is when initiative is taken by the target machine
	- receiver initiated is simpler
- Four different paradigms for code mobility:
	- *client-server computing*
		- all code is stored and executed on the server, results are returned to the client
	- *remote evaluation*
		- the client migrates to the server, where it will be executed and the results returned to the client
	- *code-on-demand*
		- the client receives code from the server, with its execution modifying the client-side execution state and running on client resources
	- *mobile agents*
		- allow a sender-initiated approach
			- move code and execution state to the server
			- operates on both client and server's resources
![[Pasted image 20250420144422.png]]
- code migration is only expected to provide *weak mobility*
	- it is possible to transfer only the code segment and perhaps some initialization data
	- the transferred program is always started over after migration
- systems that support *strong mobility* allow the execution segment to be transferred as well
	- a running process can be stopped, moved, then resumed exactly where it left off
	- more general, but much more difficult to implement
### Migration in heterogeneous systems
- the problems stemmed from heterogeneity are not different from those of portability
	- solutions are similar
	- a simple solution was to generate machine-independent intermediate code for an abstract virtual machine
		- each system would generates this abstract VM a little different to comply with their requirements
			- this is still tackled today by scripting languages like Java or Python
- VM migration enables decoupling a computing environment from the underlying system and actually migrate it to another machine
	- processes can even remain ignorant of the migration itself
	- three main ways to handle migration (that can be combined)
		- push memory pages to the new machine and resend ones that are later modified
		- stop the current virtual machine, migrate memory, then restart the VM
		- let the new VM pull in new pages as needed, let processes start on the new VM and copy memory pages on demand
