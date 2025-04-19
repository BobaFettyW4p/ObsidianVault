- names are used to share resource, to uniquely identify entities, to refer to locations, and more
	- an important issue with naming is that a name can be resolved to the entity it refers to
		- name resolution allows a process to access the named entity
			- to resolve names, you need a naming system
- in a distributed system, the implementation of a naming system is often distributed across multiple machines
	- the implementation of this affects the efficiency and scalability of the distributed system
- there are 3 distinct ways that names are used in distributed systems:
	- flat-naming systems
	- structured naming systems
	- descriptive naming systems
## Names, identifiers, and addresses
- in a distributed system, a name is a string of bits or characters that is used to refer to an entity
	- an entity can be anything
		- hosts
		- printers
		- disks
		- files
		- processes
		- users
		- mailboxes
		- Web pages
		- etc etc.
		- entities can be oeprated on
			- ex. a resource that is a printer may offer interfaces containing operations for printing a document, requesting the status of a print job, and so on
- to operate on an entity, it is necessary to access it through an *access point*
	- a special kind of entity in a distributed system
	- the name of an access point is called an *address*
- an entity can offer more than one access point
- an entity may change its access points over time
	- when a mobile computer moves to a new location, it will often receive a new IP address
- an address is just a special kind of name
	- refers to the access point of an entity
- an entity may easily change access point, or an access point may be reassigned to a different entity
- if an entity offers more than one access point, it is not clear which address to use as a reference
	- a much better solution is to have a single name, independent of the addresses of the multiple access points
- a name for an entity that is independent of its addresses is often much easier and more flexible to use
	- this is called *location independent*
- there are other types of names that deserve special treatment:
	- a *true identifier* has the following properties
		1. each identifier refers to at most one entity
		2. each entity is referred to by at most one identifier
		3. an identifier always refers to the same entity (i.e. it is never reused)
		- using identifiers makes it much easier to unambiguously refer to an entity
		- if an address can be reassigned to a different entity, we cannot use an address as an identifier
			- ex. a phone numbers cannot be used as identifiers as they can be reassigned to different people over time
		- addresses and identifiers are two important types of names that are each used for very different purposes
			- in many systems, they're represented in machine-readable format only
	- *human-friendly names* are tailored to be used by humans
		- generally represented as a character string
- how do we resolve names and identifiers to addresses?
	- two main approaches:
		- maintain a generally distributed table of `(name, address)` pairs
			- followed by naming systems such as DNS
		- *information-centric routing* - a name is resolved by routing the request gradually to the name's associated address, or even directly to the address point
		- we will even challenge the need for name-to-address resolution when we discuss *named-data networking*
		- naming and resolving a name to an address is often referred-to as the *identifier-location split*
## Flat naming
- often identifiers are simply random bit strings
	- referred to as unstructured or *flat names*
- flat names do not contain any information on how to locate the access point of its assocaited entity
### Simple solutions
- two simple solutions to locate an entity:
	1. broadcasting
	2. forwarding points
	- both of these solutions are mainly applicable only to local-area networks
#### Broadcasting
- locating an entity within a LAN is easy: you can simply broadcast a message to the LAN asking if the recipient of the message has the entity
	- once a match is found, the entity in question will reply
- in principle, this approach is used in the Internet *Address Resolution Protocol (ARP)* to find the data-link address of a machine when given only an IP address
	- a machine broadcasts a packet asking for the owner of a given IP address, the owner responds
- broadcasting becomes inefficient as the network grows
	- bandwidth gets wasted by broadcast messages, and many hosts may be interrupted by messages they cannot answer
	- a possible solution is to switch to multicasting, where only a select group of hosts receive the request
		- ethernet networks support data-link level multicasting directly in hardware
- multicasting can be used to locate entities in point-to-point networks
	- Internet supports network-level multicasting by allowing a host to join a specific multicast group
		- these groups are identified by a multicast address
			- the network layer provides a best-effort way to deliver messages sent to this address to group members
