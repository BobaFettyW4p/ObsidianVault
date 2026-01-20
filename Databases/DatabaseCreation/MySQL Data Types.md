### Character Data
- character data can be stored as either fixed-length or variable-length strings
	- `char(20) -- fixed-length`
	- `varchar(2) -- variable-length`
- the maximum length for char columns is 255 bytes
	- varchar columns can be up to 64 KB

### Text Data
- used to store notes or documents
- not recommended for sorting or grouping
	- data truncation
![[../attachments/Pasted image 20260119182927.png]]

### Numeric Data

![[../attachments/Pasted image 20260119183021.png]]

![[../attachments/Pasted image 20260119183126.png]]

- *Precision*: the total number of allowable digits both to the left and to the right of the decimal point
- *Scale*: the number of allowable digits to the right of the decimal point
- floating-point columns can be defined as unsigned
	- means it is not possible to denote a negative value, but get an additional bit

### Temporal Data

![[../attachments/Pasted image 20260119183309.png]]

### Other
- **Boolean** type
	- True
	- False
- **Binary** data types
	- *BLOB*
	- *MEDIUMBLOB*
	- *LARGEBLOB*

