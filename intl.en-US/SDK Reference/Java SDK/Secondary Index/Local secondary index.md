# Local secondary index

After you create an index table for a data table, you can read data from the index table or delete the specified index table.

## Prerequisites

-   Client is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialization.md).
-   A data table is created. The value of timeToLive is -1. The value of maxVersions is 1.
-   Predefined columns are configured for the data table.

## Delete an index table by calling the DeleteIndex operation

You can call the DeleteIndex operation to delete the specified index table from the corresponding data table.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |mainTableName|The name of the data table.|
    |indexName|The name of the index table.|

-   Examples

    ```
    private static void deleteIndex(SyncClient client) {
        DeleteIndexRequest request = new DeleteIndexRequest(TABLE_NAME, INDEX_NAME); // Specify the name of the index table that you want to delete and the name of the corresponding data table. 
        client.deleteIndex(request); // Delete the index table. 
    }
    ```


