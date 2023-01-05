hive
dfs -ls -help;
dfs -ls -R ;

MR or Tez engine for hive execution
Warehouse directory --> By Default

hive -e "SET;" | grep warehouse

SET hive.metastore.warehouse.dir;
hive.metastore.warehouse.dir=/apps/hive/warehuose


------------------ Create database --------------------------

hive 

SET hive.metastore.warehouse.dir = /user/itv004887/warehouse

create database training_retail; --> create training_retail.db in warehouse directory
Sub dir under the warehouse dir

DATABASE name : training_retail_ritesh
USE training_retail_ritesh;



hadoop fs -ls /user/itv004887/warehouse | grep -w training_retail

You can create as many databases as you want.

Create DATABASE IF NOT EXISTS training_retail;


---------------- Creating Table in a database ------------------
USE training_retail_ritesh;

CREATE TABLE NEW_ORDERS
(
	ORDER_ID INT,
	ORDER_DATE STRING,
	ORDER_NAME STRING
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',';

table --> folder -->
hdfs://m01.itversity.com:9000/user/itv004887/warehouse/training_retail_ritesh.db/new_orders

dfs -ls /user/itv004887/warehouse/
dfs -ls /user/itv004887/warehouse/training_retail_ritesh.db

describe new_orders;

------------- RETRIEVE METADATA OF HIVE -----------------

DESCRIBE EXTENDED NEW_ORDERS;

DESCRIBE FORMATTED NEW_ORDERS;


mETA DATA IS NOT STORED IN hdfs LOCATION DUE TO PERFORMANCE ISSUES

/etc/hive/conf/hive-site.xml 

ls -lrt /etc/hive/conf/

jdbc:postgresql://g01.itversity.com:5432/metastore

hive.metastore.dbaccess.ssl.properties


-----------
hive cli is depricated , it did not have the authentication and authorization features

Now for authentication and authorization beeline is used.
Hive is connected using JDBC from beeline


beeline --help
beeline
!list
!history
!sh ls -ltr
!connect jdbc:hive2://<hostname>:<port# 10000>/training_retail
give username and pwd 

show tables;

beeline -u jdbc:hive2://<hostname>:<port# 10000>/training_retail
-n <user>

show tables;
show create tables;
describe formatted <table_name>

we can use -e flag to run hive queries from linux terminal



--- Create tables in hive --
temp/external/managed

hive --database <database name>

DATA --> cd /data/retail_db
this folder has the data in comma delimitted format.
view part file

string for date and status

CREATE TABLE NEW_ORDERS
(
		order_id int,
		order_date string,
		order_cust_id int,
		order_status string

)
row format delimited fields terminated by ','

location of the table 
type of the table

----Data types in hive -------

Q. What are different datatypes in hive :
primitive, array , map , struct, union

Hive is schema on read ,...not schema on write

When you load the tables the data types are not enforced...its only when you read from the 
table then only the data type is enforced.

----- Adding comments on columns and tables --

DROP TABLE ><;

CREATE TABLE ORDES(
	ORDER_ID int COMMENT 'unique order id',
	order_date string comment 'date on which the order is placed',
	order_customer_id int comment 'customers who placed the orders'
	order_status string comment 'Table to save order level details'
) comment 'tables  to save order level details';

describe table_name;
describe formatted table_name;



------Loading data into hive tables from local-----
cd /data/retail_db/orders/

ASCII -- default delimited

Create the table.
DESCRIBE formatted <table>

Check the delimiter of the table and the data.

try to insert the records.


LOAD DATA LOCAL INPATH '/data/retail_db/orders' into table orders;

dfs -ls <hdfs location>

dfs -tail <full path >

Select * from orders limit 100;


Drop table orders;

mention the --> row format delimiter fields terminated by ','

try to load the file another time and list the hdfs location.


--------- Loading data from tables from HDFS location --------------------

hadoop fs -ls /public/retail_db/orders/


LOAD DATA INPATH '<>' into table orders;

hdfs fs -ls /user/training/retail_db/orders/

Check the permissions

It actually moves the files.

select * from orders limkt 100;

for moving just need to update the metadata of the files...just changing the pointers
move is much faster than to copy over network..

---------Overwrite and append -------------------------

Load  --> append

Overwrite -->
LOAD DATA LOCAL INPATH '/data/retail_db/orders' overwrite into table orders;

full load vs append load.


--------External table ----------------------


CREATE EXTERNAL TABLE ORDERS (.......)

try to load the data from local

select queries to validate..


-------Specifying location for the hive tables ----------

By default it will choose the hive warehouse directory.

SET hive.metastore.warehouse.dir;

extra thing in tail to add  -->

LOCATION '/public/retail_db/orders' 
--> Suppose to have write permission to destination

Same load data command


-----Extrnal vs managed ----------

described formatted;

Check the location
DROP table table_name--> For ext table just deleting the metadata but not touching the data,
DROP table in managed table the folder is gone with data.

Let suppose that particular data has the dependency upon the multiple frameworks
In that case using external table is the safest option so that other frameworks
do not face any impact.


-------Default delimiters in hive using text file format ---------
Tect file will no have metadata, we need to spcify the delimiters
default : ASCII

1. DEOP Existing
2. Create table orders with default delimiters
3. Decribe formatted
4. Insert into the table 
5. All null with , del files.
6. Create the table order_stage with delimiter ','
7. Describe formatted
8. load data command into order stage
9. select query
10. coiunt(*)
11. Insert into table orders select * from order_stage
12. Describe formatted orders
13. haddop fs -get <hdfs location>
14. view file_name : See the ascii delimiters


-------------  Overview of file formats - stored as clause ---------

If we want to store the data with any other file format,
we need to use the stored as clause

RC file is depricated and has been replaced by the ORC file format
ORC and the parquet are the most popular columnar file formats.

AVRO : Binary json.

Sequence file : outdated

if your data is not in a particular file format use a stage table in middle, then convert into desired file format in a new atble..


------------    Hive vs Traditional RDBMS --------------------------
RDMS : transactional systems

schema on write in RDMS : Data quality but not good for batch load

1. Schema on read and schema on write.
2. Transactional processing vs Batch processing
3. Scale of the data.
4. Indexing
5. ACID
6. DCL :  Commits and rollbacks
7. Metastore and Data decoupling


------------   Truncating and dropping tables in hive --------------

hive --database training_retail
show tables;
dscribe formatted <table name>
hdfs -ls 

drop table orders;

dfs -ls


if it is external table the data will not get deleted.

drop database ;


truncate will delete the data but not the metadata.

1. Create database
2. Create table
3. Load data
4. select * 
5. Describe formatted
6. truncate table <>
7. List the hdfs dir

1. Create the external table;
2. Describe formatted <>;
3. Load the data
4. dfs -ls
5. Truncate table  <>: Cannot truncate non-managed table orders

Truncate table is applicable only for the managed table.

DROP table <>;
dfs -rm -R <hdfs path>


### Partitioning and bucketing in hive 

Mananging large tables : partitioing
Performance relatted issues for storing in a single file -> Patitioning and Bucketing

: By default the partitioning means the list partitioning in case of hive

Hash partitioning is known as bucketing when it comes to hive


### Creating Tables using ORC file format 

1. Create database if not exists;
2. use trainig_retail ;
3. show tables;
4. orc --> columnar storage in binary format
5. cd /data/retail_db/order_items/ : here is the part file which is a ',' sep text file.
6. create table orders (.......float for decimal) stored as orc;

describe formatted. : check the  input format / output format 

load data local path '<>' path into table order_items; : error due to file format

### Inserting data into table for specific file format

create a stage table
load from source

create table order_item_stg (...) row format....

load data into the table
validate with select to review the data.

wc -l part-0000

insert into table order_item select * from order_items_stg;

using insert the format is being changed.Mapr takes care internally.

dfs -tail --> binary file format..

orc replaced the rc file format.


source --> stg --> insert into --> orc file format table

### LOAD Cmd for ORC file format.

Load : w/o any transformation and performing faster
Insert : with the transformation and performing slower

LOAD Just update the metadata.

SHOW TABLES;
LOAD DATA LOCAL INPATH '' INTO ORDERS;

Copies the file as is using LOAD.MAin file name is same.
File format are same then LOAD will work.

DROP Table
Create w/o row format.
Try load : Successful for same file format.
Check the delimiter. Why NULLS.

DROP TABLE and try the same with STRING with the first field.

We cannot load from order_item_stg table to order_items tables
using LOAD command as the field delimitters are different.

Hence transformations are required using INSERT.

INSERT OVERWRITe TABLE order_items select * from order_items_stg.

Insert : Takes time.

Q. What is the difference b/w LOAD a table and INSERT into a table in hive?
ANS : 
1. LOAD is to be used only when we are sure that both tables are in similar format (e.g : Having same delimiters).If the format is different we need to use INSERT COMMAND.

2. LOAD operation is faster as it is similar to copying or moving the file. However, Insert operation is slower as it is meant to use for transformation purposes.


### Creating Partitioned table in HIVE

PARTION BY col datatype.

```
CREATE TABLE order_part(...)
PARTITIONED BY (order_month INT)
...;

```

order_month is not part of the actual data, therefore we need to derive.

We cannot use LOAD here, as partioning is done.

Try the load command to validate:
```
LOAD DATA LOCAL INPATH '/data/retail_db/orders' INTO TABLE order_part;
```
In many cases the data will not be prepartitioned in the sources.
Therefore it is always good to create a stage table, partition there and load the target table.


### Adding partitions to tables in hive

We cannot mention the same col name used in partion by clause inside the create table clause, if we do so it will throw error.

```
ALTER TABLE <> ADD PARTITION (order_mont = 201307) PARTITION (order_mont = 201308) PARTITION (order_mont = 201308) ...;
```

Check the data directory: 
```
dfs -ls path
```

Check the path.


### LOADING into tables with partition



### How to create bucketted tables

Bucketted tables are called hashed tables in traditional RDBMSs

Bucketting  = Hash Partions

Hash Value will be created based on a key.
Based on the hash value the data will be stored into the corresponding bucket.

```
clustered by (col1 , col2,  ...) 

Create table orders_buck (fields....)
clustered by (order_id) into 8 buckets
row format fields delimited by ',';

describe formatted <table name>
```

We use insert command to load the data into bucketted table.
We don't use LOAD command.


### Insering data into buckettig tables

As the data is prerpocessed with the bucketting logic, we need to use the insert command.

```
DESCRIBE FORMATTED TABLE NAME;
```
Check the buckets.

```
INSERT INTO TABLE order_buck
select * from orders ;

```

We need to enfore the bucketting by :

```
hive.enforce.bucketing = true ;
```

Check if we have the data
```
SELECT * FROM ORDERS_BUCK LIMIT 10;

DESCRIBE FORMATTED;

DFS -LS <HDFS LOACTION>

DFS -TAIL <FILE>
```

### Bucketing with sorting
SHOW CREATE TABLE;
DROP TABLE

```
....
....
CLUSTERED BY (COL1...)
SORTED BY (COL2...)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','


INSERT INTO TABLE........................
```

```
DFS -TAIL <HDFS LOACTION>
```


### Overview of ACID Transactions in Hive

Hive is used for heavy weight batch processing, therefore transactions for individual records are not that feasible.

File format should be compatible with ACID transactions.
One such file format is orc.

We need to bucket the records in orc file format.

Create a table and insert a record into the table.

insert into <> values ('','','',...') ;

Need to make sure that we select proper engine in configuration.
It should be TEZ.

Now if we try to UPDATE a record, it will not work.

ACID transaction is required to maintain SCD.


### Create tables for the ACID transaction

```
cd /etc/hive/conf/

grep -i txn hive-site.xml

```

Check the 'hive.txn.manager' -> Hive Transaction manager.
1. We need to change it from DummyTxnManager to DbTxnManager.
2. Also, we need to update hive.support.concurrency =true
3. Recreate the table in orc format.
4. ALTER TABLE orders SET TBLPROPERTIES('transaction' = 'true')

Now we can check the UPDATE Query.


















































