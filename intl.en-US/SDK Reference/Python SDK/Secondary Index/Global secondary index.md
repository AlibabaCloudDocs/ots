# Global secondary index

After you create index tables for a base table, you can read data from the index tables or delete specified index tables.

## Prerequisites

-   Client is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A table is created. The value of time\_to\_live is -1. The value of max\_versions is 1.
-   Predefined columns are set for the table.

## Create an index table by calling the CreateIndex operation

You can call the CreateIndex operation to create an index table for an existing base table.

**Note:** You can also create one or more index tables when you create a base table by calling the CreateTable operation. For more information, see [Create tables](/intl.en-US/SDK Reference/Python SDK/Table/Create tables.md).

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |main\_table\_name|The name of the base table.|
    |index\_meta|The schema information of the index table, which includes the following items:    -   index\_name: the name of the index table.
    -   primary\_key\_names: the indexed columns of the index table, which is a combination of primary key columns and predefined columns of the base table.
    -   defined\_column\_names: the indexed attribute column, which is a combination of predefined columns of the base table.
    -   index\_type: the type of the index table. Only IT\_GLOBAL\_INDEX is supported. |
    |include\_base\_data|Specifies whether the index table includes the existing data in the base table.When the include\_base\_data parameter in CreateIndex is set to true, the index table includes the existing data. When the value is set to false, the index table excludes the existing data. |

-   Examples

    ```
    index_meta = SecondaryIndexMeta('index2', ['i', 's'], ['bool', 'b', 'd'])
    client.create_secondary_index(table_name, index_meta)
    ```


## Read data from the index table

You can read a row of data or read data within a specified range from the index table. If the columns to be returned are included in the index table, you can query the data from the index table. Otherwise, you must query the base table for the required data.

-   Read a row of data from the index table

    For more information, see [Single-row operations](/intl.en-US/SDK Reference/Python SDK/Single-row operations.md).

    When you use the GetRow operation to read data from the index table, take note of the following items:

    -   You must set table\_name to the name of the index table.
    -   Tablestore autocompletes the primary key for the index table. Therefore, when you specify the primary key of a row, you must specify the indexed columns based on which you create the index table and the autocompleted primary key columns.
-   Read data within a specified range from the index table

    For more information, see [Multi-row operations](/intl.en-US/SDK Reference/Python SDK/Multi-row operations.md).

    When you use the GetRange operation to read data from the index table, take note of the following items:

    -   You must set table\_name to the name of the index table.
    -   Tablestore autocompletes the primary key for the index table. Therefore, when you specify the start primary key and the end primary key of the range, you must specify the indexed columns based on which you create the index table and the autocompleted primary key columns.

## Delete the index table by calling the DeleteIndex operation

You can call the DeleteIndex operation to delete a specified index table from a base table.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |main\_table\_name|The name of the base table.|
    |index\_name|The name of the index table.|

-   Examples

    ```
    client.delete_secondary_index(table_name, 'index1')
    ```