- a multicast address can be used as a general location service for multiple entities
	- every member in a multicast group can be contacted by a request sent to the multicast address for the group
- also possible to associate a multicast address with a replicated entity and use multicasting to locate the nearest replica
	- when a request is sent to the multicast address, each replica will respond, and requesters can select the nearest one
		- more complicated than simply utilizing the first to respond
#### Forwarding pointers
- when an entity moves from location A to location B, it leaves in A a reference to its new location in B
	- this approach is simple
		- once an identity has been located, a client can look up the current address by following the chain of pointers
	- drawbacks:
		- without special measures, this chain can get extremely long
		- all intermediate links in the chain have to maintain their pointers in the chain for as long as it's needed
		- extremely vulnerable to failure
			- any pointer being lost breaks the chain and makes the entity unable to be found
### Home-based approaches
- a *home location* keeps track of the current location of an entity in many large-scale networks
	- techniques can be applied to safeguard against network or process failures
- a home-based approach is a fall-back mechanism for location services based on forwarding pointers
- ex. Mobile IP
	- each mobile host uses a specific IP address
		- all traffic intended for that IP is forwarded to the mobile host's *home agent*
			- the home agent is located on the local-area network corersponding to the IP address
			- when the home agent receives a packet for the mobile host, it looks up the host's current location and forwarded to them
	- an important feature is this entire process is hidden from applications
![[Pasted image 20250418113626.png]]
- an drawback illustrated above is that if a mobile host moves away from its home agent, traffic can travel very far uncessarily to reach the mobile host
- another drawback is a fixed home location
	- must ensure the home location always exists
		- otherwise it becomes impossible to contact the entity
		- also creates problems when a long-lived entity permanently moves to a new network
			- would be nice to move the home along with it
			- a possible solution is to register the home at a traditional naming service and let the client first look up the location of the home
				- this can be effectively cached after it has been looked up since the home should be relatively stable
### Distributed hash tables
- hash tables can be used to resolve an identifier to the address of an entity
#### General mechanism
- Chord system is a typical representative
	- uses an m-bit identifier space to randomly assign identifiers to nodes and keys to entities
		- the keys can be anything: files, processes, etc.
		- an entity with key k falls under the jurisdiction of the node with the smallest identifier id >= k
			- this node is called the successor of k and denoted as `succ(k)`
			- a naive approach would let each node `p` keep track of the successor `succ(p+1)`
				- whenever node `p` receives a request to resolve key `k`, it will forward the request to its successor unless `pred(p) < k <= p`, in which case p should return its own address
					- on average, a lookup needs to traverse half the ring
						- this isn't scalable
					- keeping track of the predecessor and forwarding a request in that direction will reduce the expected # of hops by 50%, which isn't good enough
						- this is fixed through *finger tables*
							- each Chord node maintains a finger table containing  `s <= m` entries
							- if `FTp` denotes the finger table of node `p`, then:
								- `FTp[i] = succ(p +2i−1)`
							- the ith entry points to the first succeeding node p by at least 2^i-1 units
								- these are shortcuts to existing nodes in identifier space
									- the shortcutted distance from node `p` increases exponentially as the finger table index increases
									- to look up a node `q` with index `j` in `p`'s finger table:
										- `q =FTp[j] ≤ k < FTp[j+1]`
![[Pasted image 20250418123054.png]]
- a finger table lookup generally requires `O(log(N))` steps, with `N` being the number of steps in the system
- in large distributed systems, the collection of participating nodes is expected to change over time
	- nodes will join and leave voluntarily
		- they will also fail
- joining a Chord system (DHT-based) is simple
	- if node `p` wants to join, it contacts a node within the system and requests a lookup for `succ(p+1)`
		- once this has been identified, `p` is able to insert itself into the ring
