### Populating and Modifying Tables
- CRUD actions
	- Create
	- Read
	- Update
	- Delete

### Inserting Data
- there are three main components to an insert statement
	- the name of the table into which to add data
	- the names of the columns in the table to be populated
	- the values with which to populate the columns
- you do not have to provide data for every column in the table (unless each column has been specified as not null)
- `INSERT INTO person (person_id, fname, lname, eye_color, birth_date) VALUES (null, 'William', 'Turner', 'BR', '1972-05-27');`
- rows can be copied from one table to another by using a query
	- `INSERT INTO dept_east (deptno, dname, loc) SELECT deptno, dname, loc FROM dept WHERE loc in ('NEW YORK','BOSTON')`

### Updating Data
- modifying records in a rable
	- you want to modify values for some or all rows in a table
- update when corresponding rows exist
	- you want to update rows in one table when corresponding rows exist in another
- updating with values from another table
	- you want to update rows in one table using values from another
- `UPDATE person SET street = '1225 Tremont St', city = 'Boston', state = 'MA', country = 'USA', postal_code = '02138' WHERE person_id = 1;`
- `UPDATE employees INNER JOIN merits ON employees.performance = merits.performance SET salary = salary + salary * percentage;`

### Deleting Data
- `DELETE FROM person WHERE person_id = 2;`
- `DELETE customers FROM customers LEFT JOIN orders ON customers.customerNumber = orders.customerNumber WHERE orderNumber IS NULL;`