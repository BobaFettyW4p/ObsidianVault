### Union Operators
- use the UNION keywork to combine the results of two or more SELECT statements
	- Number of Columns MUST MATCH
	- Column Names DO NOT HAVE TO MATCH
	- Data Types MUST MATCH
- The difference between a JOIN and a UNION is that JOIN links together multiple tables within a single query, whereas UNION stacks the results of multiple queries
![[../attachments/Pasted image 20260121212225.png]]

- UNION
	- combines the results of multiple statements
	- eliminate any duplicate rows
- UNION ALL
	- combine the two tables and preserve duplicate rows
- EXCEPT
	- returns the results minus another set of results
	- not supported in MySQL
- INTERSECT
	- returns overlapping results
	- not supporting in MySQL.

![[../attachments/Pasted image 20260121212810.png]]

### Joins vs Union
- Joining Tables
	- combine the columns of two tables based on matching rows
- Union Operators
	- combine the rows of two tables based on matching columns

### EXISTS
- Use EXISTS to test if a subquery returns results or not
	- `SELECT e.id, e.name FROM employees e WHERE EXISTS (SELECT * FROM customers c WHERE c.email = e.email`

### EXISTS vs. JOIN
- `SELECT * FROM employees e INNER JOIN customers c ON e.email = c.email;`
- use JOIN when you want values from both tables to be returned
- use EXISTS when you want values from a signle table to be returned
- comes with NOT