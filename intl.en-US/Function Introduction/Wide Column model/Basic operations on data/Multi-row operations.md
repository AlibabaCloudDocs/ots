# Multi-row operations

Tablestore provides multi-row operations such as BatchWriteRow, BatchGetRow, and GetRange.

**Note:** A row is the basic unit of a table. Rows consist of primary keys and attributes. A primary key is required for a row. Rows within a table contain primary key columns of the same names and same data types. Attributes are optional for a row. Rows within a table can contain different attributes. For more information, see [Overview](/intl.en-US/Function Introduction/Wide Column model/Overview.md).

## Operations

Multi-row operations include BatchWriteRow, BatchGetRow, and GetRange. The following table describes the operations.

|Operation|Description|
|---------|-----------|
|[BatchGetRow](/intl.en-US/API Reference/Operations/BatchGetRow.md)|Reads several rows of data from one or more tables.|
|[BatchWriteRow](/intl.en-US/API Reference/Operations/BatchWriteRow.md)|Inserts rows into or delete rows from one or more tables. You can also call this operation to update rows in one or more tables.|
|[GetRange](/intl.en-US/API Reference/Operations/GetRange.md)|Queries data within a specified range based on primary key values.|

## Use Tablestore SDKs

You can use the following Tablestore SDKs to perform multi-row operations on data:

-   Tablestore SDK for Java: [Multi-row operations](/intl.en-US/SDK Reference/Java SDK/Multi-row operations.md)
-   Tablestore SDK for Go: [Multi-row operations](/intl.en-US/SDK Reference/Go SDK/Multi-row operations.md)
-   Tablestore SDK for Python: [Multi-row operations](/intl.en-US/SDK Reference/Python SDK/Multi-row operations.md)
-   Tablestore SDK for Node.js: [Multiple data operations](/intl.en-US/SDK Reference/NodeJS SDK/Multi-row operations.md)
-   Tablestore SDK for .NET: [Multi-row operations](/intl.en-US/SDK Reference/.NET SDK/Multi-row operations.md)
-   Tablestore SDK for PHP: [Multi-row operations](/intl.en-US/SDK Reference/PHP SDK/Multi-row operations.md)

## BatchWriteRow

You can call this operation to write multiple rows to one or more tables in a single request. A BatchWriteRow operation consists of multiple PutRow, UpdateRow, and DeleteRow operations. The process to construct suboperations is the same as that for using the PutRow, UpdateRow, and DeleteRow operations. You can also set update conditions.

The execution and returning of results of each suboperation are carried out independently.

When multiple rows are written, some rows may fail to be written. If the operation fails for some rows, an exception may not occur, but information about the index and error messages of the failed rows is stored in BatchWriteRowResponse. Therefore, when you call the BatchWriteRow operation, you must check the returned values. You can use the isAllSucceed method of BatchWriteRowResponse to check whether all operations are successful. If you do not check the returned values, some operation failures may be ignored.

When the server detects that invalid parameters exist in some operations, the BatchWriteRow operation may return a parameter error exception before all the operations in the request are performed.

-   Parameter

    For more information, see [Single-row operations](/intl.en-US/Function Introduction/Wide Column model/Basic operations on data/Single-row operations.md).