- the real complexity comes from keeping the finger tables up to date
	- for every node `q`, `FTq[1]` is correct as this refers to the next node in the ring
		- i.e. the successor of `q+1`
		- each node `q` regularly runs a simple procedure that contacts `succ(q+1)` and requests `pred(succ(q+1)`
			- this should refer to `q`, if it does, `q`'s information is consistent with that of its successor
				- if `q`'s successor has updated it's predecessor, then `q` has to discover its new successor
					- a new node `p` has entered the system, with `q < p <= succ(q+1)`
						- thus, `q` will update `FTq[1]` to `p`
							- `q` will also check whether `p` has recorded `q` as its predecessor
								- if it hasn't this process needs to be repeated
	- to update a finger table, node `q` needs to find the successor for `k = q + 2^(i-1)` for each entry `i`
		- this can be done by issuing a request to resolve `succ(k)`
			- these are done regularly in Chord using a background process
	- similarly, each node `q` regularly confirms its predecessor is still alive
		- if it is not, `q` will set `pred(q)` as unknown
			- conversely, if node `q` discovers that the predecessor of `succ(q+1)` is unknown, it can notify `succ(q+1)` that it suspects `q` is the predecessor of it
#### Exploiting network proximity
- one potential issue with Chord is that requests may be routed erratically across the internet
	- if nodes are geographically separated, then resolving a finger table has to route to all of these geographically separate locations
		- there are 3 different ways to make a DHT-based system aware of the underlying network:
			- *topology-based assignment of node identifiers* - assign identifiers such that two nearby nodes will have identifiers that are also close to each other
				- not difficult to image that this approach may impose severe problems in the case of relatively simple systems such as Chord
					- when node identifiers are sampled from a one-dimensional space, mapping a logical ring on the internet is far from trivial
						- such a mapping can easily expose correlated failures
							- nodes on the same enterprise network will have identifiers from a relatively small interval
								- when the network becomes unreachable, we have a gap in otherwise uniform distribution of identifiers
			- with *proximity routing*, nodes maintain a list of alternatives to forward a request to
				- instead of having a single successor, each Chord node could keep track of `r` successors
					- this can apply to every entry in a finger table
						- when a node needs to look up a key, it tries to prevent "overshooting" by passing the request to a node `q` with `k < q` without knowing if there is a node `q'` such that `k <= q' < q`
						- there is no reason why a node can't keep track of `r` nodes in a range
							- each node in this range can perform a lookup for `k` as long as `q <= k`
								- when a lookup request is processes, a node can pick any of the closest `r` successors geographically while also preventing overshooting
									- this also means that node failures do not necessarily lead to lookup failures
			- *proximity neighbor selection* optimizes routing tables such that the closest node is selected as its neighbor
				- this only works when there are multiple nodes to choose from
					- not often common in Chord
						- alternatives such as Pastry provide this
### Hierarchical approaches
- *Globe location service* - general-purpose location service that is representative of many hierarchical location services proposed for what are called Personal Communication Systems
	- hierarchical scheme
		- networks are divided into a series of *domains*
			- single top-level domain that spans the entire network
				- each domain can be subdivided into smaller subdomains
					- the smallest subdomain is called a *leaf domain*
						- generally a single LAN
					- within a smaller domain, the assumption is the time it takes to transfer a message from one host to another is smaller in comparison to a larger domain
	- Each domain `D` has an associated directory node `dir(D)` that tracks all entities within the domain
		- thus creates a tree of directory nodes
			- the directory node of the top-level domain, the *root directory node* knows about all entities
				- to track an entity, each entity in domain `D` is represented by a *location record* in the directory node `dir(D)`
![[Pasted image 20250418131027.png]]
- in contrast, the directory node `N'` for the next higher-level domain `D'` that contains `D` will have a location record for `E` containing only a pointer to `N`
	- Similarly, the parent node of `N'` stores a location record for `E` that only contains a pointer to `N'`
- an entity may have multiple addresses
	- e.g. if it is replicated
		- in this case, both domains will have their own record for each address
![[Pasted image 20250418131253.png]]
- as soon as the request reaches a directory node `M` that stores a location record for entity `E`, we know that `E` is somewhere in the domain `dom(M)` represented by node `M`
	- this request will be forwarded down the tree until found
