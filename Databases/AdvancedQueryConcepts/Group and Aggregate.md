### The GROUP BY Clause
- the purpose of the GROUP BY Clause is to collect rows into groups and summarize the rows within the groups in some way, ultimately returning just one row per group
- `SELECT t.name AS tour_name, COUNT(*) AS num_waterfalls FROM waterfall w INNER JOIN tour t ON w.id=t.stop GROUP BY t.name;`
- Collapsing many detail rows into one aggregate row means that when using a GROUP BY clause, the SELECT clause should only contain:
	- all columns listed in the GROUP BY clause: t.name
	- Aggregate functions: such as `COUNT(*)`

### Summarizing Functions
- summarize multiple rows of data into a single value
	- COUNT
	- SUM
	- MIN
	- MAX
	- AVG
- Combine multiple rows of data into a single list
	- `GROUP_CONCAT`

### Multicolumn Grouping
- in some cases, you may want to generate groups that span more than one column
![[../attachments/Pasted image 20260124075903.png]]

### The HAVING Clause
- the HAVING clause places restrictions on the rows returned from a GROUP BY query
	- without a GROUP BY clause, there can be no HAVING clause
	- `SELECT t.name AS tour_name, COUNT(*) AS num_waterfalls FROM waterfall w INNER JOIN tour t ON w.id=t.stop GROUP BY t.name HAVING COUNT(*)=6;`

### WHERE vs. HAVING
- Filter on particular columns, write your conditions within the WHERE clause
- Filter on aggregations, write your conditions within the HAVING clause
- the contents of a WHERE and HAVING clause cannot be swapped
- the HAVING clause refers to the aggregation

### Order of Execution
- FROM
- WHERE
- GROUP BY
- HAVING
- SELECt
- ORDER BY

### Generating Rollups

![[../attachments/Pasted image 20260124080236.png]]
