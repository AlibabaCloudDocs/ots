# Global secondary index

After you create index tables for a base table, you can read data from the index tables or delete specified index tables.

## Prerequisites

-   Client is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialization.md).
-   A table is created. The value of timeToLive is -1. The value of maxVersions is 1.
-   Predefined columns are set for the table.

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


## Read data from the index table

You can read a row of data or read data within a specified range from the index table. If the columns to return are included in the index table, you can query the data from the index table. Otherwise, you must query the base table for the required data.

-   Read a row of data from the index table

    For more information, see [Single-row operations](/intl.en-US/SDK Reference/Java SDK/Single-row operations.md).

    When you use the GetRow operation to read data from the index table, take note of the following items:

    -   You must set tableName to the name of the index table.
    -   Tablestore autocompletes the primary key for the index table. Therefore, when you specify the primary key of a row, you must specify the columns based on which you create the index table and the autocompleted primary key columns.
-   Read data within a specified range from the index table

    For more information, see [Multi-row operations](/intl.en-US/SDK Reference/Java SDK/Multi-row operations.md).

    -   Parameters

        When you use the GetRange operation to read data from the index table, take note of the following items:

        -   You must set tableName to the name of the index table.
        -   Tablestore autocompletes the primary key for the index table. Therefore, when you specify the start primary key and the end primary key of the range, you must specify the columns based on which you create the index table and the autocompleted primary key columns.
    -   Sample code

        If the columns to return are included in the index table, you can query the data from the index table.

        ```
        private static void scanFromIndex(SyncClient client) {
            RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX_NAME); // Specify the name of the index table.
        
            // Specify the primary key to start from.
            PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.INF_MIN); // Specify the minimum value of DEFINED_COL_NAME_1.
            startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN); // Specify the minimum value of PRIMARY_KEY_NAME_1.
            startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MIN); // Specify the minimum value of PRIMARY_KEY_NAME_2.
            rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
        
            // Specify the primary key to end with.
            PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.INF_MAX); // Specify the maximum value of DEFINED_COL_NAME_1.
            endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX); // Specify the maximum value of PRIMARY_KEY_NAME_1.
            endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MAX); // Specify the maximum value of PRIMARY_KEY_NAME_2.
            rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
        
            rangeRowQueryCriteria.setMaxVersions(1);
        
            System.out.println("The following results are returned from the index table:");
            while (true) {
                GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
                for (Row row : getRangeResponse.getRows()) {
                    System.out.println(row);
                }
        
                // If the nextStartPrimaryKey value is not null, continue the read operation.
                if (getRangeResponse.getNextStartPrimaryKey() ! = null) {
                    rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
                } else {
                    break;
                }
            }
        }
        ```

        If the columns to return are not included in the index table, you must query the base table for the required data.

        ```
        private static void scanFromIndex(SyncClient client) {
            RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX_NAME); // Specify the name of the index table.
        
            // Specify the primary key to start from.
            PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.INF_MIN); // Specify the minimum value of DEFINED_COL_NAME_1.
            startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN); // Specify the minimum value of PRIMARY_KEY_NAME_1.
            startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MIN); // Specify the minimum value of PRIMARY_KEY_NAME_2.
            rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
        
            // Specify the primary key to end with.
            PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.INF_MAX); // Specify the maximum value of DEFINED_COL_NAME_1.
            endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX); // Specify the maximum value of PRIMARY_KEY_NAME_1.
            endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MAX); // Specify the maximum value of PRIMARY_KEY_NAME_2.
            rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
        
            rangeRowQueryCriteria.setMaxVersions(1);
        
            while (true) {
                GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
                for (Row row : getRangeResponse.getRows()) {
                    PrimaryKey curIndexPrimaryKey = row.getPrimaryKey();
                    PrimaryKeyColumn pk1 = curIndexPrimaryKey.getPrimaryKeyColumn(PRIMARY_KEY_NAME1);
                    PrimaryKeyColumn pk2 = curIndexPrimaryKey.getPrimaryKeyColumn(PRIMARY_KEY_NAME2);
                    PrimaryKeyBuilder mainTablePKBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
                    mainTablePKBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME1, pk1.getValue());
                    mainTablePKBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME2, ke2.getValue());
                    PrimaryKey mainTablePK = mainTablePKBuilder.build(); // Specify primary keys for the base table based on the primary keys of the index table.
        
                    // Query the base table.
                    SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(TABLE_NAME, mainTablePK);
                    criteria.addColumnsToGet(DEFINED_COL_NAME3); // Specify to read the DEFINED_COL_NAME3 attribute column from the base table.
                    // Set MaxVersions to 1 to read the latest version of data.
                    criteria.setMaxVersions(1);
                    GetRowResponse getRowResponse = client.getRow(new GetRowRequest(criteria));
                    Row mainTableRow = getRowResponse.getRow();
                    System.out.println(row); 
                }
        
                // If the nextStartPrimaryKey value is not null, continue the read operation.
                if (getRangeResponse.getNextStartPrimaryKey() ! = null) {
                    rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
                } else {
                    break;
                }
            }
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


