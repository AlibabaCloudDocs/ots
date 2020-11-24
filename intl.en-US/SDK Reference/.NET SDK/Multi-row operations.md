# Multi-row operations

Tablestore SDK for .NET provides multi-row operations such as BatchGetRow, BatchWriteRow, GetRange, and GetRangeIterator.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/.NET SDK/Initialization.md).
-   A data table is created. Data is written to the table.

## BatchWriteRow

You can call this operation to write several rows of data to one or more tables by sending one request. The BatchWriteRow operation consists of PutRow, UpdateRow, and DeleteRow operations. The process of constructing a suboperation is the same as that of calling the PutRow, UpdateRow, and DeleteRow operations. BatchWriteRow supports conditional updates.

The execution and returning of results of each suboperation are carried out independently.

When multiple rows are written, part of rows may fail to be written. If the operation fails for part of rows, the system does not return exceptions. However, information about the index and error messages of the failed rows is contained in BatchWriteRowResponse. Therefore, when you call the BatchWriteRow operation, you must check the return values and check whether the status of each row is successful. If you do not check the return values, failures of operations on part of rows are ignored.

When the server detects errors caused by operations, the BatchWriteRow operation may return exceptions of parameter errors. All operations for the request are not executed.

-   Operations

    ```
    /// <summary>
    /// <para>Insert, modify, or delete several rows of data in one or more tables. </para>
    /// <para>The BatchWriteRow operation is a set of multiple PutRow, UpdateRow, and DeleteRow operations. The execution, returning of results, and capacity unit (CU) consumption of each individual operation are carried out independently. </para>
    /// <para>Compared with the execution of a large number of write operations, the use of BatchWriteRow can reduce the request response time and increase the data write rate. </para>
    /// </summary>
    /// <param name="request">Request instance</param>
    /// <returns>Response instance</returns>
    public BatchWriteRowResponse BatchWriteRow(BatchWriteRowRequest request);
    
    /// <summary>
    /// Asynchronous mode of BatchWriteRow.
    /// </summary>
    /// <param name="request"></param>
    /// <returns></returns>
    public Task<BatchWriteRowResponse> BatchWriteRowAsync(BatchWriteRowRequest request);            
    ```

-   Parameters

    For information about parameters, see [Single-row operations](/intl.en-US/SDK Reference/.NET SDK/Single-row operations.md).

-   Examples

    The following code provides an example on how to write 100 rows of data:

    ```
    // Construct a request object to write multiple rows of data. Set the primary key for 100 rows of data.
    var request = new BatchWriteRowRequest();
    var rowChanges = new RowChanges();
    for (int i = 0; i < 100; i++)
    {
        PrimaryKey primaryKey = new PrimaryKey();
        primaryKey.Add("pk0", new ColumnValue(i));
        primaryKey.Add("pk1", new ColumnValue("abc"));
    
        // Specify the attribute columns of the row.
        UpdateOfAttribute attribute = new UpdateOfAttribute();
        attribute.AddAttributeColumnToPut("col0", new ColumnValue(0));
        attribute.AddAttributeColumnToPut("col1", new ColumnValue("a"));
        attribute.AddAttributeColumnToPut("col2", new ColumnValue(true));
    
        rowChanges.AddUpdate(new Condition(RowExistenceExpectation.IGNORE), primaryKey, attribute);
    }
    
    request.Add(TableName, rowChanges);
    
    try
    {
        // Call the BatchWriteRow operation.
        var response = otsClient.BatchWriteRow(request);
        var tableRows = response.TableRespones;
        var rows = tableRows[TableName];
    
        // When multiple rows are written, part of rows may fail to be read. You must check the return values and check whether the status of each row is successful. For more information, see the link of GitHub in the sample code.
    }
    catch (Exception ex)
    {
        // If the task fails, an exception is returned. Handle the exception.
        Console.WriteLine("Batch put row failed, exception:{0}", ex.Message);
    }           
    ```

    For the detailed sample code, visit [BatchWriteRow@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/MultiRowReadWriteSample.cs).


## BatchGetRow

You can call this operation to read several rows of data from one or more tables by sending one request. The BatchGetRow operation consists of multiple GetRow operations. The process of constructing a suboperation is the same as that of calling the GetRow operation. BatchGetRow supports filters.

Note that BatchGetRow uses the same parameter configurations for all rows. For example, if ColumnsToGet is set to colA, only the value of colA is read for all the rows.

The execution and returning of results of each suboperation are carried out independently.

When multiple rows are read, part of rows may fail to be read. If the operation fails for part of rows, the system does not return exceptions. However, information about the error messages of the failed rows is contained in BatchGetRowResponse. Therefore, when you call the BatchGetRow operation, you must check the return values and check whether the status of each row is successful.

