# Global secondary index

After you create index tables for a base table, you can read data from the index tables or delete specified index tables.

## Prerequisites

-   Client is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Node.js SDK/Initialization.md).
-   A table is created. The value of timeToLive is -1. The value of maxVersions is 1.
-   Predefined columns are set for the table.

## Create an index table by calling the CreateIndex operation

You can call the CreateIndex operation to create an index table for an existing base table.

**Note:** You can also create one or more index tables when you create a base table by calling the CreateTable operation. For more information, see [Create tables](/intl.en-US/SDK Reference/Node.js SDK/Table/Create tables.md).

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |mainTableName|The name of the base table.|
    |indexMeta|The schema information of the index table, which includes the following items:    -   name: the name of the index table.
    -   primaryKey: the primary key of the index table, which is a combination of primary key columns and predefined columns of the base table.
    -   definedColumn: the indexed attribute column, which is a combination of predefined columns of the base table.
    -   includeBaseData: specifies whether the index table includes the existing data in the base table.

When the includeBaseData parameter in CreateIndex is set to true, the index table includes the existing data. When the value is set to false, the index table excludes the existing data.

    -   indexUpdateMode: the update mode of the index table. Only IUM\_ASYNC\_INDEX is supported.
    -   indexType: the type of the index table. Only IT\_GLOBAL\_INDEX is supported. |

-   Examples

    ```
    var client = require('./client');
    var TableStore = require('../index.js');
    
    client.createIndex({
        mainTableName: "sdkGlobalTest",
        indexMeta: {
            name: "sdkIndex2",
            primaryKey: ["col1"],
            definedColumn: ["col2"],
            includeBaseData: false,
            indexUpdateMode: TableStore.IndexUpdateMode.IUM_ASYNC_INDEX,// By default, data is synchronized in incremental mode.
            indexType: TableStore.IndexType.IT_GLOBAL_INDEX,// Default value: IT_GLOBAL_INDEX.
        }
    }, function (err, data) {
        if (err) {
            console.log('error:', err);
            return;
        }
        console.log('success:', JSON.stringify(data, null, 2));
    });
    ```


## Read data from the index table

You can read a row of data or read data within a specified range from the index table. If the columns to be returned are included in the index table, you can query the data from the index table. Otherwise, you must query the base table for the required data.

-   Read a row of data from the index table

    For more information, see [Single-row operations](/intl.en-US/SDK Reference/Node.js SDK/Single-row operations.md).

    -   Parameters

        When you use the GetRow operation to read data from the index table, take note of the following items:

        -   You must set tableName to the name of the index table.
        -   Tablestore autocompletes the primary key for the index table. Therefore, when you specify the primary key of a row, you must specify the indexed columns based on which you create the index table and the autocompleted primary key columns.
    -   Examples

        ```
        var TableStore = require('../index.js');
        var Long = TableStore.Long;
        var client = require('./client');
        
        var params = {
          tableName: "index1",
          primaryKey: [ {'pk2': Long.fromNumber(2)}, {'pk1': Long.fromNumber(1)}]
        };
        
        client.getRow(params, function (err, data) {
          if (err) {
            console.log('error:', err);
            return;
          }
          console.log('success:', JSON.stringify(data, null, 2));
        });
        ```

-   Read data within a specified range from the index table

    For more information, see [Multi-row operations](/intl.en-US/SDK Reference/Node.js SDK/Multi-row operations.md).

    -   Parameters

        When you use the GetRange operation to read data from the index table, take note of the following items:

        -   You must set tableName to the name of the index table.
        -   Tablestore autocompletes the primary key for the index table. Therefore, when you specify the start primary key and the end primary key of the range, you must specify the indexed columns based on which you create the index table and the autocompleted primary key columns.
    -   Examples

        ```
        var TableStore = require('../index.js');
        var Long = TableStore.Long;
        var client = require('./client');
        
        var params = {
          tableName: "sdkIndex1", // Assume that pk2 is the first primary key column of the index table. pk1 is the second primary key column of the index table.
          direction: TableStore.Direction.FORWARD,
          maxVersions: 10,
          inclusiveStartPrimaryKey: [{ "pk2": TableStore.INF_MIN }, { "pk1": TableStore.INF_MIN }],
          exclusiveEndPrimaryKey: [{ "pk2": TableStore.INF_MAX }, { "pk1": TableStore.INF_MAX }],
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
        
            // If data.next_start_primary_key is not empty, continue the read operation.
            if (data.nextStartPrimaryKey) {
              params.inclusiveStartPrimaryKey = [
                { "pk2": data.nextStartPrimaryKey[0].value },
                { "pk1": data.nextStartPrimaryKey[1].value }
              ];
              getRange()
            } else {
              console.log(JSON.stringify(resultRows));
            }
          });
        }
        
        getRange()
        ```


## Delete the index table by calling the DeleteIndex operation

You can call the DeleteIndex operation to delete a specified index table from a base table.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |mainTableName|The name of the base table.|
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


