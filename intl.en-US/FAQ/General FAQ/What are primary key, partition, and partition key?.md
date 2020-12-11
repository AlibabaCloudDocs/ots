# What are primary key, partition, and partition key?

## Primary key

Each row is uniquely identified by the primary key. You must specify columns that compose a primary key when you create a table. These columns are called primary key columns. Each primary key column must contain values. You must ensure that the combination of values of primary key columns can identify a row. The data types of values in primary key columns cannot be modified after the data types are specified.

## Partition and partition key

Tablestore automatically splits tables into different partitions to balance data loads. The first column of a primary key is called the partition key.

Data that shares the same partition keys is distributed to the same partition. You can use the partition keys to modify data in the same partition, which ensures consistency.

The following figure shows a part of a mailing list from an email system. Information about the primary key and partition key:

-   UserID, ReceiveTime, and FromAddr are primary key columns that uniquely identify an email. UserID is the partition key.
-   ToAddr, MailSize, Subject, and Read are attribute columns that are used to store information related to emails.
-   Data whose user ID is U0001 and U0002 is distributed to a partition. Data whose user ID is U0003 and U0004 is distributed to another partition.

![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/38572/cn_zh/1512466746845/38572-01.png)