- update operations can exploit locality similarly
	- the entity is searched in an increasingly wider ring around the requesting the client
		- each time the request is passed to a higher level directory node, the search range increases

![[Pasted image 20250418132048.png]]
- in other words, the chain of pointers is constructed from the bottom up
	- main advantage: an address becomes available for lookups as soon as possible
		- if a parent node is temporarily unreachable, the address can still be looked up within the domain represented by the current node
- a delete operation is analogous to an insert operation
	- when an entity needs to be removed, the directory node is requested to remove an address
		- if the location record becomes empty, then it can be removed
			- this continues until a pointer is removed from a location that remains nonempty or until the root is reached
### Secure flat naming
- flat names do not contain any information on how to resolve a name to the entity it is referring to
	- one needs to, in principle, rely on the name-resolution process to eventually access the entity
	- if name-resolution cannot be trusted, there is no reason to believe any response can be trusted
		- two approaches to follow:
			- secure the name-resolution process
			- secure the identifier to entity association
				- this occurs in a so-called *self-certifying name*
					- we can computer an identifier from an entity by simply using a hash function:
						- `id(entity) = hash(data associated with the entity)`
						- depends on what associated data is used
							- with non modifiable entities, a client can separately compute the hash and compare with the identifier used
								- in practice, entities are often modified
									- the entity is the current address of the object, and we need to make sure the current version is what we were hoping to connect to
				- if we are able to appropriately secure the identifier to entity association, trusting the name-resolution process becomes less important
					- worst case scenario is, the process returns false or no entities
						- but this is now testable, verifiable, and fixable
## Structured naming
- flat names are good for machines, but not convenient for humans to use
	- structured names are generally composed from simple, human-readable names
### Name spaces
- names are commonly organized into *name spaces*
	- name spaces for structured names can be represented as a labeled directed graph with two types of nodes
	- a *leaf node* represents a named entity and has the property that it has no outgoing edges
		- a leaf node generally stores information on the entity it is representing so clients can access
			- alternatively, it can store the state of the entity
	- a *directory node* has a number of outgoing edges, each labelled with a name as shown:
![[Pasted image 20250418201633.png]]
- each node in a naming graph is considered yet another entity in a distributed system and has an associated identifier
- a directory node stores a table in which an outgoing edge is represented as a pair *(node identifier, edge label)*
	- this table is called a *directory table*
- each path in a naming graph can be referred by the series of labels traversed in the path
	- this sequence is called a *path name*
		- if the first node in a path name is the root of the naming graph, it is called an *absolute path name*
			- otherwise it is called a *relative path name*
- names are always organized in a name space
	- as such, a name is always defined relative only to a idirectory node
		- the difference between global and local names can often be confusing
			- a *global name* is a name that denotes the same entity, no matter where in the system it is used
			- a *local name* is a name whose interpretation depends on where that name is being used
				- both of these are similar to computer file systems
- there are many ways to organize a name space
	- most name spaces have only a single root node
		- a name space is often strictly hierarchical
			- the naming graph is organized as a tree
				- each node, except the root, has exactly one incoming edge, the root has no incoming edges
		- a *directed acyclic graph* is one where a node can have more than one incoming edge, but the graph is not permitted to have a cycle
### Name resolution
- name spaces offer a convenient mechanism for storing and retreiving information about entities through names
	- given a path name, it should be possible to look up any information stored in the node referred to by that name
		- this process is called *name resolution*
#### Closure mechanism
- name resolution can only take place if we know how and where to start
	- this is referred to as  a *closure mechanism*
		- it deals with selecting the initial node in a name space, from which name resolution is to start
#### Linking and mounting
- *aliases* are strongly related to name resolution
	- another name for the same entity
	- two different approaches to implement an alias
		- first is to simply allow multiple absolute path names to refer to the same node
			- these are called *hard links*
		- second is to represent an entity by leaf node, but instead of storing the address or state of the entity, the node stores an absolute path name instead
			- these are called *symbolic links*
