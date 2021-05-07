# Global secondary index

After you create index tables for a base table, you can read data from the index tables or delete specified index tables.

## Prerequisites

-   Client is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialization.md).
-   A data table is created. The value of timeToLive is -1. The value of maxVersions is 1.
-   Predefined columns are configured for the data table.

## Create an index table by calling the CreateIndex operation

You can call the CreateIndex operation to create an index table for an existing base table.

**Note:** You can also create one or more index tables when you create a base table by calling the CreateTable operation. For more information, see [Create tables](/intl.en-US/SDK Reference/Java SDK/Table/Create tables.md).

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |mainTableName|The name of the base table.|
    |indexMeta|The schema information of the index table, which includes the following items:    -   IndexName: the name of the index table.
    -   primaryKey: the primary key of the index table, which is a combination of any primary key columns and predefined columns of the base table.
    -   definedColumns: the indexed attribute column, which is a combination of predefined columns of the base table.
    -   indexType: the type of the index table. Only IT\_GLOBAL\_INDEX is supported.
    -   indexUpdateMode: the update mode of the index table. Only IUM\_ASYNC\_INDEX is supported. |
    |includeBaseData|Specifies whether the index table includes the existing data in the base table.If the last parameter includeBaseData in CreateIndexRequest is set to true, the existing data of the base table is included in the index table. If includeBaseData is set to false, the existing data is not included. |

-   Sample code

    ```
    private static void createIndex(SyncClient client) {
        IndexMeta indexMeta = new IndexMeta(INDEX2_NAME); // Specify the name of the index table.
        indexMeta.addPrimaryKeyColumn(DEFINED_COL_NAME_1); // Specify DEFINED_COL_NAME_1 as the first primary key column of the index table.
        indexMeta.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2); //Specify PRIMARY_KEY_NAME_2 as the second primary key column of the index table.
        indexMeta.addDefinedColumn(DEFINED_COL_NAME_2); // Specify DEFINED_COL_NAME_2 as an attribute column of the index table.
        CreateIndexRequest request = new CreateIndexRequest(TABLE_NAME, indexMeta, true); // Create the index table for the base table. Specify to include the existing data of the base table in the index table.
        CreateIndexRequest request = new CreateIndexRequest(TABLE_NAME, indexMeta, false); // Create the index table for the base table. Specify to exclude the existing data of the base table in the index table.
        /**You can set the IncludeBaseData parameter to true to enable the synchronization of existing data in the base table after an index table is created. You can then query all data from the index table.
           The amount of time required to synchronize data to the index table is determined by the amount of data in the base table.
        */
        request.setIncludeBaseData(true);
        client.createIndex(request); // Create the index table.
    }
    ```


## Delete the index table by calling the DeleteIndex operation

You can call the DeleteIndex operation to delete a specified index table from a base table.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |mainTableName|The name of the base table.|
    |indexName|The name of the index table.|

-   Sample code

    ```
    private static void deleteIndex(SyncClient client) {
        DeleteIndexRequest request = new DeleteIndexRequest(TABLE_NAME, INDEX_NAME); // Specify the name of the index table that you want to delete and the name of the corresponding base table.
        client.deleteIndex(request); // Delete the index table.
    }
    ```


