## What is TypeORM?
- TypeORM is an open-source ORM that supports TypeScript and JavasScript
- works with multiple databases
	- MySQL
	- PostgreSQL
	- SQLite
	- SQL Server
	- Oracle
- enables developers to work with databases using entities and repositories instead of raw SQL

## TypeORM Architecture
- *DataSource*
	- handles database connection
- *Entity*
	- class mapped to a database table
- *Repository*
	- interface for CRUD operations
- *QueryBuilder*
	- fine-runed control over generated SQL
- *Migration*
	- tracks schema changes
## Setting Up TypeORM
![[attachments/Pasted image 20260211211856.png]]

### Creating an Entity
![[attachments/Pasted image 20260211211915.png]]
### Adding Table Columns
![[attachments/Pasted image 20260211211940.png]]

### Creating a Primary Column

![[attachments/Pasted image 20260211212111.png]]

### Auto-Generated Column

![[attachments/Pasted image 20260211212130.png]]

### Column Data Types
![[attachments/Pasted image 20260211212150.png]]
### Using Entity Manager
![[attachments/Pasted image 20260211212211.png]]

### Inserting a Photo Into the Database
![[attachments/Pasted image 20260211212232.png]]

### Using Repositories
![[attachments/Pasted image 20260211212505.png]]

### Updating in the Database
![[attachments/Pasted image 20260211212522.png]]

### Removing From the Database
![[attachments/Pasted image 20260211212538.png]]

### Creating a one-to-one relation
![[attachments/Pasted image 20260211212608.png]]

![[attachments/Pasted image 20260211212625.png]]

![[attachments/Pasted image 20260211212639.png]]

### Loading objects with their relations
![[attachments/Pasted image 20260211212658.png]]

### Creating a Many-to-One relation
![[attachments/Pasted image 20260211212726.png]]

### Creating a Many-to-One/One-to-Many Relation

![[attachments/Pasted image 20260211212803.png]]


![[attachments/Pasted image 20260211212816.png]]

### Creating a Many-to-Many Relation
![[attachments/Pasted image 20260211213248.png]]

![[attachments/Pasted image 20260211213307.png]]

- the ORM will create an `album_photos_photo_albums` junction table:
![[attachments/Pasted image 20260211213335.png]]

![[attachments/Pasted image 20260211213350.png]]

![[attachments/Pasted image 20260211213403.png]]