![[Pasted image 20250418203311.png]]
- name resolution can be used to merge different name spaces transparently
- in a *mounted file system*, it corresponds to letting a directory node store the identifier of a directory node from a different name space
	- this is called a *foreign name space*
	- the directory node storing the node identifier is called a *mount point*
	- the directory node in the foreign name space is called a *mounting point*
		- this is typically the root of the name space
- mounting principles can be generalized to other name spaces as well
	- a directory node that acts as a mount point and stores all the necessary info for identifying and accessing the mounting point in the foreign name space is often needed
- mounting a foreign name space in a distributed system requires at least the following information:
1. the name of an access protocol
2. the name of the server
3. the name of the mounting point in the foreign name space
- each of these names has to be resolved
	- none of these names may actually be needed in a nondistributed system
![[Pasted image 20250418203942.png]]
### The implementation of a name space
- a name space forms the heart of a naming service
- a naming service is implemented by name servers
	- if a distributed system is restricted to a local-area network, it is often feasible to implement a naming service by only a single name server
		- in large scale distributed systems, it is often necessary to distribute the implementation of a name space over multiple name servers
#### Name space distribution
- name spaces for a large scale distributed system are usually organized hierarchically
- the *global layer* is formed by highest level nodes
	- the root nodes are other directory nodes logically close to the root, i.e. it's children
	- these nodes are often characterized by their stability
		- the directory tables are rarely changed
- the *administrational layer* is formed by directory nodes that are managed within a single organization
	- a characteristic feature is that they represent groups of entities that belong to the same organization or administrational unit
		- there may be a directory node for each department in an organization
			- or a directory node from which all hosts can be found
- the *managerial layer* consists of nodes that may typically change regularly
	- nodes that represent hosts in the local network belong here
		- or user-defined directories and files
	- in contrast to the other layers, nodes are also managed by individual end users of a distributed system
![[Pasted image 20250418205748.png]]
- the name space is divided into nonoverlapping parts, called *zones*
	- a zone is part of the name space that is implemented by a separate name server
- name servers in each layer have to meet different requirements
	- high availability is critical for name servers in the global layer
		- if a name server fails, name resolution cannot proceed below the failing server
- due to the low rate of change of nodes in the global layer, the result of lookup operations remain valid for long periods of time
	- these results can be cached by clients
		- as a result, global name servers do not have to respond quickly to a single lookup request, but throughput may become a concern
- can meet availability requirements for global name servers by replicating servers and with client-side caching
- name servers in the administrational layer have similar characteristics as those in the global layer
	- caching results can still be highly efficient
	- in contrast, should pay attention to the speed at which requests can be processed
		- updates also need to be processed quicker than the global layer
- at the managerial level, availability requirements are less demanding
	- performance is critical however
	- since updates occur regularly, client-side caching is less effective
![[Pasted image 20250418210650.png]]
#### Implementation of name resolution
- the distribution of a name space across multiple name servers affects the implementation of name resolution
	- each client has access to a local *name resolver*
		- the name resolver ensures the name resolution process is carried out
- in *iterative name resolution*, the name resolver hands over the complete name to the root name server
	- it is assumed the address where the root server can be contacted is well-known
		- the root server will resolve the path as far as it can and return the result to the client
			- the client will then pass the remaining path to the next name server and the process will repeat until the full path is resolved
![[Pasted image 20250418211014.png]]
- an alternative to this is called *recursive name resolution*
	- a name server will pass the result the next one it finds
![[Pasted image 20250418211057.png]]
- the main drawback of recursive name resolution is it increases demand on each name server
	- this burden is so high that global layer name servers typically only support iterative resolution
- two important advantages:
	- caching results is more effective compared to iterative name resolution
	- communication costs may be reduced
- recursive name resolution allows each name server to gradually learn the address of each name server responsible for implementing lower-level nodes
	- caching is very effective at increasing performance
