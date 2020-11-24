# Multi-row operations

Tablestore SDK for Java provides multi-row operations such as BatchWriteRow, BatchGetRow, GetRange, and createRangeIterator.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialize SDK for Java.md).
-   A data table is created. Data is written to the table.

## BatchWriteRow

You can call this operation to write several rows of data to one or more tables by sending one request. The BatchWriteRow operation consists of PutRow, UpdateRow, and DeleteRow operations. The process of constructing a suboperation is the same as that of calling the PutRow, UpdateRow, and DeleteRow operations. BatchWriteRow supports conditional updates.

The execution and returning of results of each suboperation are carried out independently.

When multiple rows are written, part of rows may fail to be written. If the operation fails for part of rows, the system does not return exceptions. However, information about the index and error messages of the failed rows is contained in BatchWriteRowResponse. Therefore, when you call the BatchWriteRow operation, you must check the return values and check whether the status of each row is successful based on isAllSucceed in BatchWriteRowResponse. If you do not check the return values, failures of operations on part of rows are ignored.

When the server detects errors caused by operations, the BatchWriteRow operation may return exceptions of parameter errors. All operations for the request are not executed.

-   Parameters

    For information about parameters, see [Single-row operations](/intl.en-US/SDK Reference/Java SDK/Single-row operations.md).

