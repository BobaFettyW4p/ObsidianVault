## What Problem Do ORMs Solve?
- Relational DBs stores tables, rows, and foreign keys
- Applications manipulate objects and classes
- ORMs bridge this "impedance mismatch"
![[attachments/Pasted image 20260211210727.png]]

## What is ORM?
- stands for *Object-Relational Mapping*
	- a programming technique for converting data between relational databases and object-oriented programming languages such as Java, C#, etc.

### Example
```
// SQL
result = db.query("SELECT * FROM user WHERE id = $1", [id]);

// ORM
user = userRepository.findOneBy({id});
```

### ORM Frameworks
- Java: Hibernate
- Javascript: TypeORM
- Python: Django


