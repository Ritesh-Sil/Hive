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

