-   Examples

    The following code provides an example on how to send a single BatchWriteRow request, which includes two PutRow operations, one UpdateRow operation, and one DeleteRow operation:

    ```
    private static void batchWriteRow(SyncClient client) {
        BatchWriteRowRequest batchWriteRowRequest = new BatchWriteRowRequest();
    
        // Construct rowPutChange1.
        PrimaryKeyBuilder pk1Builder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        pk1Builder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString("pk1"));
        RowPutChange rowPutChange1 = new RowPutChange(TABLE_NAME, pk1Builder.build());
        // Add columns.
        for (int i = 0; i < 10; i++) {
            rowPutChange1.addColumn(new Column("Col" + i, ColumnValue.fromLong(i)));
        }
        // Add rowPutChange1 to the batch operation.
        batchWriteRowRequest.addRowChange(rowPutChange1);
    
        // Construct rowPutChange2.
        PrimaryKeyBuilder pk2Builder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        pk2Builder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString("pk2"));
        RowPutChange rowPutChange2 = new RowPutChange(TABLE_NAME, pk2Builder.build());
        // Add columns.
        for (int i = 0; i < 10; i++) {
            rowPutChange2.addColumn(new Column("Col" + i, ColumnValue.fromLong(i)));
        }
        // Add rowPutChange2 to the batch operation.
        batchWriteRowRequest.addRowChange(rowPutChange2);
    
        // Construct rowUpdateChange.
        PrimaryKeyBuilder pk3Builder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        pk3Builder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString("pk3"));
        RowUpdateChange rowUpdateChange = new RowUpdateChange(TABLE_NAME, pk3Builder.build());
        // Add columns.
        for (int i = 0; i < 10; i++) {
            rowUpdateChange.put(new Column("Col" + i, ColumnValue.fromLong(i)));
        }
        // Delete the specified column.
        rowUpdateChange.deleteColumns("Col10");
        // Add rowUpdateChange to the batch operation.
        batchWriteRowRequest.addRowChange(rowUpdateChange);
    
        // Construct rowDeleteChange.
        PrimaryKeyBuilder pk4Builder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        pk4Builder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString("pk4"));
        RowDeleteChange rowDeleteChange = new RowDeleteChange(TABLE_NAME, pk4Builder.build());
        // Add rowDeleteChange to the batch operation.
        batchWriteRowRequest.addRowChange(rowDeleteChange);
    
        BatchWriteRowResponse response = client.batchWriteRow(batchWriteRowRequest);
    
        System.out.println("Were all successful:" + response.isAllSucceed());
        if (! response.isAllSucceed()) {
            for (BatchWriteRowResponse.RowResult rowResult : response.getFailedRows()) {
                System.out.println("Failed rows:" + batchWriteRowRequest.getRowChange(rowResult.getTableName(), rowResult.getIndex()).getPrimaryKey());
                System.out.println("Cause of failure:" + rowResult.getError());
            }
            /**
             * You can use the createRequestForRetry method to construct another request to retry failed rows. Only the retry request is constructed in the example.
             * We recommend that you use the custom retry policy in Tablestore SDK for Java as the retry method. This feature allows you to retry failed rows after batch operations. After you set the retry policy, you do not need to add retry code when you call the retry operation.
             */
            BatchWriteRowRequest retryRequest = batchWriteRowRequest.createRequestForRetry(response.getFailedRows());
        }
    }
                
    ```

    For the detailed sample code, visit [BatchWriteRow@GitHub](https://github.com/aliyun/aliyun-tablestore-java-sdk/blob/master/src/test/java/examples/DataOperationSample.java).


## BatchGetRow

You can call this operation to read several rows of data from one or more tables by sending one request. The BatchGetRow operation consists of multiple GetRow operations. The process of constructing a suboperation is the same as that of calling the GetRow operation.

Note that BatchGetRow uses the same parameter configurations for all rows. For example, if ColumnsToGet is set to \[colA\], only the value of colA is read for all the rows.

The execution and returning of results of each suboperation are carried out independently.

When multiple rows are read, part of rows may fail to be read. If the operation fails for part of rows, the system does not return exceptions. However, information about the error messages of the failed rows is contained in BatchGetRowResponse. Therefore, when you call the BatchGetRow operation, you must check the return values and check whether the status of each row is successful by using the isAllSucceed method in BatchGetRowResponse. You can use the getFailedRows method in BatchGetRowResponse to obtain information about failed rows.

-   Parameters

    For information about parameters, see [Single-row operations](/intl.en-US/SDK Reference/Java SDK/Single-row operations.md).

-   Examples

    The following code provides an example on how to read 10 rows of data, and configure the version conditions, columns to be read, and filters:

    ```
    private static void batchGetRow(SyncClient client) {
        MultiRowQueryCriteria multiRowQueryCriteria = new MultiRowQueryCriteria(TABLE_NAME);
        // Add 10 rows to be read.
        for (int i = 0; i < 10; i++) {
            PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString("pk" + i));
            PrimaryKey primaryKey = primaryKeyBuilder.build();
            multiRowQueryCriteria.addRow(primaryKey);
        }
        // Add conditions.
        multiRowQueryCriteria.setMaxVersions(1);
        multiRowQueryCriteria.addColumnsToGet("Col0");
        multiRowQueryCriteria.addColumnsToGet("Col1");
        SingleColumnValueFilter singleColumnValueFilter = new SingleColumnValueFilter("Col0",
                SingleColumnValueFilter.CompareOperator.EQUAL, ColumnValue.fromLong(0));
        singleColumnValueFilter.setPassIfMissing(false);
        multiRowQueryCriteria.setFilter(singleColumnValueFilter);
    
        BatchGetRowRequest batchGetRowRequest = new BatchGetRowRequest();
        // BatchGetRow allows you to read data from multiple tables. A single multiRowQueryCriteria corresponds to a query condition for one table. You can add multiple multiRowQueryCriteria conditions to read data from multiple tables.
        batchGetRowRequest.addMultiRowQueryCriteria(multiRowQueryCriteria);
    
        BatchGetRowResponse batchGetRowResponse = client.batchGetRow(batchGetRowRequest);
    
        System.out.println("Were all successful:" + batchGetRowResponse.isAllSucceed());
        if (! batchGetRowResponse.isAllSucceed()) {
            for (BatchGetRowResponse.RowResult rowResult : batchGetRowResponse.getFailedRows()) {
                System.out.println("Failed rows:" + batchGetRowRequest.getPrimaryKey(rowResult.getTableName(), rowResult.getIndex()));
                System.out.println("Cause of failure:" + rowResult.getError());
            }
    
            /**
             * You can use the createRequestForRetry method to construct another request to retry failed rows. Only the retry request is constructed in the example.
             * We recommend that you use the custom retry policy in Tablestore SDK for Java as the retry method. This feature allows you to retry failed rows after batch operations. After you set the retry policy, you do not need to add retry code when you call the retry operation.
             */
                    BatchGetRowRequest retryRequest = batchGetRowRequest.createRequestForRetry(batchGetRowResponse.getFailedRows());
        }
    }
                
    ```

    For the detailed sample code, visit [BatchGetRow@GitHub](https://github.com/aliyun/aliyun-tablestore-java-sdk/blob/master/src/test/java/examples/DataOperationSample.java).


## GetRange

You can call this operation to read data in a forward or backward direction based on a specified range.

You can also limit the number of rows you want to read. If the range is large and the number of scanned rows or the volume of data exceeds the limit, the scan stops and the read rows and the next primary key information are returned. If only part of the rows are read, you can initiate a request to start from where the last operation left off and read the remaining rows based on the next primary key information returned by the previous operation.

**Note:** In Tablestore tables, all rows are sorted by primary key. The primary key of a table sequentially consists of all primary key columns. Therefore, do not assume that the rows are sorted based on a certain primary key column.

The GetRange operation may stop and return data in the following situations:

-   The amount of data read reaches 4 MB.
-   The number of rows read reaches 5,000.
-   The number of rows returned reaches the limit.
-   All reserved read throughput is consumed. You have no reserved read throughput to read the next row of data.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |tableName|The name of the table.|
    |direction|The direction for reading.    -   If the direction is set to FORWARD, the start primary key column value must be smaller than the end primary key column value. The rows are returned based on the primary key column values in ascending order.
    -   If the direction is set to BACKWARD, the start primary key column value must be greater than the end primary key column value. The rows are returned based on the primary key column values in descending order.
Example: A table has two primary keys A and B where the value of A is smaller than that of B. If you set the direction to forward, the rows whose primary key column values are greater than or equal to the value of A and smaller than the value of B are returned in ascending order. If you set the direction to backward, the rows whose primary key column values are greater than the value of A and smaller than or equal to the value of B are returned in descending order. |
    |inclusiveStartPrimaryKey|The start and end primary keys of the read operation. The start and end primary keys must be valid primary keys or virtual points of the INF\_MIN or INF\_MAX type. The number of columns for the virtual point must be the same as that of the primary key. INF\_MIN indicates an infinitely small value of which all other values of other types are greater. INF\_MAX indicates an infinitely large value of which all other values are smaller.

    -   inclusiveStartPrimaryKey specifies the start primary key. If the row exists, the response contains this row.
    -   exclusiveEndPrimaryKey specifies the end primary key. The response excludes the row no matter whether the row exists.
Rows in tables are sorted based on the primary key column values in ascending order. The range for reading is a left-closed and right-open interval. When data is read in the forward direction, all rows whose primary key column values are greater than or equal to the start primary key column value and smaller than the end primary key column value are returned. |
    |exclusiveEndPrimaryKey|
    |limit|The maximum number of returned rows. This parameter value must be greater than 0. An operation stops when the maximum number of rows is returned in the forward or backward direction, even if part of rows within the specified range are not returned. At this time, you can record the nextStartPrimaryKey value in the response for the next reading. |
    |columnsToGet|The collection of columns you want to read. The columns can be primary key columns or attribute columns. If this parameter is not set, rows that contain all columns are returned.

**Note:**

    -   To query a row of data, the system returns the data in all columns of the row. You can use the columnsToGet parameter to read data from specified columns. If col0 and col1 are added to columnsToGet, only the values of the col0 and col1 columns are returned.
    -   If the primary key set for a row is within the specified range, but the row does not contain the specified columns, the response does not contain the row of data.
    -   If columnsToGet and filter are used at the same time, the system first obtains the columns specified by columnsToGet and then filters the returned columns. |
    |maxVersions|The maximum number of versions you want to read.**Note:** You must set at least one of the following parameters: maxVersions and timeRange.

    -   If you specify only maxVersions, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If you specify only timeRange, all data within a range or a version of data is returned.
    -   If you specify both maxVersions and timeRange, data of up to the specified number of versions within the time range is returned from the latest to the earliest. |
    |timeRange|Reads data within a range of versions or a version of data. For more information, see [TimeRange](/intl.en-US/API Reference/Data Types/TimeRange.md).**Note:** You must set at least one of the following parameters: maxVersions and timeRange.

    -   If you specify only maxVersions, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If you specify only timeRange, all data within a range or a version of data is returned.
    -   If you specify both maxVersions and timeRange, data of up to the specified number of versions within the time range is returned from the latest to the earliest.
    -   To query data within a range, you must set start and end. start indicates the start timestamp. end indicates the end timestamp. The time range is a left-open and right-closed interval, which is \[start, end\).
    -   To query data of a specific version, set timestamp. timestamp specifies a specific timestamp.
Only one of timestamp and \[start, end\) is required.

Valid values: \[0, Long.MAX\_VALUE\). Unit: milliseconds. |
    |filter|Filters read results on the server to return rows that meet the conditions in the filter. For more information, see [Configure filter](/intl.en-US/SDK Reference/Java SDK/Table operations/Configure filter.md).**Note:** If columnsToGet and filter are used at the same time, the system first obtains the columns specified by columnsToGet and then filters the returned columns. |
    |nextStartPrimaryKey|The start primary key for the next reading. The value of nextStartPrimaryKey can be used to determine whether all data is read.     -   If the value of nextStartPrimaryKey is not empty in the response, the nextStartPrimaryKey value can be used as the start primary key for the next GetRange operation.
    -   If the value of nextStartPrimaryKey is empty in the response, all data within the range is returned. |

-   Example 1

    The following code provides an example on how to read data within a range in the forward direction, determine whether nextStartPrimaryKey is empty, and read all data within the range:

    ```
    private static void getRange(SyncClient client, String startPkValue, String endPkValue) {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(TABLE_NAME);
    
        // Set the start primary key.
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(startPkValue));
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(primaryKeyBuilder.build());
    
        // Set the end primary key.
        primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(endPkValue));
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(primaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        System.out.println("GetRange result:");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
                System.out.println(row);
            }
    
            // If NextStartPrimaryKey is not empty, continue the read operation.
            if (getRangeResponse.getNextStartPrimaryKey() ! = null) {
                rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
            } else {
                break;
            }
        }
    }         
    ```

-   Example 2

    The following code provides an example on how to determine the range based on the first primary key column, read data from the minimum \(INF\_MIN\) to maximum \(INF\_MAX\) values in the forward direction based on the second primary key column, determine whether nextStartPrimaryKey is empty, and read all data within the range:

    ```
    private static void getRange(SyncClient client, String startPkValue, String endPkValue) {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(TABLE_NAME);
        // Set the start primary key. Two primary keys are used in the example.
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME1, PrimaryKeyValue.fromString(startPkValue));// Specify the value.
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME2, PrimaryKeyValue.INF_MIN);// Specify the minimum value.
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(primaryKeyBuilder.build());
    
        // Set the end primary key.
        primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME1, PrimaryKeyValue.fromString(endPkValue));// Specify the value.
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME2, PrimaryKeyValue.INF_MAX);// Specify the maximum value.
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(primaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        System.out.println("GetRange result:");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
                System.out.println(row);
            }
    
            // If nextStartPrimaryKey is not empty, continue to read data.
            if (getRangeResponse.getNextStartPrimaryKey() ! = null) {
                rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
            } else {
                break;
            }
        }
    }         
    ```

    For the detailed sample code, visit [GetRange@GitHub](https://github.com/aliyun/aliyun-tablestore-java-sdk/blob/master/src/test/java/examples/DataOperationSample.java).


## createRangeIterator

You can call this operation to read data by iteration.

```
private static void getRangeByIterator(SyncClient client, String startPkValue, String endPkValue) {
    RangeIteratorParameter rangeIteratorParameter = new RangeIteratorParameter(TABLE_NAME);

    // Set the start primary key.
    PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
    primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(startPkValue));
    rangeIteratorParameter.setInclusiveStartPrimaryKey(primaryKeyBuilder.build());

    // Set the end primary key.
    primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
    primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(endPkValue));
    rangeIteratorParameter.setExclusiveEndPrimaryKey(primaryKeyBuilder.build());

    rangeIteratorParameter.setMaxVersions(1);

    Iterator<Row> iterator = client.createRangeIterator(rangeIteratorParameter);

    System.out.println("Results obtained when Iterator is used to implement GetRange:");
    while (iterator.hasNext()) {
        Row row = iterator.next();
        System.out.println(row);
    }
}           
```

For the detailed sample code, visit [GetRangeByIterator@GitHub](https://github.com/aliyun/aliyun-tablestore-java-sdk/blob/master/src/test/java/examples/DataOperationSample.java).

