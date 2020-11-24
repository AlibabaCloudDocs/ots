# Multi-row operations

Tablestore SDKs provide multi-row operations such as BatchWriteRow, BatchGetRow, and GetRange.

## BatchWriteRow

You can call this operation to write several rows of data to one or more tables by sending one request. The BatchWriteRow operation consists of multiple PutRow, UpdateRow, and DeleteRow operations. The process of constructing a suboperation is the same as that of calling the PutRow, UpdateRow, and DeleteRow operations. BatchWriteRow supports conditional updates.

The execution and returning of results of each suboperation are carried out independently.

When multiple rows are written, some rows may fail to be written. If the operation fails for some rows, an exception may not occur, but information about the index and error messages of the failed rows is stored in BatchWriteRowResponse. Therefore, when you call the BatchWriteRow operation, you must check the return values and check whether the status of each row is successful. If you do not check the return values, failures of operations on part of rows are ignored.

When the server detects errors caused by operations, the BatchWriteRow operation may return exceptions of parameter errors. All operations for the request are not executed.

-   Operations

    ```
    /**
     * Modify multiple rows of data.
     */
    batchWriteRow(params, callback)                  
    ```

-   Parameters

    BatchWriteRow is a combination of PutRow, UpdateRow, and DeleteRow.

    -   Hierarchies are created for tables. Multiple tables can be processed at a time.

        The values of the tables parameter are based on tables. This parameter specifies information about tables and rows on which the write, modification, or delete operations are performed. For more information about parameters, see [Single-row operations](/intl.en-US/SDK Reference/Go SDK/Single-row operations.md).

    -   The type parameter is added to distinguish between different operation types.

        You can set the operation type to PUT, UPDATE, or DELETE.

        -   If you set the operation type to PUT or UPDATE, primaryKey and attributeColumns are valid.
        -   If you set the operation type to DELETE, primaryKey is valid.
-   Examples

    The following code provides an example on how to write multiple rows of data:

    ```
    var client = require('./client');
    var TableStore = require('../index.js');
    var Long = TableStore.Long;
    
    var params = {
    
        tables: [
            {
                tableName: 'sampleTable',
                rows: [
                    {
                        type: 'UPDATE',
                        condition: new TableStore.Condition(TableStore.RowExistenceExpectation.IGNORE, null),
                        primaryKey: [{ 'gid': Long.fromNumber(20010) }, { 'uid': Long.fromNumber(20010) }],
                        attributeColumns: [{ 'PUT': [{ 'col1': 'test3' }, { 'col2': 'test4' }] }],
                        returnContent: { returnType: 1 }
                    },
                    {
                        type: 'PUT',
                        condition: new TableStore.Condition(TableStore.RowExistenceExpectation.IGNORE, null),
                        primaryKey: [{ 'gid': Long.fromNumber(20020) }, { 'uid': Long.fromNumber(20020) }],
                        attributeColumns: [{ 'col1': 'test1' }, { 'col2': 'test2' }],
                        returnContent: { returnType: TableStore.ReturnType.Primarykey }
                    },
                    {
                        type: 'DELETE',
                        condition: new TableStore.Condition(TableStore.RowExistenceExpectation.IGNORE, null),
                        primaryKey: [{ 'gid': Long.fromNumber(20018) }, { 'uid': Long.fromNumber(20018) }],
                    }
                ]
            }
        ],
    };
    
    client.batchWriteRow(params, function (err, data) {
    
        if (err) {
            console.log('error:', err);
            return;
        }
    
        console.log('success:', data);
    });
    
                        
    ```

    For the detailed sample code, visit [BatchWriteRow@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/batchWriteRow.js).


## BatchGetRow

You can call this operation to read several rows of data from one or more tables by sending one request. The BatchGetRow operation consists of multiple GetRow operations. The process of constructing a suboperation is the same as that of calling the GetRow operation. BatchGetRow supports filters.

Note that BatchGetRow uses the same parameter configurations for all rows. For example, if ColumnsToGet is set to \[colA\], only the value of colA is read for all the rows.

The execution and returning of results of each suboperation are carried out independently.

When multiple rows are read, part of rows may fail to be read. If the operation fails for part of rows, the system does not return exceptions. However, information about the error messages of the failed rows is stored in BatchGetRowResponse. Therefore, when you call the BatchGetRow operation, you must check the return values and check whether the status of each row is successful.

-   Operations

    ```
    /**
     * Read multiple rows of data from one or more tables.
     */
    batchGetRow(params, callback)                   
    ```