-   Examples

    The following code provides an example of how to send a single BatchWriteRow request, which includes two PutRow operations, one UpdateRow operation, and one DeleteRow operation:

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
    
        System.out.println("Whether all operations are successful:" + response.isAllSucceed());
        if (! response.isAllSucceed()) {
            for (BatchWriteRowResponse.RowResult rowResult : response.getFailedRows()) {
                System.out.println("Failed rows:" + batchWriteRowRequest.getRowChange(rowResult.getTableName(), rowResult.getIndex()).getPrimaryKey());
                System.out.println("Cause of failures:" + rowResult.getError());
            }
            /**
             * You can use the createRequestForRetry method to construct another request to retry failed rows. Only the retry request is constructed here.
             * We recommend that you use the custom retry policy in Tablestore SDKs as the retry method. This feature allows you to retry failed rows after batch operations. After you set the retry policy, you do not need to add retry code when you call the retry operation.
             */
            BatchWriteRowRequest retryRequest = batchWriteRowRequest.createRequestForRetry(response.getFailedRows());
        }
    }           
    ```


## BatchGetRow

You can call this operation to read multiple rows from one or more tables in a single request. A BatchGetRow operation consists of multiple GetRow operations. The process to construct suboperations is the same as that for using the GetRow operations.

BatchGetRow uses the same parameter configurations for all rows. For example, if ColumnsToGet is set to colA, only the value of colA is read for all the rows.

The execution and returning of results of each suboperation are carried out independently.

When multiple rows are read, some rows may fail to be read. If the operation fails for some rows, an exception may not occur, but information about the failed rows is stored in BatchGetRowResponse. Therefore, when you call the BatchGetRow operation, you must check the returned values. You can use the isAllSucceed method of BatchGetRowResponse to check whether all operations are successful. You can also use the getFailedRows method of BatchGetRowResponse to query the information of the failed operations.

-   Parameter

    For more information, see [Single-row operations](/intl.en-US/Function Introduction/Wide Column model/Basic operations on data/Single-row operations.md).

-   Examples

    The following code provides an example on how to configure the version conditions, columns to read, and filters to read 10 rows of data:

    ```
    private static void batchGetRow(SyncClient client) {
        MultiRowQueryCriteria multiRowQueryCriteria = new MultiRowQueryCriteria(TABLE_NAME);
        // Add 10 rows to read.
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
    
        System.out.println("Whether all operations are successful:" + batchGetRowResponse.isAllSucceed());
        if (! batchGetRowResponse.isAllSucceed()) {
            for (BatchGetRowResponse.RowResult rowResult : batchGetRowResponse.getFailedRows()) {
                System.out.println("Failed rows:" + batchGetRowRequest.getPrimaryKey(rowResult.getTableName(), rowResult.getIndex()));
                System.out.println("Cause of failures:" + rowResult.getError());
            }
    
            /**
             * You can use the createRequestForRetry method to construct another request to retry failed rows. Only the retry request is constructed here.
             * We recommend that you use the custom retry policy in Tablestore SDKs as the retry method. This feature allows you to retry failed rows after batch operations. After you set the retry policy, you do not need to add retry code when you call the retry operation.
             */
                    BatchGetRowRequest retryRequest = batchGetRowRequest.createRequestForRetry(batchGetRowResponse.getFailedRows());
        }
    }
                
    ```


## GetRange

You can call this operation to read data within a specified range based on primary key values. The range specified by primary key value is a left-closed and right-open interval.

The GetRange operation allows you to read data in a forward or backward direction within a specified range. You can also specify the number of rows to read. If the range is large and the number of scanned rows or the volume of data exceeds the limit, the scan stops and the read rows and the next primary key information are returned. If the rows are not all read, you can initiate a request to start from where the last operation stops and read the remaining rows based on the next primary key information returned by the previous operation.

**Note:** In Tablestore tables, all rows are sorted by primary key. The primary key of a table sequentially consists of all primary key columns. Therefore, do not assume that the rows are sorted based on a specific primary key column.

GetRange follows the leftmost matching principle. Tablestore compares values in sequence from the first primary key column to the last primary key column to read data within the specified range. For example, the primary key of a data table consists of the following primary key columns: PK1, PK2, and PK3. When data is read, Tablestore first compares the PK1 value of a row with that of the start and end primary keys to determine whether the PK1 value of the row is within the range. If the PK1 value of a row is within the range, Tablestore stops comparing the values of other primary key columns and returns the data of the row. If the PK1 value of a row is not within the range, Tablestore continues to compare the values of other primary key columns in the same way as PK1. For more information, see [Detailed explanation of GetRange query](https://developer.aliyun.com/article/742095?spm=a2c6h.14164896.0.0.7d7c5b4fXmyQyd).

The GetRange operation may stop and return data in the following situations:

-   The amount of read data reaches 4 MB.
-   The number of read rows reaches 5,000.
-   The number of returned rows reaches the limit.
-   All reserved read throughput is consumed. You have no reserved read throughput to read the next row of data.

The number of read CUs consumed by a GetRange operation is calculated from the start point of the range to the start point of the next row that is unread. The number of consumed read CUs is calculated based on the formula: Number of consumed read CUs = \[\(Size of primary key columns in the rows + Size of attribute columns that are read\)/4 KB\] rounded up. Example: The specified range to read contains 10 rows, and the total size of primary key columns and the attribute columns that are read is 330 bytes. The number of consumed read CUs is calculated based on the formula: Number of consumed read CUs = \(3.3 KB/4 KB\) rounded up. In this case, the GetRange operation consumes one read CU.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |tableName|The name of the table.|
    |direction|The direction to read data.    -   If this parameter is set to FORWARD, the value of the start primary key must be smaller than that of the end primary key. The returned rows are sorted in ascending order based on the primary key values.
    -   If the value is set to BACKWARD, the value of the start primary key must be greater than that of the end primary key. The returned rows are sorted in descending order based on the primary key values.
Example: A table contains two primary keys A and B where the value of A is smaller than that of B. If you set direction to FORWARD, the rows whose primary key values are equal to or greater than the value of A and smaller than the value of B are returned in ascending order from A to B. If you set direction to BACKWARD, the rows whose primary key values are equal to or greater than the value of B and smaller than the value of A are returned in descending order from B to A. |
    |inclusiveStartPrimaryKey|The start primary key and end primary key of the range to read. The start and end primary keys must be valid primary keys or virtual points composed of the INF\_MIN and INF\_MAX type data. The number of columns for each virtual point must be the same as that of each primary key. INF\_MIN indicates an infinitely small value. All other values of other types are greater than INF\_MIN. INF\_MAX indicates an infinitely great value. All other values of other types are smaller than INF\_MAX.

    -   inclusiveStartPrimaryKey indicates the start primary key. If a row that contains the start primary key exists, the row is included in the response.
    -   exclusiveEndPrimaryKey indicates the end primary key. No matter whether a row that contains the end primary key exists, the row is not included in the response.
The rows in the data table are sorted in ascending order based on the primary key values. The range to read is a left-closed and right-open interval. If data is read in forward direction, rows whose primary keys are greater than or equal to the start primary key and smaller than the end primary key are returned. |
    |exclusiveEndPrimaryKey|
    |limit|The maximum number of rows that can be returned. The value of this parameter must be greater than 0. An operation stops when the maximum number of rows returned in the forward or backward direction is exceeded, even if a part of rows within the specified range are not returned. You can use the value of nextStartPrimaryKey returned in the response to read data in the next request. |
    |columnsToGet|The names of the columns to read. You can specify the names of primary key columns and the names of attribute columns. If you do not specify this parameter, all data in the row is returned.

**Note:**

    -   By default, Tablestore returns the data from all columns of the row when you query a row. You can use the columnsToGet parameter to read data from specified columns. For example, the system returns only the values of col0 and col1 if col0 and col1 are included in columnsToGet.
    -   If a row is within the specified range to read based on the primary key value but does not contain the specified columns to return, the response excludes the row.
    -   If you configure columnsToGet and filter at the same time, Tablestore first queries the columns specified by columnsToGet, and then returns rows that meet the filter conditions. |
    |maxVersions|The maximum number of versions that can be read.**Note:** You must set at least one of the following parameters: maxVersions and timeRange.

    -   If you specify only maxVersions, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If you specify only timeRange, all data whose versions are within the specified time range or data of the specified version is returned.
    -   If you specify both maxVersions and timeRange, data of up to the specified number of versions within the time range is returned from the latest to the earliest. |
    |timeRange|The versions within a specified time range or the specified version to read. For more information, see [TimeRange](/intl.en-US/API Reference/Data Types/TimeRange.md).**Note:** You must set at least one of the following parameters: maxVersions and timeRange.

    -   If you specify only maxVersions, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If you specify only timeRange, all data whose versions are within the specified time range or data of the specified version is returned.
    -   If you specify both maxVersions and timeRange, data of up to the specified number of versions within the time range is returned from the latest to the earliest.
    -   To query data whose versions are within a specified time range, you must set start and end. start indicates the start timestamp. end indicates the end timestamp. The specified range is a left-closed and right-open interval, which includes the start value and excludes the end value.
    -   To query data of a specified version, you must set timestamp. timestamp indicates a specified timestamp.
You need only to set one of timestamp and \[start, end\).

The minimum value you can specify for timestamp is 0, and the maximum value is Long.MAX\_VALUE. Unit: milliseconds. |
    |filter|The filter used to filter the query results on the server side of Tablestore. Only rows that meet the filter conditions are returned. For more information, see [Filter](/intl.en-US/Function Introduction/Wide Column model/Filter.md).**Note:** If you configure columnsToGet and filter at the same time, Tablestore first queries the columns specified by columnsToGet, and then returns rows that meet the filter conditions. |
    |nextStartPrimaryKey|The start primary key of the next read. The value of nextStartPrimaryKey can be used to determine whether all data is read.     -   If the value of nextStartPrimaryKey is not empty in the response, the nextStartPrimaryKey value can be used as the value of the start primary key for the next GetRange operation.
    -   If the value of nextStartPrimaryKey is empty in the response, all data within the range is returned. |

-   Example 1:

    The following code provides an example on how to read data within a specified range in the forward direction. Data within the range is read until the value of nextStartPrimaryKey is empty. This way, all data in the specified range can be read.

    ```
    private static void getRange(SyncClient client, String startPkValue, String endPkValue) {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(TABLE_NAME);
    
        // Specify the primary key to start from.
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(startPkValue));
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(primaryKeyBuilder.build());
    
        // Specify the primary key to end with.
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
    
            // If the NextStartPrimaryKey value is not null, continue the read operation.
            if (getRangeResponse.getNextStartPrimaryKey() ! = null) {
                rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
            } else {
                break;
            }
        }
    }         
    ```

-   Example 2

    The following code provides an example on how to read data in the forward direction based on the range determined by the value of the first primary key column. The value of the second primary key column in the start primary key is INF\_MIN. The value of the second primary key column in the end primary key is INF\_MAX. Data within the range is read until the value of nextStartPrimaryKey is null. This way, all data in the specified range can be read.

    ```
    private static void getRange(SyncClient client, String startPkValue, String endPkValue) {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(TABLE_NAME);
        // Specify the primary key to start from. In this example, the primary key includes two primary key columns.
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME1, PrimaryKeyValue.fromString(startPkValue)); // Set the value of the first primary key column to a specified value.
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME2, PrimaryKeyValue.INF_MIN); // Set the value of the second primary key column to an infinitely small value.
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(primaryKeyBuilder.build());
    
        // Specify the primary key to end with.
        primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME1, PrimaryKeyValue.fromString(endPkValue)); // Set the value of the first primary key column to a specified value.
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME2, PrimaryKeyValue.INF_MAX); // Set the value of the second primary key column to an infinitely great value.
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(primaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        System.out.println("GetRange result:");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
                System.out.println(row);
            }
    
            // If the nextStartPrimaryKey value is not null, continue to read data.
            if (getRangeResponse.getNextStartPrimaryKey() ! = null) {
                rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
            } else {
                break;
            }
        }
    }         
    ```


