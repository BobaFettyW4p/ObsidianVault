### Guidelines for creating table names
- create a unique, descriptive name that is meaningful to the entire organization
- create a name that accurately, clearly, and unambiguously identifies the subject of the table
	- the most vague and ambiguous name you can assign to a table is "Miscellaneous"
- use the minimum number of words necessary to convey the subject of the table
- do not use works that convey physical characteristics
	- avoid words such as file, record, and table
- do not use acronyms and abbreviations
	- e.g. instead of `SC`, name your table `System Configurations`
- do not use proper names or other words that will restrict the data that can be entered into the table
	- e.g. `Southwest Region Employees` would not be a suitable table to enter employee info that work in other regions
- do not use a name that implicitly or explicitly defines more than one subject
	- e.g. `Department or Branch` or `Facility/Building`
- do not use the singular form of the name

### Guidelines for creating field names
- create a unique descriptive name that is meaningful to the entire organization
- create a name that accurately, clearly, and unambiguously identifies the chracteristic a field represents
	- `Phone Number`
- use the minimum number of words necessary to convey the meaning of the characteristic the field represents
- do not use acronyms, and use abbreviations judiciously
- do not use words that could confuse the meaning of the field name
- do not use names that implicitly or explicitly identify more than one characteristic
	- e.g `Area or Location` or `Phone/Fax`
- use the singular form of the name

### Elements of the idea table
- it represents a single subject, which can be an object or event
- it has a primary key
- it does not contain unnecessary duplicate fields
- it contains only an absolute minimum amount of redundant data
- there are no duplicate records in a table
- the primary key exclusively identifies each record in a table

### Elements of the ideal field
- it represents a distinct characteristic of the subject of the table
- it contains only a single value
- it cannot be deconstructed into smaller components
- it does not contain a calculated or concatenated value

### Elements of a candidate key
- it cannot be a multipart field
- it must contain unique values
- it cannot contain null values
- its value cannot cause a breach of the organization's security or privacy rules
- it comprises a minimum number of fields necessary to define uniqueness
- its value can be modified only in rare or extreme cases

### Benefits of good design
- the database structure is easy to modify and maintain
- the data is easy to modify
- information is easy to retreive
- end-user applications are easy to develop and build
