- SQLite
- ORM
- 

### SQLite
- small
- fast
- self-contained
- a form of a Relational Database Management System
- MySQL uses a database server to run on a netork
	- SQLite uses an embedded database
- SQLite is portable and easy to move
	- all table information is stored in the file
- SQLite requires no prior installation nor setup
- SQLite acts as the query engine and the storage engine simultaneously

### SQLite vs. MySQL
- SQLite is embedded, MyS

### SQLite differences
- SQLite supports only NULL, integer, real, rext, bloc
- SQLite uses a dynamic typing system called "manifest typing", where the type of a value is associated with the value itself, not wirh its container
	- `CREATE TABLE example (id INTEGER, data TEXT);`
	- `INSERT INTO example VALUES(1, '100')`

### Where SQLit works well
- embedded devices and the internet of things
- desktop applications requiring a local database
- websites or mobile apps for storing user data and preferences
- storing app data like configuration settings, cached data, etc.
- temporary databases for testing and prototyping
- replacement for ad hoc disk files


- midterm
- open book
- everything up through SQL
- relational database design
- SQL creation
- SQL queries
	- SELECT, aggregations, insert, delete, indices,
- programmin in SQL
	- variables
	- stored procedure/functions
	- triggers
	- events
- Advanced SQL
	- index
	- transactions
	- views
	- windows
	- permissions
- Multiple choices
- short answers
- drawing
	- will create an ERD
- with SQL, you can know all the syntax, 


### Object Relational Mapping (ORM)

### What problems do ORMs solve?
- relational DBs store tables, rows, and foreign keys
- applications manipulate objects and classes
- ORMs bridge this "impedance mismatch"

### ORM in Action
```
//SQL
result = db.query("SELECT * FROM user WHERE id=$1, [id]);

//ORM
user = userRepository.findOneBy({id});
```

- Java: Hibernate
- JavaScript: TypeORM
- Python: Django

### What is TypeORM?
- Open-source ORM that supports TypeScript and JavaScript
- works with multiple databases: MySQL, PostgreSQL, SQLite, SQL Server, Oracle, and more
- enables developers to work with databases using entities and repositories instead of raw SQL

### TypeORM Architecture
- DataSource
	- handles database connection
- Entity
	- class mapped to a database table
- Repository
	- interface for CRUD operations
- QueryBuilder
	- fine tuned control over generated SQL
- Migration
	- tracks schema changes

### Active Record vs Data Mapper Pattern
- Active Record: Entities contain methods to persist themselves
- Data Mapper: Separate repository handles persistence