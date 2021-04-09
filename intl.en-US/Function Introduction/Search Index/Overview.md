# Overview

Search index has multiple efficient index schemas to cope with complex queries in big data scenarios.

## Background

Data tables in Tablestore are typical distributed NoSQL data structures. The tables support the storage and reads or writes of large-scale data such as monitoring data and log data.

Tablestore introduced the search index feature to meet requirements for the following queries: complex queries such as query based on non-primary key columns and Boolean query, and query based on primary keys including single-row read and range read.

Search indexes support a variety of queries types based on inverted indexes and column-oriented storage, including query based on non-primary key columns, full-text query, prefix query, fuzzy query, Boolean query, nested query, and geo query.

## Index differences

Aside from queries based on primary keys in data tables, Tablestore provides two index schemas for accelerated queries secondary index and search index. The following table describes the differences among the three types of indexes.

|Index type|Description|Scenario|
|----------|-----------|--------|
|Primary key|A data table is similar to a large map. Data tables only support queries based on primary keys.|You can determine a complete primary key or the prefix of a primary key.|
|Secondary index|You can create one or more index tables and perform queries based on the primary key columns of the index tables.|Only a few columns are queried and you can determine the required columns in advance. You can also determine a complete primary key or the prefix of a primary key.|
|Search index|Search index uses inverted indexes, Bkd-trees, and column-oriented storage for various query scenarios.|All query and analysis scenarios that the primary key and the Secondary index do not support.|

## API operations

Search index provides the common query \(Search\) and data export \(ParallelScan\) API operations.

Most functions of the two operations are the same. However, to improve the performance and throughput, ParallelScan does not provide some functions of the Search operation. The following table compares the two API operations.

|API operation|Description|
|-------------|-----------|
|Search|Supports all functions of search index.-   Query: query based on non-primary key columns, full-text query, prefix query, fuzzy query, Boolean query, nested query, and geo query.
-   Collapse \(distinct\)
-   Sorting
-   Aggregation
-   Count |
|ComputeSplits+ParallelScan|Exports data for multiple concurrent queries.ParallelScan can use concurrent queries to provide better performance and up to five times the throughput of a single Search request.

-   Query: query based on non-primary key columns, full-text query, prefix query, fuzzy query, Boolean query, nested query, and geo query.
-   Multiple concurrent queries in a single request |

## Precautions

-   Index synchronization

    If you have created a search index for a data table, data is written to the data table first. After the data is written to the data table, a success message is returned. At the same time, another asynchronous thread reads the newly written data from the data table and writes the data to the search index. The write performance of Tablestore is not affected when data is being synchronized between the data table and search index.

    Typically, the latency of search index is less than 10 seconds. You can view the latency in real time in the Tablestore console.

-   TTL

    Search index does not support the time to live \(TTL\) feature. You cannot create a search index within a data table that has the TTL parameter set.

    If you want to retain the data only for a period of time and the time field does not need to be updated, you can implement the TTL feature by splitting a data table into several time-based tables. This solution is implemented based on the following principles and rules, and has the following advantages.

    -   Principle: Split a table based on fixed periods of time, such as day, week, month, or year, and create a search index for each table. In this way, tables for the specified periods of time are retained.

        For example, if you want to retain data for six months, you can store the data for each month in its own data table \(such as table\_1, table\_2, table\_3, table\_4, table\_5, and table\_6\) and create a search index for each one. Each data table and search index stores only the data for a single month. Then, you only need to delete data tables that are over six months old to implement the same function as TTL.

        When you query data by using search index, you only need to query one table if data in the required time range is in that table. If data in the required time range is included in multiple tables, you need to query all these tables and then combine the query results.

    -   Rule: The size of a single table or index cannot exceed 50 billion rows. Search index provides the optimal query performance if the size of a single table or index does not exceed 20 billion rows.
    -   Advantages:
        -   You can adjust the data storage duration based on the number of data tables retained.
        -   Query performance is directly proportional to data volume. When a data table is split into multiple tables, the data volume of each table has an upper limit. This helps ensure better query performance and avoid low query delays or timeouts.
-   Max versions

    You cannot create a search index in a table where you have specified the max versions parameter.

    You can customize the timestamp whenever you write data to a data table that only allows a single version. If you first write data with a major version number and then data with a minor version number, the index of the data with the major version number may be overwritten by that of the data with the minor version number.


## Features

Search index can solve complex query problems in big data scenarios. Other systems such as databases and search engines can also solve data query problems. The following figure shows the differences between Tablestore and databases and search engines.

Tablestore can provide all of the features of databases and search engines except join operations, transactions, and relevance of search results. Tablestore also has high data reliability of databases and supports advanced queries of search engines. Because of this, Tablestore can be used to replace the common architecture of `database + search engine`. If you do not need join operations, transactions, or relevance of search results, we recommend that you use search index of Tablestore.

![Differences between Tablestore and databases and search engines](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9818958951/p55724.png)

