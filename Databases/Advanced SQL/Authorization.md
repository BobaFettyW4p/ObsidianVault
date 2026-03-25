 - users can be assigned several forms of authorizations on behalf of the database
	 - such as:
		 - Authorization to read data
		 - Authorization to insert new data
		 - Authorization to update data
		 - Authorization to delete data
	- these authorizations are called a *privilege*
		- a user can be authorized for all, none, or a combination of these types of privileges on a specified part of a database
### Granting and Revoking of Privileges
- SQL as a standard includes the privileges *select*, *insert*, *update*, and *delete*
	- The privilege *all privileges* can be used as a short form for all allowable privileges

#### Examples
```
GRANT SELECT ON department TO John, Sara;
GRANT UPDATE (budget) ON department TO John, Sara;
```

### Privilege
- a user/role that is granted a privilege is not authorized to grant that privilege to another user/role
- some other privileges include *create*, *drop*, and *alter*

### Revoke access
- to revoke access, we use the `REVOKE` command
```
REVOKE privilege_list
ON relationName or viewName
FROM user/role_list;
```

```
REVOKE SELECT ON department FROM John, Sara;
REVOKE UPDATE (budget) ON department FROM John, Sara;
```

### See Privileges
- `SHOW GRANTS` returns all privileges and roles granted to an account user or role
```
SHOW GRANTS
[FOR {user | role}]
```
- to display the privileges granted for the current user
	- `SHOW GRANTS`

### Create/Remove a user
- `CREATE USER 'jeffrey' IDENTIFIED BY 'password';`
- `DROP USER 'jeffrey';`
