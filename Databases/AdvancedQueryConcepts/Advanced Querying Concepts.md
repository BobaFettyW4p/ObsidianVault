### CASE Statements
- a CASE statement is used to apply if-else logic withina query
- `SELECT name, flag CASE flag WHEN 1 THEN 'vip' WHEN 0 THEN 'reserved seating' ELSE 'general admission' END AS ticket FROM concert;`