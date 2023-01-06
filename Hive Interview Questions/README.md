Credits : *Code Mechanics* youtube channel

Q. What is the default database of hive to store its metadata?
- Derby database

Q. What is the default execution engine in hive?
- MapReduce
- We can it in ```/etc/hive/conf/hive-site.xml``` file
```
1. vi /etc/hive/conf/hive-site.xml
2. esc from keyboard
3. Type : /execution.engine
4. Press 'Enter' and find the value

```
- This property could be changed using SET command.


Q. How to change the default execution engine in hive?
- SET hive.execution.engine = tez;

Q. Which component of hive connects to hadoop cluster to execute queries?
- Its the execution engine like MapReduce or Tez.

Q. Which component of hive converts SQL queries to jar file for execution?
- Execution engine.

Q. How do you see the execution plan of a query?
- Use EXPLAIN keyword.
```
EXPLAIN SELECT * FROM CUSTOMER;
```
Q. How do you see the database in which you are currently working on?
- SET hive.cli.print.current.db = true

Q. What is the default storage location of the hive tables?
- /user/hive/warehouse.

Q. What do you do to have a table for a particular session?
- Create temporary tables.
- /tmp/hive/'<user>'/*
- Temporary tables will be removed when the session is closed.

Q. What are the limitations of hive temporary tables?
- Only for a given session.
- Partition columns is not supported.
- Indexes are not supported.
- The user cannot access the parmanent table with same name of temporary table unless the temporary table is dropped.