### Ex. The Domain Name System
- DNS is primarily used for lookup up IP addresses of hosts and mail servers
#### The DNS name space
- DNS is hierarchically organized as a rooted tree
	- a *label* is a case-insensitive string made up of alphanumeric characters
		- max length 64 characters
			- path length is restricted to 255 characters
	- each node in the DNS name space has exactly one incoming edge node
	- a subtree is called a *domain*
	- a path name to its root node is called a *domain name*
		- a domain name can be either absolute or relative
	- the contents of a node is formed by a collection of *resource records*
Major resource records:
![[Pasted image 20250418212015.png]]
- node in the DNS name space will often represent several entities at the same time
- an *SOA (start of authority)* resource record contains info such as an email address of the system administrator, the name of the host where data on the zone can be fetched, and so on
- An *A (address)* record represents a particular host on the internet
	- contains IP address for the host to allow communication
		- a node with multiple IP addresses will have multiple A records
- The *MX (mail exchange)* record, is like a symbolic link representing a mail server
- *SRV* records are related to MX records, which represent the name of a server for a specific service
	- the service is identified through a name along with the name of a protocol
- Nodes that represent a zone will contain one or more *NS (name server)* records
	- contains the name of a name server that implements the zone represented by the node
- DNS distinguishes alias from what are called *canonical names*
	- each host has a canonical, or primary, name
		- an alias is implemented using a node storing a CNAME record containing the canonical name of the host
- DNS maintains an inverse mapping of IP addresses to host names using *PTR (pointer)* records
	- to accomodate this, DNS maintains a domain named `in-addr.arpa` which contains nodes that represent Internet hosts and which are named by the IP address fo the represented host
- finally, a *HINFO (host info)* is used to store additional information on a host
	- such as machine type and operating system
- *TXT* records are used for any other kind of info that would be considered useful
### DNS implementation
- the DNS name space can be divided into a global layer and an administrational layer
- each zone is implemented by a name server which is always replicated for availability
	- updates for a zone are handled by the primary name server
		- they take place by modifying the DNS database local to the primary server
			- secondary name servers do not access the database directly, but instead requires the primary server to transfer its content
				- this is referred to as *zone transfer*
- A DNS database is implemented as a small collection of files, of which the most important one contains all the resource records for all the nodes in a particular zone
	- allows nodes to be simply identified through their domain name
#### Modern DNS
- there are a number of changes to the current DNS implementation that are important for understanding its behavior
- so far, we have assumed that clients contact a DNS server appliance, which will contact subsequent name servers
![[Pasted image 20250418214014.png]]
- in modern DNS, we see three phenomena
	- many organizations make use of an external DNS resolver
		- services like CDNs use the client's address to select a nearby server when resolving a URL
			- the URL received by the CDN is not the client's, but the DNS resolver the client uses
				- if the DNS resolver is not close to the client, the CDN may make a poor choice of where to deploy content from
	- many clients may actually bypass their local organization and directly contact a DNS resolver of their own choice
		- it makes it much harder to measure what is happening with DNS requests from an organizational perspective
	- increasingly fewer organizations are running their own DNS servers
		- outsourced to 3rd parties
- with so many public naming and lookup services, we need ways to check the validity of an answer
	- we also need to consider how to protect the privacy of end users
##### Securing DNS responses
- all top level domains support what is known as *DNSSEC (DNS Security extensions)*
	- in principle, a straightforward system where resource records are signed by the organization that owns a zone
		- DNS had to be extended in two ways to support this
			- new fields needed to be added to the set of records for containing signatures and keys
			- larger queries and responses had to be supported
	- basic idea: 
		- resource records of the same type are grouped into a separate set
			- subsequently signed
				- a zone will contain a separate set with all its IPv4 addresses, a separate set for IPv6 addresses, one for its name servers and so on
				- any client is able to verify the signature of a received result against the signed zones
					- require a *zone-signing key* to validate
						- the corresponding public key is called a *key-signing key*
##### Protecting DNS users
- in general, info stored in DNS is public
	- it's a huge open database that allows us to resolve human-friendly names to addresses and other data
		- in contrast, what a client is requesting from DNS is private