-   Operations

    ```
    /// <summary>
    /// <para>Simultaneously read several rows of data from one or more tables. </para>
    /// <para>The BatchGetRow operation is a set of multiple GetRow operations. The execution, returning of results, and CU consumption of each individual operation are carried out independently. </para>
    /// Compared with the execution of a large number of GetRow operations, the use of BatchGetRow can reduce the request response time and increase the data read rate.
    /// </summary>
    /// <param name="request">Request instance</param>
    /// <returns>Response instance</returns>
    public BatchGetRowResponse BatchGetRow(BatchGetRowRequest request);
    
    /// <summary>
    /// Asynchronous mode of BatchGetRow.
    /// </summary>
    public Task<BatchGetRowResponse> BatchGetRowAsync(BatchGetRowRequest request);            
    ```

-   Parameters

    For information about parameters, see [Single-row operations](/intl.en-US/SDK Reference/.NET SDK/Single-row operations.md).

-   Examples

    The following code provides an example on how to read 10 rows of data at a time:

    ```
    // Construct a request object to read multiple rows of data. Set the primary key for 10 rows of data.
    List<PrimaryKey> primaryKeys = new List<PrimaryKey>();
    for (int i = 0; i < 10; i++)
    {
        PrimaryKey primaryKey = new PrimaryKey();
        primaryKey.Add("pk0", new ColumnValue(i));
        primaryKey.Add("pk1", new ColumnValue("abc"));
        primaryKeys.Add(primaryKey);
    }
    
    try
    {
        BatchGetRowRequest request = new BatchGetRowRequest();
        request.Add(TableName, primaryKeys);
    
        // Call BatchGetRow to read 10 rows of data.
        var response = otsClient.BatchGetRow(request);
        var tableRows = response.RowDataGroupByTable;
        var rows = tableRows[TableName];
    
        // Export data in rows. The code for this operation is ignored. For more information, see the link of GitHub in the sample code.
    
        // When multiple rows are written, part of rows may fail to be read. You must check the return values and check whether the status of each row is successful. For more information, see the link of GitHub in the sample code.
    }
    catch (Exception ex)
    {
        // If the task fails, an exception is returned. Handle the exception.
        Console.WriteLine("Batch get row failed, exception:{0}", ex.Message);
    }            
    ```

    For the detailed sample code, visit [BatchGetRow@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/MultiRowReadWriteSample.cs).


## GetRange

You can call this operation to read rows of data in a forward or backward direction based on a specified primary key range.

You can also set the number of rows you want to read. If the range is large and the number of scanned rows or the volume of data exceeds the limit, the scan stops, and the read rows and the next primary key information are returned. You can also initiate a request to start from where the last operation left off and read the remaining rows based on the next primary key information returned by the previous operation.

The GetRange operation may stop and return data in the following situations:

-   The amount of data read reaches 4 MB.
-   The number of rows read reaches 5,000.
-   The number of rows returned reaches the limit.
-   All reserved read throughput is consumed. You have no reserved read throughput to read the next row of data.

**Note:** In Tablestore tables, all rows are sorted by primary key. The primary key of a table sequentially consists of all primary key columns. Therefore, do not assume that the rows are sorted based on a certain primary key column.

