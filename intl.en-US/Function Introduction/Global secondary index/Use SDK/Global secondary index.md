# Global secondary index

After you create an index table for a data table, you can read data from the index table or delete the specified index table.

## Use Tablestore SDKs

You can use the following Tablestore SDKs to implement global secondary index:

-   Tablestore SDK for Java: [Global secondary index](/intl.en-US/SDK Reference/Java SDK/Secondary Index/Global secondary index.md)
-   Tablestore SDK for Go: [Global secondary index](/intl.en-US/SDK Reference/Go SDK/Secondary Index/Global secondary index.md)
-   Tablestore SDK for Python: [Global secondary index](/intl.en-US/SDK Reference/Python SDK/Secondary Index/Global secondary index.md)
-   Tablestore SDK for Node.js: [Global secondary index](/intl.en-US/SDK Reference/Node.js SDK/Secondary Index/Global secondary index.md)
-   [.Tablestore SDK for .NET: Global secondary index](/intl.en-US/SDK Reference/.NET SDK/Global secondary index.md)
-   Tablestore SDK for PHP: [Global secondary index](/intl.en-US/SDK Reference/PHP SDK/Global secondary index.md)

## Create an index table by calling the CreateIndex operation

You can call the CreateIndex operation to create an index table on an existing data table.

**Note:** You can create one or more index tables when you create a data table by calling the CreateTable operation. For more information, see [Create data tables](/intl.en-US/SDK Reference/Java SDK/Table/Create data tables.md).

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |mainTableName|The name of the data table.|
    |indexMeta|The schema information of the index table. The schema information contains the following items:    -   indexName: the name of the index table.
    -   primaryKey: the indexed columns of the index table. The indexed columns are a combination of primary key columns and predefined columns of the data table.

If you use the local secondary index feature, the first primary key column of an index table must be the same as the first primary key column of the corresponding data table.

    -   definedColumns: the indexed attribute columns. The attribute columns are a combination of predefined columns of the data table.
    -   indexType: the type of the index. Valid values: IT\_GLOBAL\_INDEX and IT\_LOCAL\_INDEX.
        -   If indexType is not specified or is set to IT\_GLOBAL\_INDEX, the global secondary index feature is used.

If you use the global secondary index feature, Tablestore automatically synchronizes the columns to be indexed and data in primary key columns from a data table to an index table in asynchronous mode. The synchronization latency is within a few milliseconds.

        -   If indexType is set to IT\_LOCAL\_INDEX, the local secondary index feature is used.

If you use the local secondary index feature, Tablestore automatically synchronizes data from the indexed columns and the primary key columns of a data table to the columns of an index table in synchronous mode. After the data is written to the data table, you can query the data from the index table.

    -   indexUpdateMode: the update mode of the index. Valid values: IUM\_ASYNC\_INDEX and IUM\_SYNC\_INDEX.
        -   If indexUpdateMode is not specified or is set to IUM\_ASYNC\_INDEX, the asynchronous mode is used to update the index.

If you use the global secondary index feature, you must set the index update mode to IUM\_ASYNC\_INDEX.

        -   If you set indexUpdateMode to IUM\_SYNC\_INDEX, the synchronous update mode is used.

If you use the local secondary index feature, you must set the index update mode to IUM\_SYNC\_INDEX, which indicates the synchronous update mode. |
    |includeBaseData|Specifies whether to include the existing data of the data table in the index table. If the last parameter includeBaseData in CreateIndexRequest is set to true, the existing data of the data table is included in the index table. If includeBaseData is set to false, the existing data is excluded. |

