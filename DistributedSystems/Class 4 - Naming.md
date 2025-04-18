Alternative title: "How to find things in a distributed system"
- textbook chapter 6

# Naming
- *names* play a critical role in all computer systems
	- share resources, identify entities, refer to locations, provide human readability
- to access an entity you must resolve the name to find the entity's address
	- name resolution is performed by a *naming system*
- distributed systems rely on distributed naming systems
	- how we do this directly affects efficiency and scalability
## What is a name?
- a string of characters used to refer to an entity

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
- what we ain to do is distribute:
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
### Pros and cons
- pros:
	- simplicity: as soon as the entity is located a client can look up the current address by following the chain of references

## Home-based approaches
- when mobility is common, a popular approach in large-scale systems is to introduce a *home location*
	- the home location tracks the current location of an entity
	- we often select the place where it was created as its home location
### How can we efficiently distribute name resolution? Distributed Hash Tables
- a DHT is a decentralized and fault-tolerant distributed system
	- stores key:value pairs, can be efficiently added/retreived
- P2P model, all processes are equal
	- nodes are processes, edges are comms channels
	- nodes can be added/removed with minimal effort and content redistribution
- typically a structured overlay network, i.e. a ring
- nodes are assigned random m-bit identifiers
- we need to decide which node to store a piece of data on
	- each data items gets hashed to an m-bit key
	- key(data) = hash(data_value)
	- data is stored with key k on node with smallest id >= k
	- this node is called on the successor of k and denoted succ(k)
#### Name resolution in Chord: naive
- how can we efficiently resolve key k to the address succ(k)?
- easy solution: each node p should keep track of the successor node succ(p+1) and its predecessor pred(p)
- we can now apply a linear search
	- when receiving a request to resolve a key it will be forwarded to one of its two neighbors (unless the key belongs to that node)
#### Finger tables
- each node maintains a finger table (shortcuts) of size s
	- where s <= m entries
		- intuition: the i-th entry points to the first node succeeding p by at least 2^i-1
		- to look up a key k, node p will immediately forward the request to a node q with index j in p's finger table:
			- q=FT(sub p)[j] <= k < FT (sub p) [j+1]
### Joining the network
- in large distributed systems, the collection of nodes can change frequently:
	- nodes may join or leave the network
	- nodes may fail (same as leaving) and recover (same as joining)
- complexity comes from keeping finger tables up to date
- joining is easy, if node *q* wants to join the system they:
	- contact an arbitrary node and request lookup for succ(q+1)
	- once identified, q can insert itself into the network, by telling succ(q+1) that it is now its predecessor
### updating pointers and finger tables
- each node q frequently runs the following procedure:
	- succ(q+1) and requests that node returns pred(succ(q+1))
	- if q = pred(succ(q+1)) then we know information is consistent
	- if not, then a new node has entered the system
		- q can update its finger table to point to the new node
### leaving the network
- easy, no need to do anything
- each node q will regularly check if its predecessor is alive
	- if it doesn't, it sets the predecessor as unknown
	- when the node checks its link to the next node, it won't reply
	- then check succ(q+1) where predecessor is unknown, then notify succ(q+1) that it suspects it to be the predecessor
## Hierarchical approaches (for flat names)
- a good solution to deal with scalability concerns is to structure name resolution into a hierarchy
- a network is divided into domains
	- a single root domain that spans the network
	- each domain subdivided into small subdomains
	- lowest level domain is called a leaf domain
- each domain has an associated directory node dir(D) that tracks entities in the domain
### Discussion: hierarchical approaches
- pros
	- efficient lookup caching
	- separate authorities can monitor different parts of the system
- cons
	- might contact many nodes to resolve a name
	- multiple points of catastrophic failure

## Structured naming
- structured names include some info that can help us locate entities
- systems generally support structured names that are composed from simple, human-readable names
	- contain structural separators
	- filesystem, DNS names
### Namespaces
- names are organized into *namespaces*
- namespaces can be represented as a *naming graph*, a directed graph with two types of nodes:
	- *directory node* has outgoing edges (labeled with a name)

### Name Resolution
- a path name is used to refer to a node in the naming graph
- a path name is represented by a sequence of edge labels separated by a slash
	- an absolute path name starts from the root node and begins with a slash
	- a relative path name does not start at the root node and does not begin with a slash
- given a path name we can look up information in order:
	- start at root node, lookup labels in order
### Alias
- an *alias* is another name for the same entity
- several ways to implement
	- hard link: allow multiple absolute path names to refer to the same node
	- soft link: allows a leaf node to contain the name of a differnet node
### Mounting a namespace
- name resolution can be used to merge different namespaces by mounting the node identifier of one namespace in a node in the current namespace
	- *foreign name space* - the name space that needs to be accessed
	- *mount point* - the node in the current namespace containing the node identifier of the foreign name space
	- *mounting point* - the node in the foreign namespace where to continue name resolution
### Closure mechanism
- challenge:
	- resolution only possible if we know how and where to start
	- a closure mechanism selects the implicit context from which to start name resolution
### Namespace implementation
- implementing the naming model is easy, how do we implement the naming system?
	- *availability* the most important, followed by *performance*
- aim: distribute the name resolution process across several machines called *name servers* by distributing nodes of the naming graph
- how to implement the name resolution process?
	- two basic approaches: iterative and recursive

#### Iterative name resolution
- resolver hands over complete name to the root name server using a well-known address
- root server will resolve path as far as it can and return to the client
- client will contact the next name server
- pros: intuitive, traceable, load balanced
- cons: heavy work for the client, caching is difficult (as it's the clients, not the server's responsibility)
#### Recursive name resolution
- client requests resolution from the root name server
- the root name server calls the next name server and so on, until the final name server returns the result
- Advantage: caching is more effective, communication costs reduced
- Disadvantage: additional load on name servers

## Domain Name System
 - hierarachically organized and structured namespace with each node having exactly one incoming edge
	 - labels are case-insensitive strings of max length 63 characters
		 - longest path name is 255 characters
	- label's separated by dots
	- a subtree is called a *domain*
	- a path name to a domain's root node is called a *domain name*

## DNS Resource records
- contents of a node is formed by a collection of resource records
	- nodes most often represent several entities at the same time

## DNS Implementation
- each zone is implemented by a name server
	- typically replicated for availability
	- updates for the zone are normally handled by a primary name server
		- hosts a local database
	- secondary name servers request the primary server to transfer its content
		- called a zone transfer

## Attribute-based naming
- often discovery of entites require more than just a name
	- important to search for entities using information about them
- most common way of providing descriptions is via key-value (or attribute-value) pairs
## LDAP (Lightweight Directory Access Protocol)
- common approach for distributed directory services is to combine structure naming with attribute-based naming
	- most systems build on LDAP
- LDAP derived from the OSI X.500 directory service
### Conceptual model
- contains records (or directory entries)
	- similar to a DNS resource record
- Records are made up of a collection of (attribute, value) pairs

### LDAP Naming
- each record is uniquely named so it cna be looked up
- expressed as a series of naming attributes
	- each attribute is a relative distinguished name
- like DNS, you can form a naming tree by stepping through the hierarchy
- unlike DNS, you can query a specific record