-   Operations

    ```
    /// <summary>
    /// Obtain data from multiple rows within a specified range.
    /// </summary>
    /// <param name="request">Request instance</param>
    /// <returns>Response instance</returns>
    public GetRangeResponse GetRange(GetRangeRequest request);
    
    /// <summary>
    /// Asynchronous mode of GetRange.
    /// </summary>
    /// <param name="request"></param>
    /// <returns></returns>
    public Task<GetRangeResponse> GetRangeAsync(GetRangeRequest request);              
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
    |limit|The maximum number of returned rows. This parameter value must be greater than 0. An operation stops when the maximum number of rows is returned in the forward or backward direction, even if part of rows within the specified range are not returned. At this time, you can use the checkpoint value in the response for the next reading. |
    |columnsToGet|The collection of columns you want to read. The columns can be primary key columns or attribute columns. If this parameter is not set, rows that contain all columns are returned.

**Note:**

    -   If you query a row of data, the system returns the data in all columns of the row. You can use the columnsToGet parameter to read data from specified columns. If col0 and col1 are added to columnsToGet, only the values of the col0 and col1 columns are returned.
    -   If the primary key set for a row is within the specified range, but the row does not contain the specified columns, the response does not contain the row of data.
    -   If columnsToGet and filter are used at the same time, the system first obtains the columns specified by columnsToGet and then filters the returned columns. |
    |maxVersions|The maximum number of read versions.**Note:** You must set at least one of the following parameters: maxVersions and timeRange.

    -   If you specify only maxVersions, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If you specify only timeRange, all data within a range or a version of data is returned.
    -   If you specify both maxVersions and timeRange, data of up to the specified number of versions within the time range is returned from the latest to the earliest. |
    |timeRange|Reads data within a range of versions or a version of data. For more information, see [TimeRange](/intl.en-US/API Reference/Data Types/TimeRange.md).**Note:** You must set at least one of the following parameters: maxVersions and timeRange.

    -   If you specify only maxVersions, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If you specify only timeRange, all data within a range or a version of data is returned.
    -   If you specify both maxVersions and timeRange, data of up to the specified number of versions within the time range is returned from the latest to the earliest.
    -   To query data within a range, you must set startTime and endTime. StartTime specifies the start timestamp. EndTime specifies the end timestamp. The time range is a left-closed and right-open interval, which is \[start\_time, end\_time\).
    -   To query data within a range of versions, set SpecificTime. SpecificTime specifies a specific timestamp.
Only one of SpecificTime and \[StartTime, EndTime\) is required.

Valid values: \[0, Int64.MaxValue\). Unit: milliseconds. |
    |filter|Filters read results on the server to return rows that meet the conditions in the filter. For more information, see [Configure a filter](/intl.en-US/SDK Reference/NodeJS SDK/Table operations/Configure a filter.md).**Note:** If columnsToGet and filter are used at the same time, the system first obtains the columns specified by columnsToGet and then filters the returned columns. |
    |nextStartPrimaryKey|The start primary key for the next reading. The value of nextStartPrimaryKey can be used to determine whether all data is read.     -   If the value of nextStartPrimaryKey is not empty in the response, the nextStartPrimaryKey value can be used as the start primary key for the next GetRange operation.
    -   If the value of nextStartPrimaryKey is empty in the response, all data within the range is returned. |

-   Examples

    The following code provides an example on how to read data within a specified range:

    ```
    // Read all rows in the range from (0, INF_MIN) to (100, INF_MAX).
    var inclusiveStartPrimaryKey = new PrimaryKey();
    inclusiveStartPrimaryKey.Add("pk0", new ColumnValue(0));
    inclusiveStartPrimaryKey.Add("pk1", ColumnValue.INF_MIN);
    
    var exclusiveEndPrimaryKey = new PrimaryKey();
    exclusiveEndPrimaryKey.Add("pk0", new ColumnValue(100));
    exclusiveEndPrimaryKey.Add("pk1", ColumnValue.INF_MAX);
    
    try
    {
        // Construct a request object to read data within a specified range.
        var request = new GetRangeRequest(TableName, GetRangeDirection.Forward,
                        inclusiveStartPrimaryKey, exclusiveEndPrimaryKey);
    
        var response = otsClient.GetRange(request);
    
        // Continue the read operation if only part of data is returned.
        var rows = response.RowDataList;
        var nextStartPrimaryKey = response.NextPrimaryKey;
        while (nextStartPrimaryKey ! = null)
        {
            request = new GetRangeRequest(TableName, GetRangeDirection.Forward,
                            nextStartPrimaryKey, exclusiveEndPrimaryKey);
            response = otsClient.GetRange(request);
            nextStartPrimaryKey = response.NextPrimaryKey;
            foreach (RowDataFromGetRange row in response.RowDataList)
            {
                rows.Add(row);
            }
        }
    
        // Export data in rows. The code for this operation is ignored. For more information, see the link of GitHub in the sample code.
    
        // If the task succeeds, no exception is returned.
        Console.WriteLine("Get range succeeded");
    }
    catch (Exception ex)
    {
        // If the task fails, an exception is returned. Handle the exception.
        Console.WriteLine("Get range failed, exception:{0}", ex.Message);
    }            
    ```

    For the detailed sample code, visit [GetRange@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/MultiRowReadWriteSample.cs).


## GetRangeIterator

You can call this operation to read data by iteration.

-   Operations

    ```
    /// <summary>
    /// Obtain data from multiple rows within the specified range. Return the iterator used to process each row of data.
    /// </summary>
    /// <param name="request"><see cref="GetIteratorRequest"/></param>
    /// <returns>Return the <see cref="RowDataFromGetRange"/> iterator. </returns>
    public IEnumerable<RowDataFromGetRange> GetRangeIterator(GetIteratorRequest request);           
    ```

-   Examples

    // Read all rows in the range from \(0, "a"\) to \(1000, "xyz"\).

    ```
    // Read all rows in the range from (0, "a") to (1000, "xyz").
    PrimaryKey inclusiveStartPrimaryKey = new PrimaryKey();
    inclusiveStartPrimaryKey.Add("pk0", new ColumnValue(0));
    inclusiveStartPrimaryKey.Add("pk1", new ColumnValue("a"));
    
    PrimaryKey exclusiveEndPrimaryKey = new PrimaryKey();
    exclusiveEndPrimaryKey.Add("pk0", new ColumnValue(1000));
    exclusiveEndPrimaryKey.Add("pk1", new ColumnValue("xyz"));
    
    // Construct a CapacityUnit to record the number of CUs consumed by iteration.
    var cu = new CapacityUnit(0, 0);
    
    try
    {
        // Construct a GetIteratorRequest. Filter conditions are supported.
        var request = new GetIteratorRequest(TableName, GetRangeDirection.Forward, inclusiveStartPrimaryKey,
                                                    exclusiveEndPrimaryKey, cu);
    
        var iterator = otsClient.GetRangeIterator(request);
        // Use the iterator that reads data in a traversal method.
        foreach (var row in iterator)
        {
            // Execute the processing logic.
        }
    
        Console.WriteLine("Iterate row succeeded");
    } 
    catch (Exception ex)
    {
        Console.WriteLine("Iterate row failed, exception:{0}", ex.Message);
    }            
    ```

    For the detailed code, visit [GetRangeIterator@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/MultiRowReadWriteSample.cs).