- systems allow applications to set up a secure channel to a  remote DNS resolver through TLS
	- prevents third parties from discovering to which websites an application is actually referring
- many modern browser support DNS queries of HTTPS as well
	- browser will directly contact a remote DNS resolver that supports this
	- offers the same protection as DNS over TLS
- queries between name servers can also be protected, but it's generally more efficient to let a name server ask for resolving only the relevant part of the path
### Ex. The Network File System
- the fundamental idea of the NFS naming model is to provide clients complete transparent access to a remote file system maintained by a server
	- achieved by letting a client mount a local file system into its own local file system
	- allows clients to mount only part of a file system:
![[Pasted image 20250418215755.png]]
- a server is said to *export* a directory when it makes that directory and its entries available to clients
	- an exported directory can be mounted into a client's local name space
	- one serious implication: users do not share name spaces
		- several potential solutions, most common is to provide each client with a partly standardized name space
- an NFS server can itself mount directories that are exported by other servers
	- it cannot export these directories to its own cleitns
		- instead, a client will have to explicitly mount such a directory from the server that maintains it
			- this restriction comes partly from simplicity - if a server could export a directory that it mounted from another server, it would have to return special file handles that include an identifier for a server
![[Pasted image 20250418220226.png]]
## Attribute-based naming
- flat and structured names generally provide a unique and location-independent way of referring to entities
	- structure names have been partly designed to provide a human friendly way to name entities so they can be conveniently accessed
		- generally assumed the name refers to a single entity
			- location independence and human friendliness are not the only criterion for naming entities
				- as more information is made available, it becomes more important to be able to search for entities
- it is popular to describe an entity in terms of *(attribute, value)* pairs
	- these are called *attribute-based naming*
		- each entity is assumed to have an associated collection of attributes
			- each attribute says something about the entity
### Directory services
- attribute-based naming systems are also known as *directory services*
	- systems that support structured naming are generally called *naming systems*
	- directory services have a set of associated attributes taht can be used for searching
- designing an appropriate set of attributes is not trivial
	- attribute design in general has to be done manually
	- the *Resource Description Framework (RDF)* has been a development that tries to standardize attribute design
		- resources are described as triplets consisting of a subject, predicate, and object
			- each subject, predicate and object can all be resources themselves
### Hierarchical implementations: LDAP
- a common approach to tackle distributed directory services is to combine structured naming with attribute-based naming
	- widely adopted in Active Directory and other systems
		- these rely on *Lightweight Directory Access Protocol (LDAP)*
- an LDAP directory service consists of a number of records, usually referred to as directory entries
	- a directory entry is comparable to a resource record in DNS
		- each record is made up of a collection of *(attribute, value)* pairs where each attribute has an associated type
			- a distinction is made bettween single-valued attributes and multiple-value attributes
![[Pasted image 20250418221457.png]]
- the collection of all directory entries in an LDAP directory service is called a *directory information base (DIB)*
	- each record in a DIB is uniquely named and can be looked up
		- such a globally unique name appears as a sequence of naming attributes
			- each naming attribute is called a *relative distinguished name (RDN)*
- as in DNS, the use of globally unique names by listing RDNs in sequence
	- this leads to a hierarchy of directory entries
		- referred to as a *directory information tree (DIT)*
			- a DIT forms the naming graph of an LDAP directory service
				- each node represents a directory entry
					- a node may also act as a directory in the traditional sense
						- there may be several children for which the node acts as a parent
![[Pasted image 20250418222102.png]]
- implementing an LDAP directory service proceeds in the same way as implementing a naming service
	- LDAP supports more lookup operations than DNS
	- in a large directory, the DIT is partitioned and distributed across several servers, known as *directory service agents (DSA)*
		- each part of a partitioned DIT corresponds to a zone in DNS
- clients are represented by *directory user agents*, or DUA
	- a DUA is similar to a name resolver in structured-naming services
