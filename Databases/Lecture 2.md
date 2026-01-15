
Normalization - determining if htere are any anomalies in your data

- in many cases, the best way to normalize data will be to take data from one table and separate it into 2 or more tables

What is SQL?
- SQL is th elanguage fo rmanipulating the data in relational tables
- Structured Query Language
- SQL Statement Classes:
	- SQL schema statements
	- SQL data statements
	- SQL transaction statements

- Lightweight Databases
	- SQLite and Microsoft Access
- Centralized Databases
	- MySQL
	- Microsoft SQL Server
	- Oracle
	- PostgreSQL
	- Teradata
	- IBM DB2
	- MariaDB

- MySQL
	- open source, free to use, straightforward to set up

- Create a database
- CREATE DATABASE [IF NOT EXISTS] database_name
- [CHARACTER SET charset_name]
- [COLLATE collation_name]
	- a character set is a set of symbols and encodings
	- a collation is a set of rules for comparing characters in a character set
- Database commands
	- Display names of existing databases
		- SHOW databaess;
	- Display name of current database
		- SELECT database
	- switch
		- USE
	- create
		- CREATE
	- Delete
		- DROP
	- rename
		- you cannot renam ea database

Constraints
- we call foreign keys "Constraints"