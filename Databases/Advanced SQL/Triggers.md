- A trigger is a stored program invoked automatically in response to an event such as insert, update, or delete that occurs in the associated table
- To create a trigger or drop a trigger, us the `CREATE TRIGGER` or `DROP TRIGGER` statement
![../attachments/[Pasted image 20260128200916.png]]


### Advantages of triggers
- triggers provide another way to check the integrity of data
	- they give an alternative way to run scheduled tasks
		- automatic data generation
- triggers can be helpful for auditing data changes in tables
- triggers can be helpful in cascade operations

### Disadvantages of triggers
- triggers can be difficult to troubleshoot
	- they execute automatically
		- may not be visible to client applicaitons
- triggers may increase the overhead of the server

### Triggers types
- *row-level trigger*
	- is activated for each row that is inserted, updated, or deleted
- *statement-level trigger*
	- executed once for each transaction regardless of how many rows are inserted, updated, or deleted
- MySQL only supports row-level triggers

#### Template
```
CREATE TRIGGER trigger_name
{BEFORE|AFTER} {INSERT|UPDATE|DELETE}
ON table_name FOR EACH ROW
trigger_body;
```

#### Examples
```
CREATE TRIGGER before_employee_update
	BEFORE UPDATE ON employees
	FOR EACH ROW
INSERT INTO employees_audit (action, employeeNumber, lastname, date) ('update', OLD.employeeNumber, OLD.lastname, NOW());
```

```
CREATE TRIGGER after_country_insert
	AFTER INSERT ON country
	FOR EACH ROW
INSERT INTO city (city, country_id)
	VALUES ('New City', NEW.country_id);
```

