# Local secondary index

After you create index tables for a base table, you can read data from the index tables or delete specified index tables.

## Use Tablestore SDKs

You can use the following Tablestore SDKs to implement the local secondary index feature:

-   Tablestore SDK for Java: [Local secondary index](/intl.en-US/SDK Reference/Java SDK/Secondary Index/Local secondary index.md)
-   Tablestore SDK for Go: [Local secondary index](/intl.en-US/SDK Reference/Go SDK/Secondary Index/Local secondary index.md)
-   Tablestore SDK for Python: [Local secondary index](/intl.en-US/SDK Reference/Python SDK/Secondary Index/Local secondary index.md)
-   Tablestore SDK for Node.js: [Local secondary index](/intl.en-US/SDK Reference/Node.js SDK/Secondary Index/Local secondary index.md)

## Create an index table by calling the CreateIndex operation

You can call the CreateIndex operation to create an index table for an existing base table.

**Note:** You can also create one or more index tables when you create a base table by calling the CreateTable operation. For more information, see [Create data tables](/intl.en-US/SDK Reference/Java SDK/Table/Create data tables.md).

-   Parameter

    |Parameter|Description|
    |---------|-----------|
    |mainTableName|The name of the base table.|
    |indexMeta|The schema information of the index table, which includes the following items:    -   IndexName: the name of the index table.
    -   primaryKey: the primary key of the index table, which is a combination of any primary key columns and predefined columns of the base table.
    -   definedColumns: the indexed attribute column, which is a combination of predefined columns of the base table.
    -   indexType: the type of the index table. Only IT\_GLOBAL\_INDEX is supported.
    -   indexUpdateMode: the update mode of the index table. Only IUM\_ASYNC\_INDEX is supported. |
    |includeBaseData|Specifies whether the index table includes the existing data in the base table.If the last parameter includeBaseData in CreateIndexRequest is set to true, the existing data of the base table is included in the index table. If includeBaseData is set to false, the existing data is not included. |

-   Examples

    ```
    private static void createIndex(SyncClient client) {
        IndexMeta indexMeta = new IndexMeta(INDEX_NAME); // Specify the name of the index table. 
        indexMeta.setIndexType(IT_LOCAL_INDEX);     // Set the index type to IT_LOCAL_INDEX, which specifies a local secondary index. 
        indexMeta.setIndexUpdateMode(IUM_SYNC_INDEX);  // Set the index update type to IUM_SYNC_INDEX, which specifies the synchronous update mode. If the index update type is set to IT_LOCAL_INDEX, the index update mode must be set to IUM_SYNC_INDEX. 
      
        indexMeta.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1); // Add a primary key column to the index table. The first primary key column of the index table must be the same as the first primary key column of the data table. 
        indexMeta.addPrimaryKeyColumn(DEFINED_COL_NAME_2); // Set DEFINED_COL_NAME_2 to the second primary key column of the index table. 
        indexMeta.addPrimaryKeyColumn(DEFINED_COL_NAME_1); // Set DEFINED_COL_NAME_1 to the third primary key column of the index table. 
        //CreateIndexRequest request = new CreateIndexRequest(TABLE_NAME, indexMeta, true); // Create the index table for the data table. Specify that the index table contains the existing data from the data table. 
        CreateIndexRequest request = new CreateIndexRequest(TABLE_NAME, indexMeta, false); // Create the index table for the data table. Specify that the index table does not contain the existing data from the data table. 
        /**You can set the IncludeBaseData parameter to true to synchronize existing data from the data table to the index table after the index table is created. Then, you can query all data from the index table. 
           The amount of time required to synchronize data to the index table varies based on the amount of data in the data table. 
        */
        //request.setIncludeBaseData(true);
        client.createIndex(request); // Create the index table. 
    }
    ```


## Read data from an index table

-   Read data within a specified range from the index table

    For more information, see [Multi-row operations](/intl.en-US/SDK Reference/Java SDK/Multi-row operations.md).

    -   Examples

        If an index table contains the columns to return, you can query the required data from the index table.

        ```
        private static void scanFromIndex(SyncClient client) {
            RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX_NAME); // Specify the index table name. 
        
            // Specify the start primary key. 
            PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN); // Specify the minimum value of PRIMARY_KEY_NAME_1. 
            startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.INF_MIN); // Specify the minimum value of DEFINED_COL_NAME_1. 
            startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MIN); // Specify the minimum value of PRIMARY_KEY_NAME_2. 
            rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
        
            // Specify the end primary key. 
            PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX); // Specify the maximum value of PRIMARY_KEY_NAME_1. 
            endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.INF_MAX); // Specify the maximum value of DEFINED_COL_NAME_1. 
            endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MAX); // Specify the maximum value of PRIMARY_KEY_NAME_2. 
            rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
        
            rangeRowQueryCriteria.setMaxVersions(1);
        
            System.out.println("Results returned from the index table:");
            while (true) {
                GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
                for (Row row : getRangeResponse.getRows()) {
                    System.out.println(row);
                }
        
                // If the nextStartPrimaryKey value is not null, continue to read data. 
                if (getRangeResponse.getNextStartPrimaryKey() != null) {
                    rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
                } else {
                    break;
                }
            }
        }
        ```

        If the index table does not contain the columns to return, you must query the required data from the data table.

        ```
        private static void scanFromIndex(SyncClient client) {
            RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX_NAME); // Specify the index table name. 
        
            // Specify the start primary key. 
            PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN); // Specify the minimum value of PRIMARY_KEY_NAME_1. 
            startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.INF_MIN); // Specify the minimum value of DEFINED_COL_NAME_1. 
            startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MIN); // Specify the minimum value of PRIMARY_KEY_NAME_2. 
            rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
        
            // Specify the end primary key. 
            PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX); // Specify the maximum value of PRIMARY_KEY_NAME_1. 
            endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.INF_MAX); // Specify the maximum value of DEFINED_COL_NAME_1. 
            endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MAX); // Specify the maximum value of PRIMARY_KEY_NAME_2. 
            rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
        
            rangeRowQueryCriteria.setMaxVersions(1);
        
            while (true) {
                GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
                for (Row row : getRangeResponse.getRows()) {
                    PrimaryKey curIndexPrimaryKey = row.getPrimaryKey();
                    PrimaryKeyColumn pk1 = curIndexPrimaryKey.getPrimaryKeyColumn(PRIMARY_KEY_NAME_1);
                    PrimaryKeyColumn pk2 = curIndexPrimaryKey.getPrimaryKeyColumn(PRIMARY_KEY_NAME_2);
                    PrimaryKeyBuilder mainTablePKBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
                    mainTablePKBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, pk1.getValue());
                    mainTablePKBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, ke2.getValue());
                    PrimaryKey mainTablePK = mainTablePKBuilder.build(); // Specify primary key columns for the data table based on the primary key columns of the index table. 
        
                    // Query the data table. 
                    SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(TABLE_NAME, mainTablePK);
                    criteria.addColumnsToGet(DEFINED_COL_NAME3); // Read the DEFINED_COL_NAME3 attribute column from the data table.
                    // Set MaxVersions to 1 to read the latest version of data. 
                    criteria.setMaxVersions(1);
                    GetRowResponse getRowResponse = client.getRow(new GetRowRequest(criteria));
                    Row mainTableRow = getRowResponse.getRow();
                    System.out.println(row); 
                }
        
                // If the nextStartPrimaryKey value is not null, continue to read data. 
                if (getRangeResponse.getNextStartPrimaryKey() != null) {
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


