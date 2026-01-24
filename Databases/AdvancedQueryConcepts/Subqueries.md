### Subqueries
- a *subquery* is a query that is nested within another query
- subqueries can be located within various clauses
	- `SELECT`
	- `FROM`
	- `WHERE`
- a subquery must be surrounded by parenthesis
- noncorrelated versus correlated subqueries
	- *noncorrelated* means the subquery does not refer to the outer query
	- *correlated* is a subquery that does refer to the values in the outer query
		- these are very slow, `JOIN` s are generally recommended
- `SELECT o.id, o.name, (SELECT COUNT(*) FROM waterfall w WHERE o.id = w.owner_id) AS num_waterfalls FROM owner o;`
- `SELECT o.id, o.name, COUNT(w.id) AS num_Waterfalls FROM owner o LEFT JOIN waterfalls w ON o.id = w.owner_id GROUP BY o.id, o.name`
- `SELECT w.name AS waterfall_name, o.name AS owner_name FROM (SELECT * FROM owner WHERE type = 'public') o JOIN watefall w ON o.id = w.owner_id;`
- `SELECT w.name FROM waterfall w WHERE w.open_to_public = 'y' AND w.county_id IN (SELECT c.id FROM coutny c WHERE c.name = 'Alger');`
- unlike subqueries within the SELECT clause or the FROM clause, subqueries in the WHERE clause do not require an alias

### WORKING > OPTIMIZING
- when writing SQL code, there are often multiple ways to do the same thing
- don't stress about writing the most optimized code up front up
	- just write code that works
		- more elegant code will come with experience
