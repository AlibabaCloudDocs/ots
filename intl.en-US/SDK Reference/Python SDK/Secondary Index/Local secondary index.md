# Local secondary index

After you create index tables for a base table, you can read data from the index tables or delete specified index tables.

## Prerequisites

-   Client is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A data table is created. The value of time\_to\_live is -1. The value of max\_versions is 1.
-   Predefined columns are configured for the data table.

## Create an index table by calling the CreateIndex operation

You can call the CreateIndex operation to create an index table for an existing data table.

**Note:** You can call the CreateTable operation to create one or more index tables when you create a data table. For more information, see [Create data tables](/intl.en-US/SDK Reference/Python SDK/Table/Create data tables.md).

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |main\_table\_name|The name of the data table.|
    |index\_meta|The schema information of the index table. The schema information contains the following items:    -   index\_name: the name of the index table.
    -   primary\_key\_names: the indexed columns of the index table. The indexed columns are a combination of primary key columns and predefined columns of the data table.

If you use the local secondary index feature, the first primary key column of an index table must be the same as the first primary key column of the corresponding data table.

    -   defined\_column\_names: the attribute columns of the index table. The attribute columns are a combination of predefined columns of the data table.
    -   index\_type: the type of the index. Valid values: IT\_GLOBAL\_INDEX and IT\_LOCAL\_INDEX.
        -   If index\_type is not specified or is set to IT\_GLOBAL\_INDEX, the global secondary index feature is used.

If you use the global secondary index feature, Tablestore automatically synchronizes the columns to index and data in primary key columns from a data table to an index table in asynchronous mode. The synchronization latency is within a few milliseconds.

        -   If index\_type is set to IT\_LOCAL\_INDEX, the local secondary index feature is used.

If you use the local secondary index feature, Tablestore automatically synchronizes data from the indexed columns and the primary key columns of a data table to the columns of an index table in synchronous mode. After the data is written to the data table, you can query the data from the index table. |
    |include\_base\_data|Specifies whether to include the existing data from the data table in the index table. When the include\_base\_data parameter is set to true, the index table contains the existing data. When the value is set to false, the index table does not contain the existing data. |

-   Examples

    The following code provides an example on how to create an index table. The data table contains the pk1 and pk2 primary key columns. The index table contains the pk1 and definedcol1 primary key columns and the definedcol2 and definedcol3 attribute columns.

    ```
    index_meta = SecondaryIndexMeta('index2', ['pk1', 'definedcol1'], ['definedcol2', 'definedcol3'],index_type= SecondaryIndexType.LOCAL_INDEX)
    client.create_secondary_index(table_name, index_meta)
    ```


## Read data from an index table

You can read a row of data or read data within a specified range from the index table. If the index table contains the attribute columns to return, you can query the data from the index table. If the index table does not contain the columns to be returned, you must query the required data from the data table.

-   Read a row of data from an index table

    For more information, see [Single-row operations](/intl.en-US/SDK Reference/Python SDK/Single-row operations.md).

    When you call the GetRow operation to read data from an index table, take note of the following items:

    -   You must set table\_name to the name of the index table.
    -   Tablestore automatically adds the primary key columns that are not specified as indexed columns to an index table. Therefore, when you specify the primary key columns of a row, you must specify the indexed columns based on which you create the index table and the primary key columns of the data table.
-   Read data within a specified range from an index table

    For more information, see [Multi-row operations](/intl.en-US/SDK Reference/Python SDK/Multi-row operations.md).

    When you call the GetRange operation to read data from an index table, take note of the following items:

    -   You must set table\_name to the name of the index table.
    -   Tablestore automatically adds the primary key columns that are not specified as indexed columns to an index table. Therefore, when you specify the start primary key and end primary key, you must specify the indexed columns based on which you create the index table and the primary key columns of the data table.

## Delete an index table by calling the DeleteIndex operation

You can call the DeleteIndex operation to delete the specified index table from the corresponding data table.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |main\_table\_name|The name of the data table.|
    |index\_name|The name of the index table.|

-   Examples

    ```
    client.delete_secondary_index(table_name, 'index1')
    ```