-   Examples

    ```
    private static void createIndex(SyncClient client) {
        IndexMeta indexMeta = new IndexMeta(INDEX2_NAME); // Specify the name of the index table. 
        indexMeta.addPrimaryKeyColumn(DEFINED_COL_NAME_1); // Specify DEFINED_COL_NAME_1 as the first primary key column of the index table. 
        indexMeta.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2); // Specify PRIMARY_KEY_NAME_2 as the second primary key column of the index table. 
        indexMeta.addDefinedColumn(DEFINED_COL_NAME_2); // Specify DEFINED_COL_NAME_2 as an attribute column of the index table. 
        //CreateIndexRequest request = new CreateIndexRequest(TABLE_NAME, indexMeta, true); // Create the index table on the data table. Specify that the index table includes the existing data of the data table. 
        CreateIndexRequest request = new CreateIndexRequest(TABLE_NAME, indexMeta, false); // Create the index table on the data table. Specify that the index table excludes the existing data from the data table. 
        /**You can set the IncludeBaseData parameter to true to synchronize existing data from the data table to the index table after the index table is created. Then, you can query all data from the index table. 
           The amount of time required to synchronize data to the index table is determined by the amount of data in the data table. 
        */
        //request.setIncludeBaseData(true);
        client.createIndex(request); // Create the index table. 
    }
    ```


## Read data from an index table

You can read a row of data or read data within a specified range from the index table. If the index table contains the attribute columns to return, you can query the data from the index table. If the index table does not contain the columns to return, you must query the required data from the data table.

-   Read a row of data from an index table

    For more information, see [Single-row operations](/intl.en-US/SDK Reference/Java SDK/Single-row operations.md).

    When you call the GetRow operation to read data from an index table, take note of the following items:

    -   You must set tableName to the name of the index table.
    -   Tablestore automatically adds the primary key columns that are not specified as indexed columns to an index table. Therefore, when you specify the primary key columns of a row, you must specify the indexed columns based on which you create the index table and the primary key columns of the data table.
-   Read data within a specified range from the index table

    For more information, see [Multi-row operations](/intl.en-US/SDK Reference/Java SDK/Multi-row operations.md).

    -   Parameters

        When you call the GetRange operation to read data from an index table, take note of the following items:

        -   You must set tableName to the name of the index table.
        -   Tablestore automatically adds the primary key columns that are not specified as indexed columns to an index table. Therefore, when you specify the start primary key and end primary key, you must specify the indexed columns based on which you create the index table and the primary key columns of the data table.
    -   Examples

        If an index table contains the columns to return, you can query the required data from the index table.

        ```
        private static void scanFromIndex(SyncClient client) {
            RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX_NAME); // Specify the index table name. 
        
            // Specify the start primary key. 
            PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.INF_MIN); // Specify the minimum value of DEFINED_COL_NAME_1. 
            startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN); // Specify the minimum value of PRIMARY_KEY_NAME_1. 
            startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MIN); // Specify the minimum value of PRIMARY_KEY_NAME_2. 
            rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
        
            // Specify the end primary key. 
            PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.INF_MAX); // Specify the maximum value of DEFINED_COL_NAME_1. 
            endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX); // Specify the maximum value of PRIMARY_KEY_NAME_1. 
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

        If an index table does not contain the columns to return, you must query the required data from the data table.

        ```
        private static void scanFromIndex(SyncClient client) {
            RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX_NAME); // Specify the index table name. 
        
            // Specify the start primary key. 
            PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.INF_MIN); // Specify the minimum value of DEFINED_COL_NAME_1. 
            startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN); // Specify the minimum value of PRIMARY_KEY_NAME_1. 
            startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MIN); // Specify the minimum value of PRIMARY_KEY_NAME_2. 
            rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
        
            // Specify the end primary key. 
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
                    PrimaryKeyColumn pk1 = curIndexPrimaryKey.getPrimaryKeyColumn(PRIMARY_KEY_NAME_1);
                    PrimaryKeyColumn pk2 = curIndexPrimaryKey.getPrimaryKeyColumn(PRIMARY_KEY_NAME_2);
                    PrimaryKeyBuilder mainTablePKBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
                    mainTablePKBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, pk1.getValue());
                    mainTablePKBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, ke2.getValue());
                    PrimaryKey mainTablePK = mainTablePKBuilder.build(); // Specify primary key columns for the data table based on the primary key columns of the index table. 
        
                    // Query the data table. 
                    SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(TABLE_NAME, mainTablePK);
                    criteria.addColumnsToGet(DEFINED_COL_NAME3); // Specify DEFINED_COL_NAME3 to read the DEFINED_COL_NAME3 attribute column from the data table. 
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


