### The Relational Model
- data is represented as sets of *tables*
- tables have columns and rows to store data, much like a spreadsheet
![[../attachments/Pasted image 20260110083250.png]]

### Keys
- - each table in a relational database includes information that uniquely identifies a row in that table
- there are several different types of keys:
	- *Candidate key*
	- *Primary key*
	- *Foreign key*

##### Candidate Key
- a *candidate key* is a set of one or more attributes that allow us to unique identify a tuple (row) in the relation
- a candidate key is a *minimal superkey*

##### Primary Key
- a *primary key* is a special field or a combination of fields that provides a unique identity to each record
	- every table in your database should have a primary key
	- this is often chosen by the database designed as the main identifier

##### Foreign Key
- some tables include information used to navigate to another table
	- these relationships are known as *foreign keys*



![[../attachments/Pasted image 20260110091305.png]]

- in this example, the *Agent ID* key is the primary key of the `Agents` table
- the `Entertainers` table has a foreign key that references the `Agents` table
	- in this way, referencing the `Entertainers` table can allow access to the rows of the `Agents` table

### Relationships

- a *relationship* exists between two tables when you can in some way associate the records of the first table with those of the second
	- this can be established by:
		- a set of primary and foreign keys
		- or through a third table known as a linking table
	- helps reduce redundnat data and eliminate duplicate data
	- the relational model characterizes relationships as:
		- *one-to-one*
		- *one-to-many*
		- *many-to-many*

##### One-to-one relationships
- a single record int he first record is related to only one record in the second table
- one table serves as a "parent" table and the other serves as a "child" table
![[../attachments/Pasted image 20260110093450.png]]

##### One-to-many relationships
- a single record in the first table can be related to **many** records in the second table, but a single record in the second table can be related to **only one** record in the first table
![[../attachments/Pasted image 20260110093736.png]]

##### Many-to-many relationships
- a single record in the first table can be related to **many** records in the second table, and a single record in the second table can be related to **many** records in the first table
![[../attachments/Pasted image 20260110093919.png]]

### Data Integrity
- table-level integrity ensures that there are no duplicate records within the table
- field-level integrity ensures that each field is valid, consistent, and accurate
- relationship-level integrity ensures that the relationship between a pair of tables is synchronized whenever data is entered into, updated in, or deleted from either table

### Terminology
- *entity* - something of interest to the database user community. Examples include customers, parts, geographic locations, etc.
- *column* - an individual piece of data stored in a table
- *row* - a set of columns that together completely describe an entity or some action on an entity. Also called a record.
- *table* - a set of rows, held either in-memory (nonpersistent) or on permanent storage (persistent)
- *result set* - another name for a nonpersistent table, generally the result of an SQL query