# Overview

Tablestore is a multi-model service developed by Alibaba Cloud. Tablestore can store a large amount of structured data. Wide Column is one of the models used in Tablestore. This topic describes the components of the Wide Column model and the differences between the Wide Column model and relational model.

## Components

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3818958951/p11642.png)

The preceding figure shows the components of the Wide Column mode. The following table lists the components of the Wide Column model.

|Components|Description|
|----------|-----------|
|Primary key|Primary keys uniquely identify each row in tables. A primary key consists of one to four primary key columns.|
|Partition key|The first primary key column is called the partition key. Tablestore partitions data in a table based on partition keys. Rows that share the same partition key values are distributed to the same partition for load balancing.|
|Attribute column|All columns except for the primary key columns in a row are called attributed columns. Each attribute column can contain values of different versions. Tablestore does not impose limits on the number of attribute columns that can be contained in each row.|
|Version|Each value in an attribute column has a unique version number. The version number uses a timestamp based on which to define time to live \(TTL\).|
|Data type|Tablestore supports the following data types: STRING, BINARY, DOUBLE, INTEGER, and BOOLEAN.|
|TTL|You can configure TTL for each table. If you set TTL to one month for a table, Tablestore deletes data that is stored in the table from the previous month.|
|Max versions|You can set the maximum number of versions for the value in each attribute column of one table. Max versions can be used to control the number of versions for the value in each attribute column. When the actual number of versions in an attribute column exceeds the max versions value, Tablestore asynchronously deletes earlier versions.|

## Compare the Wide Column model with the relational model

The following table lists the differences between the Wide Column model and relational model.

|Model|Feature|
|-----|-------|
|Wide Column model|Three-dimensional structure \(row, column, and time\), schema-free data, wide row, max versions, and TTL.|
|Relational model|Two-dimensional structure \(row and column\) and traditional schema.|

