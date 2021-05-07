# Overview

This topic describes the terms and features of secondary indexes. This topic also describes the differences between secondary indexes and the items you must take note of when you use secondary indexes.

## Background information

You can create one or more index tables on a data table and use the primary key columns of the index tables to query data. This way, you do not need to query the corresponding data table, and the data can be more efficiently queried.

To meet requirements such as strongly consistent queries, Tablestore allows you to use local secondary indexes in addition to global secondary indexes.

## Differences between secondary indexes

Secondary indexes include global secondary indexes and local secondary indexes.

|Secondary index|Difference|
|---------------|----------|
|Global secondary index|-   Tablestore can synchronize the columns to be indexed and data in primary key columns from a data table to an index table in asynchronous mode. The synchronization latency is within a few milliseconds.
-   You can select a primary key column from a data table or a predefined column as the first primary key column of a global secondary index. |
|Local secondary index|-   Tablestore can synchronize the columns to be indexed and data in primary key columns from a data table to an index table in synchronous mode. You can query the data from the index table after the data is written to the data table.
-   The first primary key column of the index table must be the same as the first primary key column of the data table. |

## Terms

|Term|Description|
|:---|:----------|
|index table|The table created based on indexed columns from a data table. Data in the index table is read-only. |
|predefined column|The non-primary key column predefined when a data table is created. A non-primary key column is used as an indexed attribute column. You can also specify the data type of the non-primary key column. **Note:** Tablestore uses a schema-free model. You can write different attribute columns to a row. You do not need to specify attribute columns in a schema. |
|single-column index|The index that is created on a single column.|
|compound index|The index that is created on multiple columns in a data table. For example, you can specify two indexed columns for a compound index.|
|indexed attribute column|The predefined column mapped to an index table.|
|autocomplete|The feature that allows Tablestore to automatically add the primary key columns that are not specified as indexed columns to an index table.|

## Features

-   Single-column index and compound index

    You can create an index on one or more columns in a data table.

-   Index synchronization

    Global secondary indexes and local secondary indexes differ in synchronization modes.

    -   If you use the global secondary index feature, Tablestore automatically synchronizes the columns to index and data in primary key columns from a data table to an index table in asynchronous mode. The synchronization latency is within a few milliseconds.
    -   If you use the local secondary index feature, Tablestore automatically synchronizes data from the indexed columns and the primary key columns of a data table to the columns of an index table in synchronous mode. After the data is written to the data table, you can query the data from the index table.
-   Covered index

    Index tables can contain attribute columns. You can create predefined columns when you create a data table. Then, you can create an index table based on a combination of predefined columns and primary key columns of the data table. You can also specify predefined columns as attribute columns for the index table. You can also choose not to specify indexed attribute columns.

    If you specify one or more predefined columns of a data table as indexed attribute columns, you can obtain the data stored in the corresponding predefined columns from the index table without the need to query the data table.

-   Index that contains existing data from a data table

    You can create an index table that contains existing data from a data table.

-   Sparse index

    You can specify a predefined column as an indexed attribute column for an index table. If a row in a data table does not contain the predefined column but contains all indexed columns, an index is created for the row. However, an index is not created for a row when the row contains no indexed columns.

    For example, a data table contains primary key columns PK0, PK1, and PK2 and predefined columns Defined0, Defined1, and Defined2. You can create an index table that contains primary key columns PK0, Defined0, and Defined1 and indexed attribute column Defined2.

    -   The index table contains a row from the data table that contains Defined0 and Defined1 but does not contain Defined2.
    -   The index table does not contain a row from the data table that contains Defined0 and Defined2 but does not contain Defined1.

## Limits

For more information, see [Secondary index](/intl.en-US/Function Introduction/Limits/Secondary index.md).

## Usage notes

When you configure indexed columns and attribute columns for an index table, take note of the following items:

-   Tablestore automatically adds the primary key columns that are not specified as indexed columns to an index table. When you scan an index table, you must specify the range of primary key columns. The range can be from negative infinity to positive infinity.

    When you create an index table, you need only to specify the columns that you want to index. Tablestore automatically adds all primary key columns of the corresponding data table to the index table. For example, a data table contains the PK0 and PK1 primary key columns and the Defined0 predefined column.

    If you use the global secondary index feature, you can create an index on the required columns.

    -   If you create an index on the Defined0 predefined column, Tablestore generates the index table that contains primary key columns Defined0, PK0, and PK1.
    -   If you create an index on the Defined0 predefined column and PK1 primary key column, Tablestore generates the index table that contains primary key columns Defined0, PK0, and PK1.
    -   If you create an index on the PK1 primary key column, Tablestore generates the index table that contains primary key columns PK1 and PK0.
    If you use the local secondary index feature, the first primary key column of an index table must be the same as the first primary key column of the corresponding data table.

    -   If you create an index on the Defined0 predefined column and PK1 primary key column, Tablestore generates the index table that contains primary key columns Defined0, PK0, and PK1.
    -   If you create an index on the Defined0 predefined column and PK0 and PK1 primary key columns, Tablestore generates the index table that contains primary key columns Defined0, PK0, and PK1.
    -   If you create an index on the PK0 and PK1 primary key columns, Tablestore generates the index table that contains primary key columns PK0 and PK1.
-   You can specify predefined columns of a data table as attribute columns of an index table based on query modes and costs.

    After you specify a predefined column of a data table as an attribute column of an index table, you can query this index table instead of the data table for the column value. However, this increases storage costs. If the predefined column of the data table is not specified as an attribute column of the index table, you must use the index table to query the data table.

-   If you use the global secondary index feature, select an appropriate column as the first primary key column of an index table.
    -   If you specify a column whose values are dates or time as the first primary key column of the index table, the update speed of the index table may decrease. Therefore, we recommend that you do not set the column to the first primary key column of an index table.

        We recommend that you hash columns whose values are dates or time and then create indexes for the hashed columns. If you want to hash and create indexes for columns whose values are time or dates, use DingTalk to contact Tablestore technical support.

    -   We recommend that you do not define a column of low cardinality or a column that contains enumerated values as the first primary key column of an index table. For example, if the gender column is defined as the first primary key column, the horizontal scalability of the index table is limited, which results in poor write performance.

When you use an index table, take note of the following items:

-   You must comply with the following rules when you write data to a data table that is associated with an index table. Otherwise, the data cannot be written to the data table.
    -   You cannot customize version numbers for the data when you write the data to an index table.
    -   An index table cannot contain a row that has the same primary key during a batch write operation.

## Billable items

For more information, see [Billable items of secondary indexes](/intl.en-US/Pricing/Billable items of secondary indexes.md).

