# Multi-row operations

Tablestore SDKs provide multi-row operations such as BatchWriteRow, BatchGetRow, and GetRange.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A data table is created. Data is written to the table.

## BatchWriteRow

You can call this operation to write several rows of data from one or more tables in a request. The BatchWriteRow operation consists of multiple PutRow, UpdateRow, and DeleteRow operations. The process of constructing an operation is the same as that of calling the PutRow, UpdateRow, and DeleteRow operations. BatchWriteRow supports conditional update.

Each individual operation of BatchWriteRow is independently performed. The result for each individual operation is independently returned.

When multiple rows are written, part of rows may fail to be written. If the operation fails for part of rows, an exception may not occur, but information about the index and error messages of the failed rows is stored in BatchWriteRowResponse. When you call the BatchWriteRow operation, you must check the response to determine whether the status of each row is successful. We recommend that you check the response in case of failed operations.

When the server detects incorrect parameters during operations, BatchWriteRow may throw a parameter exception when none of the operations are completed for the request.

-   API operations

    ```
    // Add, delete, or update multiple rows of data in multiple tables.
    //
    // @param BatchWriteRowRequest             Encapsulate the parameters required to call the BatchWriteRow operation.
    // @return  BatchWriteRowResponse          The content of the response to the BatchWriteRow operation.
    BatchWriteRow(request *BatchWriteRowRequest) (*BatchWriteRowResponse,error)                  
    ```

-   Parameters

    For more information, see [Single-row operations](/intl.en-US/SDK Reference/Go SDK/Single-row operations.md).

-   Examples

    The following code provides an example on how to write 100 rows of data:

    ```
    batchWriteReq := &tablestore.BatchWriteRowRequest{}
    for i := 0; i < 100; i++ {
        putRowChange := new(tablestore.PutRowChange)
        putRowChange.TableName = tableName
        putPk := new(tablestore.PrimaryKey)
        putPk.AddPrimaryKeyColumn("pk1", "pk1value1")
        putPk.AddPrimaryKeyColumn("pk2", int64(i))
        putPk.AddPrimaryKeyColumn("pk3", []byte("pk3"))
        putRowChange.PrimaryKey = putPk
        putRowChange.AddColumn("col1", "fixvalue")
        putRowChange.SetCondition(tablestore.RowExistenceExpectation_IGNORE)
        batchWriteReq.AddRowChange(putRowChange)
    }
    
    response, err := client.BatchWriteRow(batchWriteReq)
    if err != nil {
        fmt.Println("batch request failed with:", response)
    } else {
        fmt.Println("batch write row finished")
    }                   
    ```

    For the detailed sample code, visit [BatchWriteRow@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/MultipleRowOperation.go).


## BatchGetRow

You can call this operation to read several rows of data from one or more tables in a request. BatchGetRow consists of multiple GetRow operations. The process of constructing an operation is the same as that of calling the GetRow operation. BatchGetRow supports conditional update.

Note that BatchGetRow uses the same parameter configurations for all rows. For example, if ColumnsToGet is set to colA, only the value of colA is read for all the rows.

Each individual operation of BatchGetRow is independently performed. The result for each individual operation is independently returned.

When multiple rows are read, part of rows may fail to be read. If the operation fails for part of rows, an exception may not occur, but the error messages of the failed rows is stored in BatchGetRowResponse. When you call the BatchWriteRow operation, you must check the response to determine whether the status of each row is successful.

-   API operations

    ```
    // Return multiple rows of data from a table.
    //
    // @param BatchGetRowRequest             Encapsulate the parameters required to call the BatchGetRow operation.
    // @return  BatchGetRowResponse          The content of the response to the BatchGetRow operation.
    BatchGetRow(request *BatchGetRowRequest) (*BatchGetRowResponse, error)                    
    ```

-   Parameters

    For more information, see [Single-row operations](/intl.en-US/SDK Reference/Go SDK/Single-row operations.md).

