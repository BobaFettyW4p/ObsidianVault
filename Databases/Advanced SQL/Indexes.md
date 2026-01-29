- an *index* in MySQL is a data structure that improves the speed of data retreival operations on a database table

### How Do Indexes Work?
- internally, MySQL uses a data structure called a *B-tree* (a balanced tree) for most of its indexes.
	- this allows the database to find a specific row or range of rows efficiently

### Types of Indexes
- B-tree indexes
	- good for high-cardinality columns where there is a wide range of values
![[../attachments/Pasted image 20260128070302.png]]
- bitmap indexes
	- works well for low-cardinality columns
		- these have a modest number of distinct values,
			- absolutely
			- relative to the number of records that contain the data

### Types of Indexes in MySQL
- **Primary key**
	- a unique index where all key column values must be unique
	- each table can have at most one primary key
- **Unique index**
	- ensures that all values in the index must be unique
- **Full-text index**
	- used for full-text searches
	- allows users to search for words or phrases in the documents
- **Composite Index**
	- also known as a *Multiple-Column Index* 
	- similar to other types of indexes, but uses multiple columns for the index

### Create an Index
```
-- general form
CREATE INDEX <index-name> ON <relation-name> (<attribute-list>);

CREATE INDEX my_index ON my_table (log_date);
```
- indexes can take a long time to create if there's a lot of rows in the relation you're indexing
	- not quite, but similar to creating a whole new table

### Create a Unique Index
- you add the attribute UNIQUE to the index definition
- `CREATE UNIQUE INDEX dept ON instructor (dept name);`

### Create a Full-text Index
- the attribute FULLTEXT to the index definition
- `CREATE FULLTEXT INDEX dept ON instructor (dept name);`

### Deleting an index
- `ALTER TABLE my_table DROP INDEX my_index`
	- all other data in the table will be untouched

### Show index
- all database servers allow you to view the available indexes
- in MySQL
	- `SHOW INDEX FROM customer`

### Create Multicolumn Index
- you can also create a multicolumn index or a composite index
	- `ALTER TABLE customer ADD INDEX idx_full_name (last_name, first_name);`
- the order of the columns matters
	- the most selective columns should always be chosen first

### Single vs Multiple Columns Index
- Using a `(country, population)` index works with
	- `SELECT * FROM city WHERE country = 'USA' and population = 1000000`
- Using a `(country, population)` index also works with
	- `SELECT * FROM city WHERE country = 'USA';`
- Using a `(country, population)` index does NOT work with
	- `SELECT * FROM city WHERE population = 1000000`

### How Indexes are Used
- Consider the following query
	- `SELECT customer_id, first_name, last_name FROM customer WHERE first_name LIKE 'S%' AND last_name LIKE 'P%';`
- for this query, the server can employ any of the following strategies to service it
	- scan the entire table
		- very slow
	- visit the index on last_name then visit each row
	- visit the index on first_name then visit each row
	- use the index on the last_name and first_name columns
		- fastest

### The Downside of Indexes
- Every index can be viewed as another table within the database
	- the server has to keep everything up to date
		- this adds a small amount of overhead to each action on the database
			- can add up in large databases
- indexes also require disk space

### Index Best Practices
- Indexes should be used judiciously
	- every index added can make write operations (INSERT, UPDATE, DELETE) slower
		- even while reading becomes faster
- the goal is to not have too many indexes or too few
	- focus on making sure they're added to:
		- all primary key columns
		- all columns that are referenced in foreign key constraints
		- any columns that will frequently be filtered on
- **Index High Cardinality Columns**
	- columns that have a wide range of unique values are good candidate for indexing
- **Avoid Indexing Low-Cardinality Columns*
	- these are less efficient
	- the engine will make intelligent decisions and may find it faster to scan the whole table
- **Composite Indexes**
	- if you often filter by two or more columns, a composite index may make sense
		- the order of columns matters
- **Remove Unused Indexes**
	- over time, some indexes may become obsolete
		- periodically check your indexes and removes those that are not being used
- **Regularly Monitor and Analyze**
	- use tools like EXPLAIN to understand how your queries use indexes and optimize accordingly

### EXPLAIN Statement
- The EXPLAIN statmement provides information about how MySQL executes statements
- EXPLAIN works with SELECT, DELETE, INSERT, and UPDATE statements
- EXPLAIN can be used to see where indexes should be added to tables to speed up queries
- EXPLAIN can also be used to check whether the optimizer joins tables in an optimal order

![[../attachments/Pasted image 20260128074149.png]]

```
EXPLAIN SELECT * FROM city WHERE country = 'USA' AND population > 1000000;

EXPLAIN ANALYZE SELECT * FROM city WHERE country = 'USA' AND population > 1000000;
```
