# Overview

Search index has multiple efficient index schemas to help you process complex queries in big data scenarios.

## Purposes

Data tables in Tablestore use distributed NoSQL data structures. Data such as monitoring data and log data can be stored, read, and written at a large scale.

Tablestore provides the search index feature to meet your requirements for complex queries. These queries include Boolean query, queries based on primary keys including single-row read and range read, and queries based on non-primary key columns.

The search index feature is implemented by using inverted indexes and column-oriented storage. This feature provides query methods to solve problems in complex big data scenarios. The query and analysis methods include full-text search, fuzzy query, prefix query, geo query, Boolean query, nested query, and aggregation. Aggregation can be implemented by using max, min, count, sum, avg, distinct\_count, and group\_by.

## Differences among indexes

Aside from queries based on primary keys in data tables, Tablestore provides two index schemas for accelerated queries: global secondary index and search index. The following table describes the differences among the three types of indexes.

|Index type|Description|Scenarios|
|----------|-----------|---------|
|Primary key of a data table|A data table is similar to a large map. Data tables support queries based only on primary keys.|You can specify a complete primary key or the prefix of a primary key.|
|Global secondary index|You can create one or more index tables and perform queries by using the primary key columns of the index tables.|You can define the required columns in advance. Therefore, only a small number of columns are queried. You can also specify a complete primary key or the prefix of a primary key.|
|Search index|The search index feature uses inverted indexes, Bkd-trees, and column-oriented storage for various query scenarios.|The following scenarios do not support the use of a primary key of a data table and the secondary index feature:-   Query based on non-primary key columns
-   Boolean query
-   Query by using operators AND, OR, and NOT
-   Full-text search
-   Geo query
-   Prefix query
-   Fuzzy query
-   Nested query
-   Exists query
-   Aggregation by using min, max, sum, avg, count, distinct\_count, and group\_by |

Compared with indexes of traditional database services such as MySQL, the search index feature is not subject to the leftmost matching principle. Therefore, the search index feature can be used in more scenarios. In most cases, only one search index is required for a data table. For example, a data table about student information may contain the name, student number, gender, grade, class, and home address columns. When you create a search index, you can add these columns to the search index. When you use the search index, you can specify a combination of conditions. Examples: students named Tom in Grade Three, male students who live one kilometer away from their school, and students in Class Two, Grade Three who live in the specified residential community.

## Operations

The search index feature provides the Search and ParallelScan operations. The Search operation is used for general queries. The ParallelScan operation is used for data export.

Most features of the two operations are the same. However, to improve the performance and throughput, ParallelScan does not provide some functions of the Search operation. The following table compares the two API operations.

|Operation|Description|
|---------|-----------|
|Search|Supports all features of search index. -   Query: query based on non-primary key columns, full-text search, prefix query, fuzzy query, Boolean query, nested query, and geo query.
-   Collapse \(distinct\)
-   Sorting
-   Aggregation
-   Total number of rows |
|ComputeSplits+ParallelScan|Exports data for multiple concurrent queries. The query features of search index are supported. However, analysis features such as sorting and aggregation are not supported. ParallelScan can use concurrent queries to provide better performance and up to five times the throughput of a single Search request.

-   Query: query based on non-primary key columns, full-text search, prefix query, fuzzy query, Boolean query, nested query, and geo query.
-   Multiple concurrent queries in a single request |

## Usage notes

-   Synchronization for indexes

    After a search index is created for a data table, data is written to the data table first. After the data is written to the data table, a success message is returned. When the system writes data to the data table, another asynchronous thread reads the newly written data from the data table and writes the data to the search index. The write performance of Tablestore is not affected when data is being asynchronously synchronized from a data table to a search index.

    Typically, the latency generated when data is synchronized for a search index is within 10 seconds. You can view the latency in real time in the Tablestore console.

-   TTL

    Search index does not support the time to live \(TTL\) feature. You cannot create a search index within a data table that has the TTL parameter set.

    If you want to retain the data only for a period of time and the time field does not need to be updated, you can implement the TTL feature by splitting a data table into several time-specific data tables. This solution is implemented based on the following principles and rules, and has the following advantages:

    -   Principle: Split a data table based on fixed periods of time, such as day, week, month, or year. Then, you can create a search index for each table. This way, tables for the specified periods of time are retained.

        For example, if you want to retain data for six months, you can store the data for each month in its own data table \(such as table\_1, table\_2, table\_3, table\_4, table\_5, and table\_6\) and create a search index for each data table. Each data table and search index stores the data only for a single month. Then, you need only to delete data tables that are retained for more than six months to implement the same feature as TTL.

        When you query data by using search index, you need only to query one table if data that meets the time range requirement is in that table. If data that meets the time range requirement is included in multiple tables, you need to query all these tables and then combine the query results.

    -   Rule: The size of a single table or search index cannot exceed 50 billion rows. The search index feature provides the optimal query performance if the size of a single table or search index does not exceed 20 billion rows.
    -   Advantages:
        -   You can adjust the data storage duration based on the number of data tables retained.
        -   Query performance is directly proportional to data volumes. After a data table is split into multiple data tables, the data volume of each data table has an upper limit. This helps ensure better query performance and avoid low query latencies or timeouts.
-   Max versions

    You cannot create a search index for a data table for which you have specified the max versions parameter.

    You can customize the timestamp when you write data to a column that allows only a single version. If you first write data with a greater version number and then write data with a smaller version number, the index that contains the data with the greater version number may be overwritten by data with the smaller version number.


## Features

Search index can solve complex query problems in big data scenarios. Other systems such as databases and search engines can also solve data query problems. The following figure shows the differences between Tablestore and databases and search engines.

Tablestore can provide all features of databases and search engines except JOIN operations, transactions, and relevance of search results. Tablestore also has high data reliability of databases and supports advanced queries of search engines. Therefore, Tablestore can be used to replace the common architecture that consist of `databases and search engines`. If you do not need JOIN operations, transactions, or relevance of search results, we recommend that you use the search index feature of Tablestore.

![Differences between Tablestore and databases and search engines](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9818958951/p55724.png)