-   Examples

    The following code provides an example on how to read 10 rows of data at a time:

    ```
    batchGetReq := &tablestore.BatchGetRowRequest{}
    mqCriteria := &tablestore.MultiRowQueryCriteria{}
    
    for i := 0; i < 10; i++ {
        pkToGet := new(tablestore.PrimaryKey)
        pkToGet.AddPrimaryKeyColumn("pk1", "pk1value1")
        pkToGet.AddPrimaryKeyColumn("pk2", int64(i))
        pkToGet.AddPrimaryKeyColumn("pk3", []byte("pk3"))
        mqCriteria.AddRow(pkToGet)
        mqCriteria.MaxVersion = 1
    }
    mqCriteria.TableName = tableName
    batchGetReq.MultiRowQueryCriteria = append(batchGetReq.MultiRowQueryCriteria, mqCriteria)
    batchGetResponse, err := client.BatchGetRow(batchGetReq)
    
    if err != nil {
        fmt.Println("batchget failed with error:", err)
    } else {
        fmt.Println("batchget finished")
    }
                        
    ```

    For the detailed sample code, visit [BatchGetRow@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/MultipleRowOperation.go).


## GetRange

You can call this operation to read data within the specified range based on the primary key values.

The GetRange operation allows you to read data in a forward or backward direction within a specified range. You can also specify the number of rows to read. If the range is large and the number of scanned rows or the volume of data exceeds the limit, the scan stops and the read rows and the next primary key are returned. If only part of the rows are read, you can initiate a request to start from where the last operation left off and read the remaining rows based on the next primary key returned by the previous operation.

The GetRange operation may stop and return data in the following situations:

-   The amount of data read reaches 4 MB.
-   The number of rows read reaches 5,000.
-   The number of rows returned reaches the limit.
-   All reserved read throughput is consumed. You have no reserved read throughput to read the next row of data.

**Note:** By default, all rows are sorted by primary key in Tablestore tables. The primary key of a table sequentially consists of all primary key columns. Therefore, do not assume that the rows are sorted based on a specific primary key column.

-   API operations

    ```
    // Query multiple rows of data within a specified range in a table.
    //
    // @param GetRangeRequest            Encapsulate the parameters required to call the GetRange operation.
    // @return GetRangeResponse          The content of the response to the GetRange operation.
    GetRange(request *GetRangeRequest) (*GetRangeResponse,error)                   
    ```

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |TableName|The name of the table.|
    |Direction|The direction to read data.    -   If the value is FORWARD, the value of the start primary key must be smaller than that of the end primary key. The returned rows are sorted in ascending order based on the primary key value.
    -   If the value is BACKWARD, the value of the start primary key must be greater than that of the end primary key. The returned rows are sorted in descending order based on the primary key value.
Example: A table contains two primary keys A and B where the value of A is smaller than that of B. If you set direction to forward, the rows whose primary key column values are greater than or equal to the value of A and smaller than the value of B are returned in ascending order. If you set direction to backward, the rows whose primary key column values are greater than the value of A and smaller than or equal to the value of B are returned in descending order. |
    |StartPrimaryKey|Read data between the start primary key column and the end primary key column. The start and end points of ranges are composed of valid primary keys or virtual points INF\_MIN and INF\_MAX. The number of columns for the virtual point must be the same as that of the valid primary key. INF\_MIN indicates an infinitely small value of which all other values of other types are larger. INF\_MAX indicates an infinitely large value of which all other values are smaller.

    -   StartPrimaryKey indicates the primary key column from which the read starts. If the specified row exists, the row is included in the response.
    -   EndPrimaryKey indicates the primary key column at which the read ends. The end primary key column is excluded from the response even if the row exists.
Rows in tables are sorted based on the primary key column values in ascending order. The range for reading is a left-closed and right-open interval. When data is read in the forward direction, all rows whose primary key column values are greater than or equal to the start primary key column value and smaller than the end primary key column value are returned. |
    |EndPrimaryKey|
    |Limit|The maximum number of rows to return. This value must be greater than 0. An operation stops when the maximum number of rows is returned in a forward or backward direction, even if a part of rows within the specified range are not returned. You can use NextStartPrimaryKey in the response to record the position where the read ends in this operation. This way, the next read can start from this position. |
    |ColumnsToGet|The set of columns to read. The column name can be the primary key column or attribute column. If you do not specify this parameter, all data in the row is returned.

