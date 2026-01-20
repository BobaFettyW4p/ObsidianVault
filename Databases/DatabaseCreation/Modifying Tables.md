### Tables
- Rename table
	- `ALTER TABLE old_table_name RENAME TO new_table_name;`
- Delete table
	- `DROP TABLE my_table;`
- Delete table
	- `DROP TABLE IF EXISTS my_table;`

### Columns
- Rename a Column
	- `ALTER TABLE my_table RENAME COLUMN old_column_name TO new_column name`
- Add a column
	- `ALTER TABLE my_table ADD new_num_column INTEGER`
- Delete a column
	- `ALTER TABLE my_table DROP COLUMN deleted_column`
- Modify a column
	- `ALTER TABLE my_table MODIFY country VARCHAR(2) NULL`

### Constraints
- Add a constraint
	- `ALTER TABLE table_name ADD CONSTRAINT constraint_name UNIQUE (column1);`
	- `ALTER TABLE Persons ADD CONSTRAINT PK_Person PRIMARY KEY (ID);`
	- `ALTER TABLE Orders ADD FOREIGN KEY (PersonID) REFERENCES Persons(PersonID);`
- Delete a constraint
	- `ALTER TABLE my_table DROP INDEX constraint_name;`
	- `ALTER TABLE Persons DROP PRIMARY KEY;`
	- `ALTER TABLE Orders DROP FOREIGN KEY FK_PersonOrder;`