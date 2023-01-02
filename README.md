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




