-   Parameters

    Difference between BatchGetRow and GetRow:

    -   Hierarchies are created for tables. Multiple tables can be read at a time.

        The values of the tables parameter are based on tables. This parameter specifies information about tables and rows on which various operations are performed. For more information about parameters, see [Single-row operations](/intl.en-US/SDK Reference/NodeJS SDK/Single-row operations.md).

    -   The primaryKey parameter allows you to set primary keys for multiple rows. You can use the primaryKey parameter to read data from multiple rows at a time.
-   Examples

    The following code provides an example on how to read multiple rows of data from multiple tables, and retry the operation if an error occurs during the read of a single row:

    ```
    var client = require('./client');
    var TableStore = require('../index.js');
    var Long = TableStore.Long;
    
    var params = {
        tables: [{
            tableName: 'sampleTable',
            primaryKey: [
                [{ 'gid': Long.fromNumber(20013) }, { 'uid': Long.fromNumber(20013) }],
                [{ 'gid': Long.fromNumber(20015) }, { 'uid': Long.fromNumber(20015) }]
            ],
            startColumn: "col2",
            endColumn: "col4"
        },
        {
            tableName: 'notExistTable',
            primaryKey: [
                [{ 'gid': Long.fromNumber(10001) }, { 'uid': Long.fromNumber(10001) }]
            ]
        }
        ],
    };
    
    var maxRetryTimes = 3;
    var retryCount = 0;
    
    function batchGetRow(params) {
        client.batchGetRow(params, function (err, data) {
            if (err) {
                console.log('error:', err);
                return;
            }
    
            var isAllSuccess = true;
            var retryRequest = { tables: [] };
            for (var i = 0; i < data.tables.length; i++) {
                var faildRequest = { tableName: data.tables[i][0].tableName, primaryKey: [] };
    
                for (var j = 0; j < data.tables[i].length; j++) {
                    if (! data.tables[i][j].isOk && null ! = data.tables[i][j].primaryKey) {
                        isAllSuccess = false;
                        var pks = [];
                        for (var k in data.tables[i][j].primaryKey) {
                            var name = data.tables[i][j].primaryKey[k].name;
                            var value = data.tables[i][j].primaryKey[k].value;
                            var kp = {};
                            kp[name] = value;
                            pks.push(kp);
                        }
                        faildRequest.primaryKey.push(pks);
    
                    } else {
                        // get success data
                    }
                }
    
                if (faildRequest.primaryKey.length > 0) {
                    retryRequest.tables.push(faildRequest);
                }
            }
    
            if (! isAllSuccess && retryCount++ < maxRetryTimes) {
                batchGetRow(retryRequest);
            }
    
            console.log('success:', data);
        });
    }
    
    batchGetRow(params, maxRetryTimes);
    
                        
    ```

    For the detailed sample code, visit [BatchGetRow@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/batchGetRow.js).


## GetRange

You can call this operation to read rows of data in a forward or backward direction based on a specified primary key range.

You can also set the number of rows you want to read. If the range is large and the number of scanned rows or the volume of data exceeds the limit, the scan stops and the read rows and the next primary key information are returned. You can also initiate a request to start from where the last operation left off and read the remaining rows based on the next primary key information returned by the previous operation.

The GetRange operation may stop and return data in the following situations:

-   The amount of data read reaches 4 MB.
-   The number of rows read reaches 5,000.
-   The number of rows returned reaches the limit.
-   All reserved read throughput is consumed. You have no reserved read throughput to read the next row of data.

**Note:** In Tablestore tables, all rows are sorted by primary key. The primary key of a table sequentially consists of all primary key columns. Therefore, do not assume that the rows are sorted based on a certain primary key column.

-   Operations

    ```
      /**
       * Read data within the specified primary key range.
       */
      getRange(params, callback)
                        
    ```

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |tableName|The name of the table.|
    |direction|The direction for reading.    -   If the direction is set to FORWARD, the start primary key column value must be smaller than the end primary key column value. The rows are returned based on the primary key column values in ascending order.
    -   If the direction is set to BACKWARD, the start primary key column value must be greater than the end primary key column value. The rows are returned based on the primary key column values in descending order.
