# Local secondary index

After you create an index table for a data table, you can read data from the index table or delete the specified index table.

## Prerequisites

-   Client is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Node.js SDK/Initialization.md).
-   A data table is created. The value of timeToLive is -1. The value of maxVersions is 1.
-   Predefined columns are configured for the data table.

## Create an index table by calling the CreateIndex operation

You can call the CreateIndex operation to create an index table for an existing data table.

**Note:** You can call the CreateTable operation to create one or more index tables when you create a data table. For more information, see [Create data tables](/intl.en-US/SDK Reference/Node.js SDK/Table/Create data tables.md).

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |mainTableName|The name of the data table.|
    |indexMeta|The schema information of the index table. The schema information contains the following items:    -   name: the name of the index table.
    -   primaryKey: the indexed columns of the index table. The indexed columns are a combination of primary key columns and predefined columns of the data table.

If you use the local secondary index feature, the first primary key column of an index table must be the same as the first primary key column of the corresponding data table.

    -   definedColumn: the attribute columns of the index table. The attribute columns are a combination of predefined columns of the data table.
    -   includeBaseData: specifies whether to include the existing data from the data table in the index table.

If includeBaseData parameter is set to true, the index table contains the existing data. If the value is set to false, the index table does not contain the existing data.

    -   indexType: the type of the index. Valid values: IT\_GLOBAL\_INDEX and IT\_LOCAL\_INDEX.
        -   If indexType is not specified or is set to IT\_GLOBAL\_INDEX, the global secondary index feature is used.

If you use the global secondary index feature, Tablestore automatically synchronizes the columns to index and data in primary key columns from a data table to an index table in asynchronous mode. The synchronization latency is within a few milliseconds.

        -   If indexType is set to IT\_LOCAL\_INDEX, the local secondary index feature is used.

If you use the local secondary index feature, Tablestore automatically synchronizes data from the indexed columns and the primary key columns of a data table to the columns of an index table in synchronous mode. After the data is written to the data table, you can query the data from the index table.

    -   indexUpdateMode: the update mode of the index. Valid values: IUM\_ASYNC\_INDEX and IUM\_SYNC\_INDEX.
        -   If indexUpdateMode is not specified or is set to IUM\_ASYNC\_INDEX, the asynchronous mode is used to update the global secondary index.

If you use the global secondary index feature, you must set the index update mode to IUM\_ASYNC\_INDEX.

        -   If you set indexUpdateMode to IUM\_SYNC\_INDEX, the synchronous update mode is used.

If you use the local secondary index feature, you must set the index update mode to IUM\_SYNC\_INDEX. |

-   Examples

    ```
    var client = require('./client');
    var TableStore = require('../index.js');
    
    client.createIndex({
        mainTableName: "sdkLocalIndexTest",
        indexMeta: {
            name: "sdkLocalIndex",
            primaryKey: ["pk1","col1"],// Add primary key columns to the index table. The first primary key column of the index table must be the same as the first primary key column of the data table. 
            definedColumn: ["col2"],
            includeBaseData: false,
            indexUpdateMode: TableStore.IndexUpdateMode.IUM_SYNC_INDEX,// Set the index update mode to IUM_SYNC_INDEX, which specifies the synchronous update mode. If the index type is set to IT_LOCAL_INDEX, the index update mode must be set to IUM_SYNC_INDEX. 
            indexType: TableStore.IndexType.IT_LOCAL_INDEX,// Set the index type to IT_LOCAL_INDEX, which specifies a local secondary index. 
        }
    }, function (err, data) {
        if (err) {
            console.log('error:', err);
            return;
        }
        console.log('success:', JSON.stringify(data, null, 2));
    });
    ```


## Read data from an index table

You can read a row of data or read data within a specified range from the index table. If the index table contains the attribute columns to return, you can query the data from the index table. If the index table does not contain the columns to return, you must query the required data from the data table.

-   Read a row of data from an index table

    For more information, see [Single-row operations](/intl.en-US/SDK Reference/Node.js SDK/Single-row operations.md).

    -   Parameters

        When you call the GetRow operation to read data from an index table, take note of the following items:

        -   You must set tableName to the name of the index table.
        -   Tablestore automatically adds the primary key columns that are not specified as indexed columns to an index table. Therefore, when you specify the primary key columns of a row, you must specify the indexed columns based on which you create the index table and the primary key columns of the data table.
    -   Examples

        ```
        var TableStore = require('../index.js');
        var Long = TableStore.Long;
        var client = require('./client');
        
        var params = {
          tableName: "sdkLocalIndex",
          primaryKey: [ {'pk1': Long.fromNumber(1)},{'col1': Long.fromNumber(2)}, {'pk2': Long.fromNumber(2)}]
        };
        
        client.getRow(params, function (err, data) {
          if (err) {
            console.log('error:', err);
            return;
          }
          console.log('success:', JSON.stringify(data, null, 2));
        });
        ```

-   Read data within a specified range from an index table

    For more information, see [Multi-row operations](/intl.en-US/SDK Reference/Node.js SDK/Multi-row operations.md).

    -   Parameters

        When you call the GetRange operation to read data from an index table, take note of the following items:

        -   You must set tableName to the name of the index table.
        -   Tablestore automatically adds the primary key columns that are not specified as indexed columns to an index table. Therefore, when you specify the start primary key and end primary key, you must specify the indexed columns based on which you create the index table and the primary key columns of the data table.
    -   Examples

        ```
        var TableStore = require('../index.js');
        var Long = TableStore.Long;
        var client = require('./client');
        
        var params = {
          tableName: "sdkLocalIndex", 
          direction: TableStore.Direction.FORWARD,
          maxVersions: 10,
          inclusiveStartPrimaryKey: [{ "pk1": TableStore.INF_MIN }, { "col1": TableStore.INF_MIN }, { "pk2": TableStore.INF_MIN }],
          exclusiveEndPrimaryKey: [{ "pk1": TableStore.INF_MIN }, { "col1": TableStore.INF_MIN }, { "pk2": TableStore.INF_MAX }],
          limit: 2
        };
        
        var resultRows = []
        
        var getRange = function () {
          client.getRange(params, function (err, data) {
            if (err) {
              console.log('error:', err);
              return;
            }
            resultRows = resultRows.concat(data.rows)
        
            // If data.next_start_primary_key is not empty, the system continues to read data. 
            if (data.nextStartPrimaryKey) {
              params.inclusiveStartPrimaryKey = [
                { "pk1": data.nextStartPrimaryKey[0].value },
                { "col1": data.nextStartPrimaryKey[1].value }
                { "pk2": data.nextStartPrimaryKey[2].value }
              ];
              getRange()
            } else {
              console.log(JSON.stringify(resultRows));
            }
          });
        }
        
        getRange()
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
    var client = require('./client');
    
    client.dropIndex({
      mainTableName: "sdkGlobalTest",
      indexName: "sdkIndex1"
    }, function (err, data) {
      if (err) {
        console.log('error:', err);
        return;
      }
      console.log('success:', JSON.stringify(data, null, 2));
    });
    ```


