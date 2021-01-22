# Usage notes

This topic describes the terms, precautions, and features of global secondary index.

## Terms

|Term|Description|
|:---|:----------|
|index table|The table created based on indexed columns from a base table.Data in the index table is read-only. |
|predefined column|The non-primary key column predefined when a base table is created. The non-primary key column is used as the indexed attribute column. You can also specify the data type of the non-primary key column.**Note:** Tablestore uses a schema-free model. Each row can have different attribute columns. You do not need to specify attribute columns in a schema. |
|single-column index|The index that is created for a single column.|
|compound index|The index that is created for multiple columns in a table. A compound index can have indexed columns 1 and 2.|
|indexed attribute column|The predefined column mapped to an index table.|
|autocomplete|Tablestore automatically adds all primary key columns of a base table to the corresponding index table.|

## Description

-   Supports single-column indexes and compound indexes

    You can create an index for one or more columns in a base table.

-   Supports index synchronization

    You can synchronize the columns to be indexed and data in primary key columns from the base table to the index table. The latency of synchronization is within a few milliseconds.

-   Supports covered indexes

    Index tables can contain attribute columns. Predefined columns are created when you create a table. You can create an index table based on a combination of predefined columns and primary key columns of the base table. You can also set predefined columns to attribute columns in the index table or choose not to specify indexed attribute columns.

    If you set one or more predefined columns of a base table to the indexed attribute columns, you can query the index table to obtain the data stored in the corresponding predefined columns without having to query the base table. For example, a base table includes three primary key columns named PK0, PK1, and PK2 and three predefined columns named Defined0, Defined1, and Defined2.

    -   You can create an index table that includes only indexed column PK2 or includes indexed column PK2 and indexed attribute column Defined0.
    -   You can create an index table that includes only indexed columns PK1 and PK2 or includes indexed columns PK1 and PK2 and indexed attribute column Defined0.
    -   You can create an index table that includes indexed columns PK2, PK1, and PK0, and indexed attribute columns Defined0, Defined1, and Defined2.
    -   You can create an index table that includes only indexed column Defined0.
    -   You can create an index table that includes indexed columns Defined0 and PK1 and indexed attribute column Defined1.
    -   You can create an index table that includes only indexed columns Defined1 and Defined0, or includes indexed columns Defined1 and Defined0 and indexed attribute column Defined2.
-   Supports indexes that include existing data from the base table

    You can create an index table that includes existing data from the base table.

-   Supports sparse indexes

    You can set a predefined column to an indexed attribute column. A row is indexed when all indexed columns exist even if the indexed attribute column of the row does not exist in the base table. However, a row is not indexed when the row excludes one or more indexed columns. For example, a base table includes the primary key columns PK0, PK1, and PK2 and the predefined columns Defined0, Defined1, and Defined2. You can create an index table that includes primary key columns Defined0 and Defined1 and indexed attribute column Defined2.

    -   The index table includes a row in the base table that excludes the Defined2 and includes Defined0 and Defined1.
    -   The index table excludes a row in the base table that includes Defined0 and Defined2 but excludes Defined1.

## Limits

For more information, see [Global secondary index](/intl.en-US/Function Introduction/Limits/Global secondary index.md).

## Precautions

When you configure the indexed columns and attribute columns for an index table, take note of the following items:

-   Tablestore automatically adds all primary key columns of a base table to the index table. When you scan an index table, you must specify the range of primary key columns. The range can be anywhere from negative infinity to positive infinity.

    When you create an index table, you need only to specify the columns that you want to index. Tablestore automatically adds all primary key columns of a base table to the index table. For example, a base table contains primary key columns PK0 and PK1 and predefined column Defined0.

    -   When you create an index table for the Defined0 column, Tablestore generates the index table that contains primary key columns Defined0, PK0, and PK1.
    -   When you create an index table for the Defined0 and PK1 columns, Tablestore generates the index table that contains primary key columns Defined0, PK0, and PK1.
    -   When you create an index table for the PK1 column, Tablestore generates the index table that contains primary key columns PK1 and PK0.
-   You can set predefined columns of the base table to attribute columns of the index table based on query modes and costs.

    When you set a predefined column of a base table to an attribute column of an index table, you can search this index table instead of the base table for the column value. However, this increases storage costs. Otherwise, you must query the base table based on the index table.

-   An index table may be slow to update if you set a column whose values are date or time to the first primary key column of the index table. Therefore, we recommend that you do not set the column to the first primary key column of an index table.

    We recommend that you hash column values related to the time or date and create indexes for the columns. If you have similar requirements, use DingTalk to contact Tablestore technical support.

-   We recommend that you do not define a column of low cardinality or a column that contains enumerated values as the first primary key column of an index table. For example, if the gender column is defined as the first primary key column, the horizontal scalability of the index table is restricted, which results in poor write performance.

When you use an index table, take note of the following items:

-   You must comply with the following rules when you write data to a base table that is associated with an index table. Otherwise, the data cannot be written to the base table.
    -   You cannot customize versions when you write data to an index table.
    -   An index table cannot contain rows that have the same primary key during a batch write operation.

