- each stored program contains a body that consists of an SQL statement
- each statement may be a compound statement made up of several statements separated by semicolons
- a procedure is invoked using a CALL statement and can only pass back values using output variables
```
CREATE PROCEDURE
ALTER PROCEDURE
DROP PROCEDURE
```

```
CREATE PROCEDURE getcities (IN country VARCHAR(20))
BEGIN
	SELECT * FROM city
	INNER JOIN country c ON city.country_id=c.country.id
	WHERE c.country = country;
END;

CALL getCities('United States');
```

```
CREATE PROCEDURE cityCount(IN country VARCHAR(20)) OUT cities INT)
BEGIN
	SELECT * INTO cities FROM city
	INNER JOIN country c ON c.country_id = c.country_id
	WHERE c.country = country;
END;

CALL citycount('United States', @cities);
SELECT @cities
```

```
CREATE PROCEDURE dorepeat(p1 INT)
BEGIN
	SET @x = 0;
	REPEAT
		SET @x = @x + 1;
	UNTIL @x > p1 END REPEAT;
END;
```

