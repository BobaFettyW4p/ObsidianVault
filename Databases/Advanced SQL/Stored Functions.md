- each stored program contains a body that consists of an SQL statement
	- each statement may be a compound statement made up of several statements separated by a semicolon
- a function can be called form inside a statement just like any other function
- different from a stored procedure, you can use a stored function in SQL statements wherever an expression is used
```
CREATE FUNCTION
ALTER FUNCTION
DROP FUNCTION
```

### Functions Types
- *Deterministic functions* always result in the same output every time they are called with a fixed set of input values and given the same condition of the database
	- e.g. `AVG()`
- *Nondeterministic functions* result in different output each time they are called with a fixed set of input values
	- e.g. `GETDATE()`

```
CREATE FUNCTION hell (s CHAR(20)) RETURNS CHAR(50) deterministic
BEGIN
	RETURN CONCAT('hELLO ',s,'!')
END;
```

```
CREATE FUNCTION customerLevel(credit DECIMAL(10,2)) RETURNS VARCHAR(20) deterministic
BEGIN
	DECLARE customerLevel VARCHAR(20);
	IF credit > 50000 THEN
		SET customerLevel = 'PLATINUM';
	ELSEIF (credit >= 10000 AND credit <= 50000) THEN
		SET customerLevel = 'GOLD';
	ELSEIF credit < 10000 THEN
		SET customerLevel = 'SILVER';
	END IF;
		RETURN (customerLevel);
	END;
```