### Common Operators
#### Aggregate Functions
- `COUNT()`
- `SUM()`
- `AVG()`
- `MIN()`
- `MAX()`

#### Numeric Functions
- `ABS()`
- `SQRT()`
- `LOG()`
- `ROUND()`
- `CAST()`
#### String Functions
- `LENGTH()`
- `TRIM()`
- `CONCAT()`
- `SUBSTR()`
- `REGEXP()`

#### Datetime Functions
- `CURRENT_DATE`
- `CURRENT_TIME`
- `DATEDIFF()`
- `EXTRACT()`
- `CONVERT()`

#### Null Functions
- `COALESCE()`

### The SELECT Clause
- The SELECT clause specifies the columns that you want a statement to return
- Selecting Expressions
	- `SELECT name, ROUND(population * 0.9, 0) FROM county;`
- Selecting Functions
	- `SELECT CURRENT_DATE`
- DISTINCT
	- `SELECT DISTINCT type FROM owner;`
- COUNT and DISTINCT
	- `SELECT COUNT(DISTINCT type) AS unique FROM owner;`
### CAST
- The CAST function is used to convert between various data types, and is often used for numeric data
- Using CAST does not permanently change the data type of the column
- `SELECT * FROM my_table WHERE CAST(str_col AS DECIMAL)>3;`
### Date
- Return the current date or time
	- `SELECT CURRENT_DATE;`
	- `SELECT CURRENT_TIME;`
	- `SELECT CURRENT_TIMESTAMP;`
- Add or subtract a date or time interval
	- `SELECT CURRENT_DATE - INTERVAL 1 DAY;`
	- `SELECT SUBDATE(CURRENT_DATE, 1);`
	- `SELECT DATE_SUB(CURRENT_DATE, INTERVAL 1 DAY;`
	- `SELECT CURRENT_TIMESTAMP + INTERVAL 3 HOUR;`
	- `SELECT ADDDATE(CURRENT_TIMESTAMP, INTERVAL 3 HOUR);`
	- `SELECT DATE_ADD(CURRENT_TIMESTAMP, INTERVAL 3 HOUR);`

### Difference between two dates or times
- `SELECT DATEDIFF(end_date, start_date) AS day_diff FROM my_table;`
	- return number of days
- `SELECT TIMEDIFF(end_time, start_time) AS time_diff FROM my_table;`
	- return number of hours
- `SELECT TIMESTAMPDIFF(hour, start_dt, end_dt) AS hour_diff FROM my_table;`
	- return number of hours
### Extract a part of a date or time
- there are multiple ways to extract a time unit (month, hour, etc.) from a date or time value
	- `SELECT EXTRACT(month FROM CURRENT_DATE);`
	- `SELECT MONTH(CURRENT_DATE);`
		- MySQL supports YEAR(), QUARTER(), MONTH(), WEEK(), DAY(), HOURS(), MINUTE(), and SECOND()
### Convert a String to a Datetime Data Type
- There are two ways to convert a string to a datetime data type:
	- use the `CAST` function for a simple case
	- Use `STR_TO_DATE/TO_DATE/CONVERT` for a custom case
		- `SELECT CAST('2020-10-15' AS DATE);`
		- `SELECT CAST('14:30' AS TIME);`
		- `SELECT CAST('2020-10-15 14:30' AS DATETIME);`
### Null Functions
- Return an alternative value if there is a null value
- `SELECT COALESCE(greeting, 'hi') AS greeting FROMmy_table; IFNULL(greeting, 'hi');`

### Aggregate Functions
- `SELECT MAX(buyPrice) highest_price FROM products;`
- `SELECT productcode, SUM(priceEach*quantityOrdered) total FROM orderDetauls;`
- `SELECT AvG(buyPrice) FROM products;`

### GROUP_CONCAT
- `SELECT GROUP_CONCAT(type) FROM products;`
- `SELECT GROUP_CONCAT(DISTINCT type) FROM products;`

### String Functions
- `SELECT CONCAT("Database ", "Class ", "is ", "fun!") AS ConcatenatedString;`
- `SELECT CHAR_LENGTH("Database Class") AS LengthOfString;`
- `SELECT SUBSTR("Database Class", 5, 3) AS ExtractString;`
- `SELECT UPPER("Database Class is FUN!");`
