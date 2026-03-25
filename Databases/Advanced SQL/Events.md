- MySQL event are tasks that execute according to a specified schedule
	- e.g.
```
CREATE EVENT [IF NOT EXIST] event_name
ON SCHEDULE schedule
DO
	event_body
```
- if the event is a one-time event, use this syntax:
	- `AT timestamp [+ INTERVAL]`
- if the event is a recurring event, use the `EVERY` clause
	- `EVERY interval STARTS timestamp [+ INTERVAL] ENDS timestamp [+ INTERVAL]`

#### Examples
```
CREATE EVENT add_store
ON SCHEDULE AT CURRENT_TIMESTAMP + INTERVAL 1 SECOND
DO
	INSERT INTO countrY(country)
	VALUES('New Country');
```

```
CREATE EVENT add_country
ON SCHEDULE EVERY 1 SECOND
STARTS CURRENT_TIMESTAMP
ENDS CURRENT_TIMESTAMP + INTERVAL 1 HOUR
DO
	INSERT INTO country(country)
	VALUES('New Country');
```