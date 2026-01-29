- it is a mechanism for querying data
- it is a result of the query
- it has a anme
- the output of a query is a single table
- the difference between a view and a table is that a view does not hold any data itself like a table, it just references the data instead

### Why are views useful?
- Avoiding duplicate effort
	- you can write a complex query exactly once and access the results when needed
- Reducing clutter
	- views can be used to trim the amount of information you need to wade through by showing only columns relevant to your needs
- Providing security
	- views can limit access to only certain columns in a table

### Subquery vs View
- a *subquery* is temporary
	- it exists only for the duration of the query
	- great for one time use
- a *view* is more permanent
	- once a view is created, you can continue to write queries that reference the view

### Create a View to Save the Results of a Query
```
CREATE VIEW my_view
AS
SELECT *
FROM my_table
WHERE country = 'US';
```

### Views
- Display existing views
	- `SHOW FULL TABLES WHERE table_type = 'VIEW';`
- Update a view
	-  `CREATE OR REPLACE VIEW my_view AS SELECT * FROM my_table WHERE country = 'CA';`
- Delete a view
	- `DROP VIEW my_view;`
		- this does not result in any data loss 
			- the data remains in the original table, and the view can always be recreated

