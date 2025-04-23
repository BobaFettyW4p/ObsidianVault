Alternative title: "How to find things in a distributed system"
- textbook chapter 6

# Naming
- *names* play a critical role in all computer systems
	- share resources, identify entities, refer to locations, provide human readability
- to access an entity you must resolve the name to find the entity's *address*
	- name resolution is performed by a *naming system*
- distributed systems rely on distributed naming systems
	- how we do this directly affects efficiency and scalability
![[Pasted image 20250423160825.png]]
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

## Home-based approaches
- when mobility is common, a popular approach in large-scale systems is to introduce a *home location*
	- the home location tracks the current location of an entity
	- we often select the place where it was created as its home location
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
## Hierarchical approaches (for flat names)
- a good solution to deal with scalability concerns is to structure name resolution into a hierarchy
- a network is divided into *domains*
	- a single *root domain* that spans the network
	- each domain subdivided into small *subdomains*
	- lowest level domain is called a *leaf domain*
- each domain has an associated directory node `dir(D)` that tracks entities in the domain
![[Pasted image 20250423170146.png]]
### Lookup
![[Pasted image 20250423170210.png]]
- our user is looking to look up a name `N`
	- the request traverses up the tree until it hits a node that has knowledge of that name
		- once this is found, it then works its way down the tree until the requested name is found

### Insert

![[Pasted image 20250423170310.png]]

- to insert a record, the request will traverse the tree until we hit a node that has knowledge of the domain the name will live
	- it then traverses its way down the tree until that domain is found
		- the node responsible for hosting the name will then create a new child node to host the record
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
### Closure mechanism
- challenge:
	- resolution is only possible if we know how and where to start
	- a **closure mechanism** selects the implicit context from which to start name resolution
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

## Attribute-based naming
- often discovery of entitles require more than just a name
	- important to *search* for entities using information about them
- most common way of providing descriptions is via key-value (or attribute-value) pairs
- attribute-based naming models assume that each entity is made up of a collection of attributes
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
