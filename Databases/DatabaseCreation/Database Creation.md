### Create a database
- Create a database
	- `CREATE DATABASE [IF NOT EXISTS] database_name [CHARACTER SET charset_name] [COLLATE collation_name];`
- a character set is a set of symbols and encodings
- a collation is a set of rules for comparing characters in a character set

### Database Commands
- Display names of existing databases
	- `SHOW databases;`
- Display name of current database
	- `SELECT database();`
- Switch to another database
	- `USE another_db;`
- Create a database
	- `CREATE DATABASE my_new_db;`
- Delete a Database
	- `DROP DATABASE my_new_db;`
- Rename a database
	- generally not possible
		- have to migrate to a new database

### Schema vs. Database
- in MySQL, a schema is the same thing as a database
	- two terms are used interchangeably
- in Oracle or SQL Server, a schema consists of the database objects owned by a particular user
	- in this case, *schema* and *user* are used interchangeably

