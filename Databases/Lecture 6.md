# NoSQL and Key-Value Database

- what is NoSQL
	- differentiate from a traditional RDBMS
- NoSQL systems store and retreive data from many formats
	- key-value stores
	- graph databases
	- column-family stores
	- document stores
	- rows in tables
- there are no joins
- schema-free
- works on many processors
	- can store on multiple processors and maintain high-speed performance
### Advantages
- scalability
- flexibility
- performance

### Types of NoSQL data stores
- key-value store
	- simple data store, use a key to access a value
		- shopping carts, user profiles, user preferences, caching, and session data in web applications
	- Ex.
		- memcache
		- redis
		- riak
		- dynamoDB
- Document store
	- store data in document-list structures
		- e.g, JSON
		- web-content management
		- real-time event/interaction logs
		- rapidly evolving schemas
	- Ex. MongoDB
- Graph store
	- store data in nodes and edges
		- ideal for data with complex relationships
	- social networks
	- fraud detection
	- relationship-heavy data problems
	- Ex. Neo4j

### What is a key-value store
- the client can either get the value for the key, put a value for a key, or delete a key from the data store
	- the value is a blob
		-  we don't care what's inside
	- supports storing multiple random data structures

### Operations
- Use SET operation which requires two parameters: a key an a value
	- `SET uchicago http://www.uchicago.edu/`
- redis stores strings
	- recognizes integers, can perform some simple operations
- key-value pairs live forever unless an expiration is specified

redis supports storing lists, hashes, sets, and sorted sets

redis lists 


graph databases
nodes - data points
edges - relationships

not so much to store information about thins as to tie them together

the simplest graph has a single node with some named values called properties
- nodes often represent entities or discrete objects that can be classified with zero or more labels
	- data is stored as properties of the nodes
	- properties are stored as key-value pairs
- nodes can be grouped by applying a label to each member
	- a node can have zero or more labels
	- labels are used to classify nodes
	- properties can hold different data types, such as number, string or boolean
		- can be a homogenous list containing strings, numbers or boolean values
		- relationships can contain properties that describe the relationship

Neo4j via Cypher
- rich neo4j specific graph traversal language
	- MATCH some set of nodes or relationships
	- WHERE some set of properties holds
	- RETURN some set of results captured by the MATCH and WHERE clauses
Query all
```
MATCH (n)
RETURN n;
```