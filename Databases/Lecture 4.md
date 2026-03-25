- Inserting, Updating, and Deleting
- will be another assignment next week with grouping, updating, deleting
- project will be posted next week
	- group of 2
- last week of MySQL
### Inserting, Updating and Deleting
- insert - way to put data in
	- three main components
		- name of table in which to add
		- name of the columns to be populated
		- values with which to populate the columns
	- do not have to provide data for every column (unless specified to not be null)
- `INSERT INTO person (person_id, fname, lname, eye_color, birth_date) VALUES (null, 'William', 'Turner', 'BR', '1972-05-27';`
- you can copy rows directly using a query
- `INSERT INTO dept_east (deptno, dname, loc) SELECT deptno, dname, loc FROM dept WHERE loc IN ('NEW YORK','BOSTON');`
- Update data
	- modifying records in a table
		- you want to modify values for some or all rows in a table
	- updating when corresponding rows exist
	- `UPDATE person SET street = '1225 Tremont St.', city = 'Boston', state = 'MA', country = 'USA', postal_code = '02138' WHERE person_id = 1;`
	- `UPDATE employees INNER JOIN  merits ON employees.performance = merits.performance SET salary = salary + salary * percentage`
- Deleting data
	- `DELET FROM  person WHERE person_id = 2;`
	- `DELETE customers FROM customers LEFT JOIN orders ON customers.customerNumbers = orders.CustomerNumber WHER orderNumber IS NULL;`

### Transactions
- a transaction lets more safely update/add/delete to a database
- it consists of a sequence of operations that are executed as a single unit
- `START TRANSACTION;`
- `INSERT INTO page_views(user_id, page) VALUES (525, 'home');`
- `INSERT INTO page_views (user_id, page) VALUES (525, 'contact us');`
- `DELETE FROM new_users WHERE user_id = 525;`
- `UPDATE page_views SET page = 'request info' WHERE page = 'contact us';`
- `COMMIT;`
	- changes can be undone if needed
		- `ROLLBACK`
### SAVEPOINT
- `START TRANSACTION;`


- primary index: a unique index where all key column values must be unique
	- each table can have only one primary key
- unique index
	- ensures all values in the index must be unique
- full-text index
	- used for full-text searches
		- allows users to search for words or phrases in the documents
- `CREATE INDEX name ON relation_name (attribute-list);`
- indexes can take a long time to create
	- creating the same table again stored and indexed against the created index
- can also create a multicolumn index or a composite index
	- `ALTER TABLE customer ADD INDEX idx_full_name (last_name, first_name);`
	- the order of the columns matters! always choose the most selective column first
- `EXPLAIN` runs your query through an analyzer and forms an executin plan before it gets executed

- types of indexes
	- b tree indexes
		- good for high cardinality oclumns where there's a high variety of values

#### Variables
- 3 types
- user-defined variables (prefied with @)
	- can acccess without decalring our initializing
		- if referred to before initializations, it has  avalue of NULL
	- `SET @x = 10;`
	- `SELECT @x;`
- local variables (no prefix)
	- `DECLARE variable_name datatype(size) [DEFAULT default_value];`
	- `SET variable_name = value;
	- `SET totalSale = 10.99;`
- each stored program contains a body that consists of an SQL statement

- `CREATE PROCEDURE getCities (IN country VARCHAR(20))`
- `BEGIN`
	- `SELECT * FROM City`
	- `I`
- Stored Procedures
- `CREATE PROCEDURE dorepeat(p1 INT)`
- `BEGIN`
	- `SET @X=0;`
	- `REPEAT`
		- `SET @x`

- Triggers
	- exceute something based on an event