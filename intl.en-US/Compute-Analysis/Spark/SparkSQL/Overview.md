Overview 
=============================

Spark helps you perform complex computing and efficient analysis on Tablestore data by using E-MapReduce (EMR) SQL or DataFrame.



Features 
-----------------------------

In addition to basic features, Tablestore Spark connector provides the following core features for batch computing:

* Index selection: An appropriate index determines the efficiency of data queries. You can select an index that best matches filter conditions to increase the query efficiency. Tablestore allows you to choose between global secondary index and search index.

  **Note**

  For more information about global secondary index and search index, see [Reveal of the storage technologies for large amounts of structured data: Tablestore storage and index engines](https://developer.aliyun.com/article/710400).
  

* Partition pruning: This feature allows you to filter unnecessary splits in advance by using fine-grained configurations based on filter conditions, which reduces the amount of data sent from the server.

  

* Projection column and Filter pushdown: This feature allows you to push down the Projection column and Filter condition to the server, which reduces the amount of data sent from each partition.

  

* Dynamical adjustment of the size of each split: This feature allows you to adjust the size of each split and the number of splits. Each split is bound to the partition of a Resilient Distributed Dataset (RDD), which accelerates the execution of Spark tasks.

  **Note**

  You can call the ComputeSplitsBySize operation to obtain the splits. This operation splits all data in the table into splits based on the specified size and returns the split points between these splits and the information of the machine where the splits are distributed. This operation is used to execute plans such as concurrency plans on computing engines.
  




Stream computing uses change data capture (CDC) to complete streaming consumption and computing in micro-batch mode of Spark based on [Overview](/intl.en-US/Function Introduction/Tunnel service/Overview.md). Meanwhile, at-least-once semantics is provided. In stream computing, one split is bound to one partition of an RDD. Partitions of a table can be scaled out to implement the linear scalability of data throughput.

Use EMR SQL or DataFrame 
---------------------------------------------

You can use one of the following methods to access Tablestore by using Spark: Use EMR SQL and DataFrame.

* Use EMR SQL

  This method uses standard SQL statements to access and perform operations on business data. You can use this method to implement seamless migration of existing business logic.
  

* Use DataFrame

  This method requires programming knowledge. However, you can combine this method with other features to execute complex business logic, which is suitable for complex and flexible scenarios.
  




Data access method 
---------------------------------------

Tablestore provides the following methods for batch computing of Spark to access data: KV-based queries from a table or global search index and search index-based queries. This way, in addition to a wide range of query and analysis capabilities, Tablestore allows you to read and write large amounts of data.

Differences between the two data access methods:

* KV-based queries implement high efficiency when the fields specified for filtering are primary key columns. However, this method is not suitable when the fields specified for filtering are non-primary key columns because these field values often change. In addition, KV-based queries do not support geo query.

  

* Search index-based queries apply to the following data access scenarios.

  **Note**

  Based on inverted indexes and column-oriented storage, search index provides query and analysis features such as [full-text search, fuzzy query, geo query, and aggregation](/intl.en-US/Function Introduction/Search Index/Overview.md) that are similar to those of Elasticsearch.
  * A few real-time data analysis scenarios that have high requirements on latencies.

    
  
  * Multiple fields specified for filtering are non-primary key columns. These fields cannot be contained by the primary key of the global secondary index or table.

    
  
  * Filtering efficiency is high when fields are used for filtering. A field can be used to filter out most data.

    For example, in `select * from table where col = 1000;`, col indicates the non-primary key column. The condition col = 1000 can be used to filter out most data.
    
  
  * Query conditions contain fields for geo query.

    
  

  




The following section uses a figure and SQL statement `select * from table where col1 like 'A%' or col2 = 'a';` to describe how to use the two query types.

* When you use search index to access data, you can obtain a row of data (pk1 = 1) that contains the col1 column whose value is 'A%' from the search index. You can also obtain two rows of data (pk1 = 1 and pk1 = 2) that contain the col2 column whose value is 'a' from the search index. Then, the two results are concatenated by using union to obtain data that meets conditions (`pk1 = 1,col1 = 'Alibaba Cloud',col2 = 'a'`).

  

* When you use KV-based queries, data is queried from the Tablestore table. The table can be queried only by using primary key columns. If the field specified in the SQL statement is not a primary key column of the table, the whole table must be scanned.

  col1 is not the primary key column of the table. Therefore, Tablestore scans the whole table to search for a row of data that contains the col2 column whose value is 'A%'. col2 is not the primary key column of the table. Therefore, Tablestore scans the whole table again to search for two rows of data that contain the col2 column whose value is ''a''. Then, the two results are concatenated by using union.

  You can also create an index table where the primary key columns are col1 and col2. However, this method reduces flexibility.
  