**Note:**

    -   If you query a row of data, the system returns the data in all columns of the row. You can set the ColumnsToGet parameter to read the data only in specified columns. If col0 and col1 are added to ColumnsToGet, only the values of the col0 and col1 columns are returned.
    -   If a row is within the specified range to read based on the primary key value but does not contain the specified columns to return, the response excludes the row.
    -   When ColumnsToGet and Filter are used at the same time, the columns specified by ColumnsToGet are returned. Then, the returned columns are filtered. |
    |MaxVersions|The maximum number of read versions.**Note:** You must specify at least one of MaxVersion and TimeRange.

    -   If you specify only MaxVersion, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If you specify only TimeRange, all data within a range or a version of data is returned.
    -   If you specify both MaxVersion and TimeRange, data of up to the specified number of versions within the time range is returned from the latest to the earliest. |
    |TimeRange|Reads data within a range of versions or a specific version of data. For more information, see [TimeRange](/intl.en-US/API Reference/Data Types/TimeRange.md).**Note:** You must specify at least one of MaxVersion and TimeRange.

    -   If you specify only MaxVersion, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If you specify only TimeRange, all data within a range or a version of data is returned.
    -   If you specify both MaxVersion and TimeRange, data of up to the specified number of versions within the time range is returned from the latest to the earliest.
    -   To query data within a range, you must set Start and End. Start specifies the start timestamp. End indicates the end timestamp. The time range is a left-closed and right-open interval, which is \[Start, End\).
    -   To query data of a specific version, set Specific. Specific specifies a specific timestamp.
You can set one of Specific and \[Start, End\).

Valid values: \[0, INT64.MAX\). Unit: milliseconds. |
    |Filter|Filters the read results on the server side and returns only the rows of data that meet the conditions in the filter. For more information, see [Configure filter](/intl.en-US/SDK Reference/Go SDK/Table operations/Configure filter.md).**Note:** When ColumnsToGet and Filter are used at the same time, the columns specified by ColumnsToGet are returned. Then, the returned columns are filtered. |
    |NextStartPrimaryKey|The start primary key of the next read. The value of NextStartPrimaryKey can be used to determine whether all data is read.     -   If the value of NextStartPrimaryKey is not empty in the response, the NextStartPrimaryKey value can be used as the start primary key for the next GetRange operation.
    -   If the value of NextStartPrimaryKey is empty in the response, all data within the range is returned. |

-   Examples

    The following code provides an example on how to read data within a specified range:

    ```
    getRangeRequest := &tablestore.GetRangeRequest{}
        rangeRowQueryCriteria := &tablestore.RangeRowQueryCriteria{}
        rangeRowQueryCriteria.TableName = tableName
    
        startPK := new(tablestore.PrimaryKey)
        startPK.AddPrimaryKeyColumnWithMinValue("pk1")
        startPK.AddPrimaryKeyColumnWithMinValue("pk2")
        startPK.AddPrimaryKeyColumnWithMinValue("pk3")
        endPK := new(tablestore.PrimaryKey)
        endPK.AddPrimaryKeyColumnWithMaxValue("pk1")
        endPK.AddPrimaryKeyColumnWithMaxValue("pk2")
        endPK.AddPrimaryKeyColumnWithMaxValue("pk3")
        rangeRowQueryCriteria.StartPrimaryKey = startPK
        rangeRowQueryCriteria.EndPrimaryKey = endPK
        rangeRowQueryCriteria.Direction = tablestore.FORWARD
        rangeRowQueryCriteria.MaxVersion = 1
        rangeRowQueryCriteria.Limit = 10
        getRangeRequest.RangeRowQueryCriteria = rangeRowQueryCriteria
    
        getRangeResp, err := client.GetRange(getRangeRequest)
    
        fmt.Println("get range result is " ,getRangeResp)
    
        for {
            if err != nil {
                fmt.Println("get range failed with error:", err)
            }
            for _, row := range getRangeResp.Rows {
                fmt.Println("range get row with key", row.PrimaryKey.PrimaryKeys[0].Value, row.PrimaryKey.PrimaryKeys[1].Value, row.PrimaryKey.PrimaryKeys[2].Value)
            }
            if getRangeResp.NextStartPrimaryKey == nil {
                break
            } else {
                fmt.Println("next pk is :", getRangeResp.NextStartPrimaryKey.PrimaryKeys[0].Value, getRangeResp.NextStartPrimaryKey.PrimaryKeys[1].Value, getRangeResp.NextStartPrimaryKey.PrimaryKeys[2].Value)
                getRangeRequest.RangeRowQueryCriteria.StartPrimaryKey = getRangeResp.NextStartPrimaryKey
                getRangeResp, err = client.GetRange(getRangeRequest)
            }
            fmt.Println("continue to query rows")
        }
        fmt.Println("range get row finished")
    ```

    For the detailed sample code, visit [GetRange@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/MultipleRowOperation.go).