- what makes an LDAP implementation different from a DNS implementation are the facilities for searching through a DIB
	- facilities are provided to search for a directory entry given a set of criteria that attributes of the searched entries should meet
	- searching in a directory service is generally an expensive operation
- LDAP can be taken to the next level by allowing several trees to coexist while also being linked to each other
	- this approach is followed in AD, which leads to a *forest* of domains
- LDAP by itself exploits hierarchy for scalability, but it's also common to combine LDAP with DNS
### Decentralized implementations
- with the advent of peer to peer systems, researchers have been looking for solutions for decentralized attribute-based naming systems
	- peer to peer systems are often used to store files
		- initially, files could not be searched, only looked up by key
			- we need a mapping of *(attribute, value)* pairs to *index servers*, which will point to files matching those pairs
#### Using a distributed index
- the basic idea is that a search query is formulated as a list of *(attribute, value)* pairs just as in LDAP
	- the result should be a list of references to entities that match all pairs
		- in the case of a peer to peer system storing files, a list of keys to relevant files may be returned, after which the client can look up all of the files using the keys
#### Space-filling curves
- a common approach to implementing decentralized attribute-based naming systems is to use *space-filling curves*
- the idea is to map the N-dimensional space covered by the attributes into a single dimension and then use a simple hasing technique to distribute the resultant space among index servers
	- a key issue is to have *(attribute, value)* pairs that are "close" to each other to be handled by the same index server

- there are many ways of supporting-attribute based naming systems in a decentralized fashion
	- the essence is to assign attributes to servers to clients know where to direct their queries, while balancing the queries amongst all server based on the attributes assigned
- instead of forcing a client to combine results, we can let servers collaborate
## Named-data networking
- a name needs to be resolved to an address to access the named entity normally
	- the name resolution process can be dropped in specific instances, such as Chord or other DHT implementation
		- can also be sidestepped by utilizing *Information-centric networking*
			- most popular form, *named-data networking (NDN)*
### Basics
- named-data networking revolves around the principle that applications are not really interested to know where an entity is stored, but rather that they can get a copy to access locally when needed
	- the main idea is that an application can retrieve an entity from the network using the entity's name and only the entity's name
		- the network accepts the name as input and subsequently routes a request to an appropriate location where the entity is stored and return a copy to the requester
- in NDN, names are assumed to be structured:
![[Pasted image 20250418224549.png]]
- named-data networking assumes that data cannot be modified without generating a new name
	- without it, efficiency may be at stake as caching by routers becomes far less effective
		- may seem to be a drawback, but as long as the user is aware, they can request an updated version
- named-data networking provides separate synchronization protocols to better facilitate updates
### Routing
- the most interesting part of NDN is how to realize routing and forwarding of requests for named data
- with seemingly arbitrary structured names, routing may be subject to serious scalability problemts
	- the problem is not fundamentally different than deploying IP addresses
- An NDN router consists of 3 elements:
	- a *content store* is essentially a cache for keeping data associated with a name
		- if a named request enters a router and the named data is in this cache, the router immediately returns that data to the requester
	- a *pending interest table* is nothing but a table that keeps track of a *(name, interface)* pair
		- if a request for data `N` arrived through interface `I` of the router, then `(N,I)` is stored
	- a *forwarding information base* tells the router what to do when it cannot serve a request
		- the request may be flooded to all neighboring routers, a random walk may be initiated, or the request may be dropped
			- these decisions are not fundamentally different from routing using IPv4 or IPv6 addresses
![[Pasted image 20250418225353.png]]
- when a request forwarded by a router returns a request, the router will look up whether there was a pending request, send it through the associated interface, and clear the entry in the pending interest table
	- the router may also decide to store the returned content in its content store for future requests
### Security in named-data networking
- a weak aspect of any naming system is that the name servers and routers need to be protected as well
	- there are numerous threats to NDN routers, which all boil down to effectively attempting to disrupt the service
- secure naming is about ensuring the provided name is unforgeably linked to the content it refers to
	- simplest way is to include a signed digest of that content into the name