Example: A table has two primary keys A and B where the value of A is smaller than that of B. If you set direction to forward, the rows whose primary key column values are greater than or equal to the value of A and smaller than the value of B are returned in ascending order. If you set direction to backward, the rows whose primary key column values are greater than the value of A and smaller than or equal to the value of B are returned in descending order. |
    |inclusiveStartPrimaryKey|The start and end primary keys of the read operation. The start and end primary keys must be valid primary keys or virtual points of the INF\_MIN or INF\_MAX type. The number of columns for the virtual point must be the same as that of the primary key. INF\_MIN indicates an infinitely small value of which all other values of other types are greater. INF\_MAX indicates an infinitely large value of which all other values are smaller.

    -   inclusiveStartPrimaryKey specifies the start primary key. If the row exists, the response contains this row.
    -   exclusiveEndPrimaryKey specifies the end primary key. The response excludes the row no matter whether the row exists.
Rows in tables are sorted based on the primary key column values in ascending order. The range for reading is a left-closed and right-open interval. When data is read in the forward direction, all rows whose primary key column values are greater than or equal to the start primary key column value and smaller than the end primary key column value are returned. |
    |exclusiveEndPrimaryKey|
    |limit|The maximum number of returned rows. This parameter value must be greater than 0. An operation stops when the maximum number of rows are returned in the forward or backward direction, even if part of rows within the specified range are not returned. At this time, you can record the nextStartPrimaryKey value in the response for the next reading. |
    |columnsToGet|The collection of columns you want to read. The columns can be primary key columns or attribute columns. If this parameter is not set, rows that contain all columns are returned.

**Note:**

    -   If you query a row of data, the system returns the data in all columns of the row. You can use the columnsToGet parameter to read data from specified columns. If col0 and col1 are added to columnsToGet, only the values of the col0 and col1 columns are returned.
    -   If the primary key set for a row is within the specified range, but the row does not contain the specified columns, the response does not contain the row of data.
    -   If columnsToGet and columnFilter are used at the same time, the system first obtains the columns specified by columnsToGet and then filters the returned columns. |
    |maxVersions|The maximum number of versions you want to read.**Note:** You must set at least one of the following parameters: maxVersions and timeRange.

    -   If you specify only maxVersions, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If you specify only timeRange, all data within a range or a version of data is returned.
    -   If you specify both maxVersions and timeRange, data of up to the specified number of versions within the time range is returned from the latest to the earliest. |
    |timeRange|Reads data within a range of versions or a version of data. For more information, see [TimeRange](/intl.en-US/API Reference/Data Types/TimeRange.md).**Note:** You must set at least one of the following parameters: maxVersions and timeRange.

    -   If you specify only maxVersions, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If you specify only timeRange, all data within a range or a version of data is returned.
    -   If you specify both maxVersions and timeRange, data of up to the specified number of versions within the time range is returned from the latest to the earliest.
    -   To query data within a range, you must set startTime and endTime. startTime specifies the start timestamp. endTime specifies the end timestamp. The time range is a left-closed and right-open interval, which is \[startTime, endTime\).
    -   To query data within a range of versions, set specificTime. specificTime specifies a specific timestamp.
Only one of specificTime and \[startTime, endTime\) is required.

Valid values: \[0, Long.MAX\_VALUE\). Unit: milliseconds. |
    |columnFilter|Filters read results on the server to return rows that meet the conditions in the filter. For more information, see [Configure a filter](/intl.en-US/SDK Reference/NodeJS SDK/Table operations/Configure a filter.md).**Note:** If columnsToGet and columnFilter are used at the same time, the system first obtains the columns specified by columnsToGet and then filters the returned columns. |
    |nextStartPrimaryKey|The start primary key for the next reading. The value of nextStartPrimaryKey can be used to determine whether all data is read.     -   If the value of nextStartPrimaryKey is not empty in the response, the nextStartPrimaryKey value can be used as the start primary key for the next GetRange operation.
    -   If the value of nextStartPrimaryKey is empty in the response, all data within the range is returned. |

-   Examples

    The following code provides an example on how to read data within a specified range:

    ```
    var Long = TableStore.Long;
    var client = require('./client');
    
    var params = {
      tableName: "sampleTable",
      direction: TableStore.Direction.FORWARD,
      inclusiveStartPrimaryKey: [{ "gid": TableStore.INF_MIN }, { "uid": TableStore.INF_MIN }],
      exclusiveEndPrimaryKey: [{ "gid": TableStore.INF_MAX }, { "uid": TableStore.INF_MAX }],
      limit: 50
    };
    
    client.getRange(params, function (err, data) {
      if (err) {
        console.log('error:', err);
        return;
      }
    
    // If data.next_start_primary_key is not empty, continue the read operation.
      if (data.next_start_primary_key) {
    
      }
    
      console.log('success:', data);
    });
    
                        
    ```

    For the detailed sample code, visit [GetRange@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/getRange.js).


