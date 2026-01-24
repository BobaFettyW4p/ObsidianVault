## Querying Basics
- SELECT
- FROM
- WHERE
- GROUP BY
- HAVING
- ORDER BY
- LIMIT

### The SELECT Clause
- The SELECT clause specifies the columns that you want a statement to return
	- Selecting Columns
		- `SELECT id, name FROM owner;`
	- Selecting All Columns
		- `SELECT * FROM owner;`
	- Aliasing Columns
		-  `SELECT id AS county_id, name FROM county;`
	- Aliases with case sensitivity and punctuation
		- `SELECT id AS "Waterfall #", name AS "Waterfall Name" FROM waterfall;`

### The FROM Clause
- The FROM clause is used to specify the source of the data you want to retreive
	- `SELECT name FROM waterfall;`
	- `SELECT name FROM waterfall w;`
###  The WHERE Clause
- The WHERE clause is used to restrict query results to only rows of interest
	- `SELECT * FROM  my_table WHERE year_id=2021;`
	- `SELECT id, name FROM waterfall WHERE name NOT LIKE '%Falls%';`
	- `SELECT id, name FROM waterfall WHERE name NOT LIKE '%Falls%' AND owner_id IS NULL;`
### Common Operators
#### Logical Operators
- `AND`
- `OR`
- `NOT`
#### Comparison Operators (Symbols)
- `=`
- `!=`, `<>`
- `<`
- `<=`
- `>`
#### Comparison Operators (Keywords)
- `BETWEEN`
- `EXISTS`
- `IN`
- `IS NULL`
- `LIKE`
#### Math Operators
- `+`
- `-`
- `*`
- `/`
- `%`

### The LIMIT Clause
- When quickly viewing a table, it is best practice to return a limited number of rows instead of the entire table
	- `SELECT * FROM owner LIMIT 3;`
	- `SELECT * FROM owner LIMIT 3 OFFSET 10;`

### IN
- Use IN to test whether a value falls within a list of values
	- `SELECT * FROM employees e WHERE e.id IN (10001, 10032, 10057);`
	- `SELECT e.id FROM employees e WHERE e.id NOT IN (SELECT v.emp_id FROM vacations v);`

### The ORDER BY Clause
- The ORDER BY clause is used to specify how you want the results of a query to be sorted
	- `SELECT id, name FROM waterfall WHERE name NOT LIKE '%Falls%' ORDER BY year;`
- The default sort is in ascending order
- use `ASC` and `DESC` to control the sort in each order