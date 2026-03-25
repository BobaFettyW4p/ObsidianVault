## Document Databases

### What is a document?
- a *document* is the basic unit of data and is roughly equivalent to a row in a relational database management system
	- we don't say row, we say document
- a *collection* is a group of documents
	- we don't say table, we say collection
- a document database has no defined schema
	- in an RDBMS, every row in a table has to follow the same schema
	- embedding child documents as subobjects provides for easy access and better performance
- document keys and values are not of fixed types or sizes
- schema can be defined on the fly, but you may have to pay for these design decisions later on
- popular document databases
	- MongoDB
	- CouchDB
	- Terrastore
	- OrientDB
	- RavenDB
### Topology
- MongoDB is designed to scale out
	- document-oriented data model makes it easier to split data across multiple servers
- common MongoDB topologies
	- standalone
		- one mongoDB, one data set
		- pros: simplest, fine for labs/dev
		- cons: no high availability, no automatic failover, limited scale
	- replica set (high availability and read scaling)
		- group of mongodb nodes that manage the cluster
	- sharded cluster (horizontal scaling)
		- scale out writes and data volume by partitioning data across multiple machines

