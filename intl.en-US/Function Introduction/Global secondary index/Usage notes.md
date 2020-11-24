# Usage notes

This topic describes the terms and precautions for global secondary indexes.

## Terms

|Term|Description|
|:---|:----------|
|index table|The table created based on indexing of columns from the data table.Data in the index table is read-only. |
|predefined column|The non-primary key column predefined as an attribute column when the table was created. You can also specify the data type of the column.**Note:** Tablestore uses a schema-free model. You can write an unlimited number of attribute columns to a row and do not need to specify a number of attribute columns in a schema. |
|single-column index|The index that is created for a single column.|
|compound index|The index that is created for multiple columns in a table. A compound index can have indexed columns 1 and 2.|
|indexed attribute column|The predefined column that is mapped to an index table.|
|autocomplete|Tablestore automatically adds all primary key columns of the data table to the index table.|

## Precautions

When you create an index table, take note of the following items:

-   You can create a maximum of 64 tables in an instance, including data tables and index tables. The index table names must be unique within an instance.
-   You can create a maximum of 15 predefined columns for a data table. If the limit is exceeded, the data table fails to be created.
-   You can create a maximum of five index tables for a data table. If the limit is exceeded, the index table fails to be created.
-   You cannot create an index table for a data table that has the TTL parameter set. If you want to create index tables for a data table that has the TTL parameter set, use DingTalk to contact Tablestore technical support.
-   You cannot create an index table for a data table that has the max versions parameter set.

    If a data table has the max versions parameter set, index tables fail to be created. You cannot set the max versions parameter for a data table that is associated with an index table.

-   An index table can contain a maximum of four indexed columns and eight attribute columns. If the limit is exceeded, the index table fails to be created.

When you configure the indexed columns and attribute columns for an index table, take note of the following items:

-   An indexed column is a combination of primary key columns and predefined columns of the data table. You can set the data type of an indexed column to INTEGER, STRING, or BINARY. The limits on indexed columns are the same as those on primary key columns of the data table.
-   An indexed attribute column is a combination of predefined columns of the data table. You can set the data type of an indexed attribute column to STRING or BINARY. The limits on indexed attribute columns are the same as those on attribute columns of the data table.
-   Tablestore automatically adds all primary key columns of the data table to the index table. When you scan an index table, you must specify the range of primary key columns. The range can be anywhere from negative infinity to positive infinity.

    When you create an index table, you need to specify only the column that you want to index. Tablestore adds the other primary key columns of the data table to the index table. For example, a data table contains the primary key columns PK0 and PK1 and a predefined column Defined0.

    -   When you create an index for the Defined0 column, Tablestore generates an index table that has the primary key columns Defined0, PK0, and PK1.
    -   When you create an index for the Defined0 and PK1 columns, Tablestore generates an index table that has the primary key columns Defined0, PK1, and PK0.
    -   When you create an index for the PK1 column, Tablestore generates an index table that has the primary key columns PK1 and PK0.
-   You can specify appropriate predefined columns of the data table as attribute columns of the index table based on the query mode and costs.

    When you specify a predefined column of the data table as an attribute column of the index table, you can search this index table instead of the data table for the column value. However, this increases storage costs. Otherwise, you must query the data table based on the index table.

-   If an index table contains multiple columns, the size limit on the columns is the same as that on primary key columns of the data table.
-   You cannot specify an auto-increment primary key column as the first primary key column of an index table.
-   We recommend that you do not specify a column whose values are date or time as the first primary key column of an index table because it may slow down index table updates.

    We recommend that you hash columns related to the time or date and create indexes for the hashed columns. If you have similar requirements, use DingTalk to contact Tablestore technical support.

-   We recommend that you do not define a column of low cardinality or a column that contains enumerated values as the first primary key column of an index table. For example, if the gender column is defined as the first primary column, the horizontal scalability of the index table is restricted and poor write performance is caused.

When you use an index table, take note of the following items:

-   You must comply with the following rules when you write data to a data table that is associated with an index table. Otherwise, the data cannot be written to the data table.
    -   You cannot customize versions when you write data to an indexed data table.
    -   An indexed data table cannot contain repeated rows that have the same primary key during the same batch write operation.
-   When you query an index table, the associated data table is not queried. You need to manually query the data table.
-   You cannot use the Stream feature in an index table.

