### Cartesian Product of Sets

![[../attachments/Pasted image 20260121205653.png]]

### Joining Tables
- a *Join* is a combination of a Cartesian product followed by a selection process
	- a Join Operatioon pairs two tuples from different relations $IFF$ a given join condition is satisfied
- in SQL, *joining* means combining data from multiple tables together with a single query
- What is needed?
	- Tables
	- Aliases
	- Join Type
	- Join Condition
![[../attachments/Pasted image 20260121210059.png]]

![[../attachments/Pasted image 20260121210459.png]]

### Joining Tables
- `SELECT * FROM  states s [JOIN_TYPE] pets p ON [JOIN_CONDITION];`

![[../attachments/Pasted image 20260121211027.png]]

### Ways to Join Tables Together
- JOIN
	- defaults to an INNER JOIN
- INNER JOIN
	- returns the rows in common
- LEFT JOIN
	- returns the rows in the left table and the matching rows in the other table
- RIGHT JOIN
	- returns the rows in the right table and the matching rows in the other table
- FULL OUTER JOIN
	- returns the rows in both tables
- CROSS JOIN
	- returns all combinations of rows in the two tables

#### Tips
- a LEFT JOIN is equivalent to a LEFT OUTER JOIN
	- similarly, a RIGHT JOIN is equivalent to a RIGHT OUTER JOIN
- a LEFT JOIN is much more common than a RIGHT JOIN
	- if a RIGHT JOIN is needed, it's more common to flip the tables in the FROM clause a do a LEFT JOIN instead
- a Join condition can be on multiple columns
- use NATURAL JOIN instead of INNER JOIN if the names of all  the columns that you are joining match


### The SELECT Clause
- Qualifying Columns
	- `SELECT owner.name, car.name FROM owner INNER JOIN Car ON car.ownerId=owner.id;`
- Selecting Subqueries
	- `SELECT id, name, population, (SELECT AVG(population) FROM  county) AS average_pop FROM county;`