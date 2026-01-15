

- *Normalization* is the process of decomposing large tables into smaller ones in order to eliminate redundant/duplicate data, and to avoid problems with inserting, updating, or deleting data

### Anomalies
- an *anomaly* is an inconsistent, incomplete, or contradictory state of the database
	- *Insertion anomaly* - user is unable to insert a new record when it should be possible to do so
	- *Deletion anomaly* - when a record is deleted, other information that is tied to it is also deleted
	- *Update anomaly* - when a record is updated, but other appearances of the same items are not updated

##### Anomaly example

![[../attachments/Pasted image 20260110151006.png]]

- **Insertion Anomaly**: it is not possible to add a new class unless the instructor, schedule, room, and student list are known
	- e.g. it's not possible to create a class without students already enrolled in it
- **Deletion Anomaly**: if student `S1001` is deleted, the class `ART103A` would be deleted as well
- **Update Anomaly**: if the schedule of class `ART103A` is updated in the first record, but not the other records in the table for that class, data will be inconsistent 

### Normalization Levels
- the levels of normalization, from weakest to strongest:
	- First Normal Form (1NF)
	- Second Normal Form (2NF)
	- Third Normal Form (3NF)
	- Boyce-Codd Normal Form (BCNF)
	- Fourth Normal Form (4NF)
	- Fifth Normal Form (5NF)

#### First Normal Form (1NF)
- each column must have a unique name
- the order of the rows and colums doesn't matter
- each column must have a single data type
- no two rows can contain identical values
- each column must contain a single value
- columns cannot contain repeating groups
	- domains of attributes are *atomic*: no sets, arrays, lists, repeating fields, or groups allowed in domains

#### Before normalization
![[../attachments/Pasted image 20260110152040.png]]

##### After normalization
![[../attachments/Pasted image 20260110152420.png]]

#### Second Normal Form (2NF)
- it is in 1NF
	- all non-key fields depend on all of the key fields

##### Before normalization

![[../attachments/Pasted image 20260110153645.png]]

##### After normalization

![[../attachments/Pasted image 20260110153712.png]]

![[../attachments/Pasted image 20260110153727.png]]

#### Third Normal Form (3NF)
- it is in 2NF
- it contains no *transitive dependencies*
	- a *transitive dependency* is when one non-key field's value depends on another non-key field's value

##### Before Normalization
![[../attachments/Pasted image 20260110153908.png]]

##### After Normalization
![[../attachments/Pasted image 20260110153948.png]]

![[../attachments/Pasted image 20260110153931.png]]

### Stopping at Third Normal Form
- it stores separate data separately so you can add and remove pieces of information without destroying unrelated data
	- also removes redundant data so the database doesn't have additional copies of the same info that wastes time and space
		- also makes updating values more difficult
- it's possible to continue normalizing data all the way down the levels, but each one gets progressively more difficult, and provides less utility
	- this generally provides the best value
>"less time and good quality"


### Boyce-Codd Normal Form (BCNF)
- it is in 3NF
- every *determinant* is a candidate key
- a *determinant* is a field that at least partly determines the value in another field

##### Before normalization

![[../attachments/Pasted image 20260110154521.png]]

##### After normalization
![[../attachments/Pasted image 20260110154539.png]]

![[../attachments/Pasted image 20260110154551.png]]

### Fourth Normal Form (4NF)
- it is in BCNF
- it does not contain an unrelated multi-valued dependency

##### Before Normalization
![[../attachments/Pasted image 20260110154642.png]]

##### After normalization
![[../attachments/Pasted image 20260110154702.png]]

![[../attachments/Pasted image 20260110154712.png]]

### Fifth Normal Form
- it is in 4NF
- it contains no multi-valued dependencies

##### Before Normalization
![[../attachments/Pasted image 20260110154756.png]]

##### After Normalization
![[../attachments/Pasted image 20260110154824.png]]

![[../attachments/Pasted image 20260110154834.png]]

![[../attachments/Pasted image 20260110154853.png]]