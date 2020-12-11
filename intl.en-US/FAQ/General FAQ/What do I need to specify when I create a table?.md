What do I need to specify when I create a table? 
=====================================================================

Tablestore can store semi-structured data. When you create a table, you need only to specify one to four primary key columns. Attribute columns are optional.

Each table can contain an unlimited number of attribute columns. Rows in each table can contain attribute columns that differ in column quantity and supported data types. When you use an application to write data, Tablestore requires that the application have the names of primary key columns and attribute columns and the values in all these columns specified.

What is partition key? 
-------------------------------------------

The first primary key column is called the partition key. When the size of data in a table reaches a specified value, Tablestore distributes data to different partitions based on the range of values in the partition key to implement load balancing

By default, data in a table is distributed to one partition when the table is created. When multiple partitions are created for the table, data distributed to a partition shares the same specified range of the values in the partition key. The range of values in a partition key is sorted by the system based on different data types of the values such as INTEGER and STRING.

The number of partitions affects performance when you access data and the utilization of reserved read/write throughput. When a table contains multiple partitions, the reserved read/write throughput is pre-distributed to each partition based on a proportion.

How can I select a partition key? 
------------------------------------------------------

When you create a table, select a partition key based on the following rules to ensure access performance if the table stores large amounts of data.

* Do not use primary key columns whose range of values is narrow, such as customer gender Male or Female.

* Do not use primary key columns whose values may be frequently accessed. For example, the primary key column that contains timestamps is used as the partition key.

* We recommend that you use primary key columns that contain data that is less frequently accessed as the partition key. For example, you can select the UserID column.




What do I do if I cannot predict which data will be frequently accessed when I select a partition key? 
---------------------------------------------------------------------------------------------------------------------------

We recommend that you calculate the hash value of data in a column before you write data to a partition key. For example, when you write a row of data, you can use simple algorithms to calculate the hash value of characters from a user ID, concatenate the hash value and the actual value, and store the concatenation result of the user ID in the table. You can use this lightweight operation to distribute access. Note that a value in a partition key is concatenated by a hash value and actual value. You cannot perform the GetRange operation based on the partition key.

Why Tablestore limits the number of tables that an account can have? 
-----------------------------------------------------------------------------------------

Each Tablestore account can create a maximum of 10 instances. Each instance can have 64 tables created. In other words, a maximum of 640 tables can be created in an account.

The following situations are considered to increase the number of tables:

* Large amounts of data and high access performance Unlike traditional SQL-based database services such as MySQL that addresses access to large amounts of data by using sharding, Tablestore resolves these bottlenecks by implementing a distributed solution.

  You can store structured or semi-structured data in a large sparse table without having to worry about performance issues when the amount of data to access is extremely large.
  

* Rapid business increase In addition to the increase based on existing data and increase of visits, you may use Tablestore to provide services for your customers such as the third-party partners and providers. To provide services for the providers, you can deploy a table for each provider after you activate Tablestore. The number of tables may quickly reach the upper limit. The constant increase of the upper limit can cause maintenance costs uncontrollable and make global data analysis more difficult.

  We recommend that you use Tablestore in a nontraditional way and store large amounts of structured and semi-structured data in a table.
  

* Considerations for Tablestore The distributed mode of Tablestore makes the number of tables a resource attribute of Tablestore. When the scale of the Tablestore cluster is specified, Tablestore imposes no limits on the maximum number of tables. The scalability of Tablestore allows you to increase the upper limit. Tablestore specifies the upper limit of tables in an account in terms of controllability of Tablestore resources.

  




To increase the upper limit of tables in an account, submit a ticket.
