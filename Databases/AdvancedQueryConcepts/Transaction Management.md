### Transactions
- a *transaction* allows you to more safely update/add/delete to a database
	- it consists of a sequence of operations that are executed as a single unit
- `START TRANSACTION; INSERT INTO page_views (user_id, page) VALUES (525, 'home'); INSERT INTO page_views (user_id, page) VALEUS (525, 'contact us'); DELETE FROM new_users WHERE user_id = 525; UPDATE page_views SET page = 'request info' WHERE page = 'contact us'; COMMIT;
- it's also possible to undo your changes if needed
	- `ROLLBACK`
- 