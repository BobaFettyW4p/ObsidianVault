- a *common table expression (CTE)* is a temporary result set
	- it temporarily saves the output of a query for you to write other queries that reference it
	- a CTE can be spotted when you see the `WITH` keyword
		- there are two types of CTEs
			- *Nonrecursive CTE*
			- *Recursive CTE*
- `WITH my_cte AS (SELECT name, AVG(grade) AS avg_grade FROM my_table GROUP BY name) SELECT * FROM my_cte WHERE avg_grade < 70;`

### Advantages of a CTE versus a subquery
- multiple references
	- once a CTE is defined, you can reference it by name multiple times within the `SELECT` queries
- multiple tables
	- CTE syntax is more readable when working with multiple tables because you can list all the CTEs up front