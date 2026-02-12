## What is SQLite?
- SQLite is small, fast, self-contained form of a Relational Database Management System (RDBMS)
- MySQL uses a database server to run on a network
	- Conversely, SQLite is known as an embedded database
- SQLite is portable and easy to move
	- all table information is located in the file
- SQLite requires no prior installation nor setup

## SQLite vs. MySQL
- **Deployment**
	- SQLite is embedded, MySQL requires a server
- **Complexity**
	- SQLite is simpler to set up with less overhead
- **Data Types**
	- SQLite has dynamic typing whereas MySQL has static typing
- **Concurrency**
	- MySQL supports higher concurrency; SQLite is more restrictive
- **Size**
	- SQLite is lightweight, whereas MySQL is more robust

## SQL Differences
- SQLite only supports `NULL`, `INT`, `REAL`, `TEXT`, and `BLOB` as types
- SQLite uses a dynamic typing system called *manifest typing*
- the type of a value is associated with the value, not its container
```
CREATE TABLE example(id INTEGER, data TEXT);
INSERT INTO example VALUES (1, '100'), (2, 100);
```
- MySQL uses a static typing system where the data type is strictly defined by the table's schema
```
CREATE TABLE example(id INT, data VARCHAR(255));
INSERT INTO example VALUES(1, '100'), (2,'100);
```
- SQLite uses `||` for string concatenation
	- MySQL uses `CONCAT()`
- SQLite uses `SUBSTR()`
	- MySQL uses `SUBSTRING()`
- most common SQL queries can run with little or no modification in both systems
## SQLite Advantages
- no installation or administration needed
- serverless architecture means fewer resources used
- great for applications needing portability
- can handle low to moderate traffic HTTP requests with ease
- provides a full set of SQL operations and almost never needs tuning
## SQLite Disadvantages
- lacks user management and security features
- not easily scalable
- not suitable for big databases
## Where Does SQLite Work Well?
- embedded devices and Internet of Things
- Desktop applications requiring a local database
- websites or mobile apps for storing user data and preferences
- storing application data like configuration settings, cached data, etc.
- temporary databases for testing and prototyping
- replacement for ad hoc disk files
- education and training
## When Not to Use SQLite
- Clinet/Server applications
- high-volume websites
- very large datasets
- high concurrency
## Shell
- To start
	- `sqlite3`
- Create a new database
	- `sqlite3 test.db`
- Open a database file
	- `.open c:\sqlite\db\filename.db`
- Show databases in the current database connection
	- `.database`
- Show all available commands and their purposes
	- `.help`
- Show tables in a database
	- `.tables`
- Show the structure of a table
	- `.schema customers`
- Show indexes
	- `.indexes`
- To exit
	- `.exit`
