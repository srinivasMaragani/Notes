DATABASE

Database Languages
   Data Definition Language
   Data Manipulation Language
   Data Control Language
   Transactional Control Language







DCL

The full form of DCL is Data Control Language in Structured Query Language (SQL). DCL commands are used to control privileges in the database. The privileges (right to access the data) are required for performing all the database operations,
like creating tables, views, or sequences. DCL statements are used to perform the work related to the rights, permissions, and other control of the database system.

There are two types of privileges in the database:

-> System Privilege
-> Object Privilege

* System Privilege

Built-in Server-Level Roles
Role Name	Description
sysadmin	Full administrative privileges on the SQL Server instance. This role allows complete control over the SQL Server instance and all databases.
serveradmin	Can manage server-wide configuration and shutdown the server.
setupadmin	Can manage linked servers and SQL Server startup procedures.
processadmin	Can view and kill processes running on the server.
diskadmin	Can manage disk files on the server, such as attaching and detaching databases.
dbcreator	Can create, alter, and drop databases.
securityadmin	Manages server security, including creating logins and roles, but not the data inside databases.
bulkadmin	Allows users to execute BULK INSERT operations.
public	        A default role that provides a basic level of access, such as being able to connect to the instance. Every login is a member of this role.

*Object Privilege 

Role Name	    Description
db_owner	    Full control over the database. Members can perform any task in the database, including managing roles and users.
db_accessadmin	    Can manage database access. Members can add or remove users.
db_securityadmin    Can manage roles and permissions within the database but cannot perform CRUD operations.
db_ddladmin	    Can execute Data Definition Language (DDL) commands (e.g., CREATE, ALTER, DROP) in the database.
db_backupoperator   Can back up the database.
db_datareader	    Can read all data in the database.
db_datawriter	    Can modify data in the database.
db_denydatareader   Denies read access to all data in the database.
db_denydatawriter   Denies write access to all data in the database.
db_executor	    Can execute stored procedures and functions in the database.
public	            This is a default role that includes all users of the database. Members of this role have basic access rights.

Working flow 

-> There is Logins in server , we should create for user 
USE master
'CREATE LOGIN chaitanya WITH PASSWORD = 'cocoon@777';
->Then create ServerRole
USE master
'CREATE USER chaitanya FOR LOGIN chaitanya;'(for azure sql, there are no roles)
->Then assign db role to user
USE [db_name]
'ALTER ROLE db_ddladmin ADD MEMBER chaitanya;'

 


TCL

Transaction Control Language (TCL) is a critical component of SQL used to manage transactions and ensure data integrity in relational databases.
By using TCL commands, we can control how changes to the database are committed or reverted, maintaining consistency across multiple operations.

TCL Commands
TCL includes the following commands:

1. COMMIT
The COMMIT command is used to save all the transactions to the database that have been performed during the current transaction.
Once a transaction is committed, it becomes permanent and cannot be undone.
This command is typically used at the end of a series of SQL statements to ensure that all changes made during the transaction are saved. 
Syntax:  

COMMIT;
2. ROLLBACK 
The ROLLBACK command is used to undo all the transactions that have been performed during the current transaction but have not yet been committed.
This command is useful for reverting the database to its previous state in case an error occurs or if the changes made are not desired.
Syntax:  

ROLLBACK;
3. SAVEPOINT
The SAVEPOINT command is used to set a point within a transaction to which we can later roll back.
This command allows for partial rollbacks within a transaction, providing more control over which parts of a transaction to undo.
Syntax:  

SAVEPOINT savepoint_name;


