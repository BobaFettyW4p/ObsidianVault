### Create a table with Constraints
- `CREATE TABLLE my_table (`
	- `id INTEGER NOT NULL,`
	- `name VARCHAR(15),`
	- `country VARCHAR(2) DEFAULT 'IL',`
	- `cap_name VARCHAR(15),`
	- `CONSTRAINT pk PRIMARY KEY (id)`

### Specify a Primary key
- Primary key = Not NULL values and values must be unique
- `CREATE TABLE my_table (`
	- `id INTEGER PRIMARY KEY,`
	- `country VARCHAR(2),`
	- `name VARCHAR(15)`
- `);`

- `CREATE TABLE my_table (`
	- `id INTEGER,`
	- `country VARCHAR(2),`
	- `name VARCHAR(15),`
	- `PRIMARY KEY (id)`
- `);`
- `CREATE TABLE my_table (`
	- `id INTEGER,`
	- `country VARCHAR(2),`
	- `name VARCHAR(15),`
	- `CONSTRAINT pk_id name PRIMARY KEY (id,name)`
- `);`
### Specify a Foreign Key

- `CREATE TABLE customers (`
	- `id INTEGER PRIMARY KEY,`
	- `order_id INTEGER,`
	- `name VARCHAR(15),`
	- `location VARCHAR(20),`
	- `CONSTRAINT fk_id_name FOREIGN KEY (order_id) REFERENCES orders (o_id)`
- `);`

### Orphaned Rows
- an orphaned record is a record whose foreign key value references a non-existent primary key value
![[../attachments/Pasted image 20260119195425.png]]

- there are six different options to choose from when defining foreign key constraints:
	- on delete restrict
	- on delete cascade
	- on delete set null
	- on update restrict
	- on update cascade
	- on update set null
- `CONSTRAINT fk_customer_address FOREIGN KEY (address_id) REFERENCES address (address_id) ON DELETE RESTRICT ON UPDATE CASCADE;`
- `CONSTRAINT fk_customer_store FOREIGN KEY (store_id) REFERENCES store (store_id) ON DELETE CASCADE ON UPDATE CASCADE;`

### Requiring Unique Values In A Column With UNIQUE
- `CREATE TABLE my_table (`
	- `id INTEGER UNIQUE,`
	- `country VARCHAR(2),`
	- `name VARCHAR(15)`
- `);`
- `CREATE TABLE my_table (`
	- `id INTEGER,`
	- `country VARCHAR(2),`
	- `name VARCHAR(15),`
	- `UNIQUE (id)`
- `);`
- `CREATE TABLE my_table (`
	- `id INTEGER,`
	- `country VARCHAR(2),`
	- `name VARCHAR(15),`
	- `CONSTRIANT unq_country_name UNIQUE (country, name)`
- `);`

### Table with an automatically generated field
- `CREATE TABLE my_table (`
	- `u_id INTEGER PRIMARY KEY AUTO-INCREMENT,`
	- `country VARCHAR(2), name VARCHAR(15)`
- `);`


