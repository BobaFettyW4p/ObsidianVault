- *Active Record*
	- entities contain methods to persist themselves
- *Data Mapper*
	- separate repository handles persistence

- Aspects:
	- **Responsibility**
		- Active Record: Model contains both business logic and CRUD methods
		- Data Mapper: Business logic and persistence are separated
	- **Complexity**
		- Active Record: Simple and easy to use
		- Data Mapper: More complex with more layers of abstraction
	- **Coupling**
		- Active Record: Models are tightly coupled to the database schema
		- Data Mapper: Models are decoupled from database interactions
	- **Ease of Use
		- Active Record: Easier for small-to-medium applications
		- Data Mapper: Better for large, complex applications
	- **Testability**
		- Active Record: Harder to test due to tight coupling with database
		- Data Mapper: Easier to test as database operations are abstracted away
	- **Performance**
		- Active Record: Lightweight for simple CRUD operations
		- Data Mapper: More overhead due to separate of concerns
	- **Best For**
		- Active Record: Small-to-medium applications with simple database needs
		- Data Mapper: Large and complex systems where separation of business logic and data persistence is needed

### Active Record

![[attachments/Pasted image 20260211214814.png]]

![[attachments/Pasted image 20260211214827.png]]

![[attachments/Pasted image 20260211214843.png]]

### Data Mapper

![[attachments/Pasted image 20260211214859.png]]

![[attachments/Pasted image 20260211214914.png]]

![[attachments/Pasted image 20260211214925.png]]

### Migration

![[attachments/Pasted image 20260211214945.png]]

![[attachments/Pasted image 20260211214958.png]]

### Transactions with Custom Repositories

![[attachments/Pasted image 20260211215021.png]]