- there are 3 types of variables
	- *User-defined variables*
		- prefixed with `@`
		- any user-defined variable can be accessed without initializing or declaring it
			- referring to a variable that has not been initialized with have a value of NULL and a type of string
		- `SET @x = 10;`
		- `SELECT @x;`
	- *Local variables*
		- no prefix
		- they must be declared with DECLARE before being accessed
		- can be used as local variables and the input parameters inside a stored procedure
		- `DECLARE variable_name datatype(size) [DEFAULT default_value]`
			- `DECLARE totalSale DECIMAL(10,2) DEFAULT 0.0;`
		- `SET variable_name = value;`
			- `SET totalSale = 10.99;`
	- *Server System variables*
		- prefixed with `@@`
		- affect the overall operation of the server
			- in comparison, session variables affect operation only for individudal client connections

### Select Into a Variable
```
SELECT c1, c2, c3
INTO var1, var2, var3
FROM table_name
WHERE condition;
```

### Disable Foreign Key Check
- you can disable foreign key check in MySQL by setting the system variable `foreign_key_checks` to 0
	- `SET foreign_key_checks = 0`
- you can also enable foreign key check
	- `SET foreign_key_checks = 1`