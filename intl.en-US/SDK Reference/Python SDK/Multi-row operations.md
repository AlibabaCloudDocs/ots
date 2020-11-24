# Multi-row operations

Tablestore SDKs provide multi-row operations such as BatchWriteRow, BatchGetRow, and GetRange.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A data table is created. Data is written to the table.

## BatchWriteRow

You can call this operation to write several rows of data to one or more tables by sending one request. The BatchWriteRow operation consists of PutRow, UpdateRow, and DeleteRow operations. The process of constructing a suboperation is the same as that of calling the PutRow, UpdateRow, and DeleteRow operations. BatchWriteRow supports conditional updates.

The execution and returning of results of each suboperation are carried out independently.

When multiple rows are written, part of rows may fail to be written. If the operation fails for part of rows, the system does not return exceptions. However, information about the index and error messages of the failed rows is contained in BatchWriteRowResponse. Therefore, when you call the BatchWriteRow operation, you must check the return values and check whether the status of each row is successful. If you do not check the return values, failures of operations on part of rows are ignored.

When the server detects errors caused by operations, the BatchWriteRow operation may return exceptions of parameter errors. All operations for the request are not executed.

-   Operations

    ```
    """
    Description: This operation writes data to multiple rows.
    request = MiltiTableInBatchWriteRowItem()
    request.add(TableInBatchWriteRowItem(table0, row_items))
    request.add(TableInBatchWriteRowItem(table1, row_items))
    response = client.batch_write_row(request)
    response indicates the returned result. Type: tablestore.metadata.BatchWriteRowResponse.
    """
    def batch_write_row(self, request):                   
    ```

-   Parameters

    For more information about parameters, see [Single-row operations](/intl.en-US/SDK Reference/Python SDK/Single-row operations.md).

-   Examples

    The following code provides an example on how to write multiple rows of data:

    ```
    put_row_items = []
    # Add rows for PutRow.
    for i in range(0, 10):
        primary_key = [('gid',i), ('uid',i+1)]
        attribute_columns = [('name','somebody'+str(i)), ('address','somewhere'+str(i)), ('age',i)]
        row = Row(primary_key, attribute_columns)
        condition = Condition(RowExistenceExpectation.IGNORE)
        item = PutRowItem(row, condition)
        put_row_items.append(item)
    
    # Add rows for UpdateRow.
    for i in range(10, 20):
        primary_key = [('gid',i), ('uid',i+1)]
        attribute_columns = {'put': [('name','somebody'+str(i)), ('address','somewhere'+str(i)), ('age',i)]}
        row = Row(primary_key, attribute_columns)
        condition = Condition(RowExistenceExpectation.IGNORE, SingleColumnCondition("age", i, ComparatorType.EQUAL))
        item = UpdateRowItem(row, condition)
        put_row_items.append(item)
    
    # Add rows for DeleteRow.
    delete_row_items = []
    for i in range(10, 20):
        primary_key = [('gid',i), ('uid',i+1)]
        row = Row(primary_key)
        condition = Condition(RowExistenceExpectation.IGNORE)
        item = DeleteRowItem(row, condition)
        delete_row_items.append(item)
    
    # Construct a request to write multiple rows.
    request = BatchWriteRowRequest()
    request.add(TableInBatchWriteRowItem(table_name, put_row_items))
    request.add(TableInBatchWriteRowItem('notExistTable', delete_row_items))
    
    # When you call the batch_write_row method to write multiple rows, exceptions may occur if errors such as request parameter errors occur. If the operation fails for part of rows, an exception may not occur but the internal items may fail.
    try:
        result = client.batch_write_row(request)
        print ('Result status: %s'%(result.is_all_succeed()))
    
        # Check the result of inserting rows.
        print ('check first table\'s put results:')
        succ, fail = result.get_put()
        for item in succ:
            print ('Put succeed, consume %s write cu.' % item.consumed.write)
        for item in fail:
            print ('Put failed, error code: %s, error message: %s' % (item.error_code, item.error_message))
    
        # Check the result of updating rows.
        print ('check first table\'s update results:')
        succ, fail = result.get_update()
        for item in succ:
            print ('Update succeed, consume %s write cu.' % item.consumed.write)
        for item in fail:
            print ('Update failed, error code: %s, error message: %s' % (item.error_code, item.error_message))
    
        # Check the result of deleting rows.
        print ('check second table\'s delete results:')
        succ, fail = result.get_delete()
        for item in succ:
            print ('Delete succeed, consume %s write cu.' % item.consumed.write)
        for item in fail:
            print ('Delete failed, error code: %s, error message: %s' % (item.error_code, item.error_message)) 
    # Client exceptions are caused by parameter errors or network exceptions.
    except OTSClientError as e:
        print "get row failed, http_status:%d, error_message:%s" % (e.get_http_status(), e.get_error_message())
    # Server exceptions are caused by parameter or throttling errors.
    except OTSServiceError as e:
        print "get row failed, http_status:%d, error_code:%s, error_message:%s, request_id:%s" % (e.get_http_status(), e.get_error_code(), e.get_error_message(), e.get_request_id())                    
    ```

    For the detailed sample code, visit [BatchWriteRow@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/batch_write_row.py).


## BatchGetRow

You can call this operation to read several rows of data from one or more tables by sending one request. The BatchGetRow operation consists of multiple GetRow operations. The process of constructng a suboperation is the same as that of calling the GetRow operation. BatchGetRow supports filters.

Note that BatchGetRow uses the same parameter configurations for all rows. For example, if ColumnsToGet is set to \[colA\], only the value of colA is read for all the rows.

The execution and returning of results of each suboperation are carried out independently.

When multiple rows are read, part of rows may fail to be read. If the operation fails for part of rows, the system does not return exceptions. However, information about the error messages of the failed rows is contained in BatchGetRowResponse. Therefore, when you call the BatchGetRow operation, you must check the return values and check whether the status of each row is successful.

-   Operations

    ```
    """
    Description: This operation reads the specified rows of data.
    request = BatchGetRowRequest()
    request.add(TableInBatchGetRowItem(myTable0, primary_keys, column_to_get=None, column_filter=None))
    request.add(TableInBatchGetRowItem(myTable1, primary_keys, column_to_get=None, column_filter=None))
    request.add(TableInBatchGetRowItem(myTable2, primary_keys, column_to_get=None, column_filter=None))
    request.add(TableInBatchGetRowItem(myTable3, primary_keys, column_to_get=None, column_filter=None))
    response = client.batch_get_row(request)
    response: the response to the request. Type: tablestore.metadata.BatchGetRowResponse.
    """
    def batch_get_row(self, request):                    
    ```

-   Parameters

    For information about parameters, see [Single-row operations](/intl.en-US/SDK Reference/Python SDK/Single-row operations.md).

-   Examples

    The following code provides an example on how to read three rows of data at a time:

    ```
    # Specify the columns you want to return.
    columns_to_get = ['name', 'mobile', 'address', 'age']
    
    # Read three rows of data.
    rows_to_get = [] 
    for i in range(0, 3):
        primary_key = [('gid',i), ('uid',i+1)]
        rows_to_get.append(primary_key)
    
    # Configure filter conditions. Set name to John and address to China.
    cond = CompositeColumnCondition(LogicalOperator.AND)
    cond.add_sub_condition(SingleColumnCondition("name", "John", ComparatorType.EQUAL))
    cond.add_sub_condition(SingleColumnCondition("address", 'China', ComparatorType.EQUAL))
    
    # Construct a request to read multiple rows.
    request = BatchGetRowRequest()
    
    # Add rows to be read from table_name. The 1 parameter specifies that the latest version is read.
    request.add(TableInBatchGetRowItem(table_name, rows_to_get, columns_to_get, cond, 1))
    
    # Add rows to be read from notExistTable.
    request.add(TableInBatchGetRowItem('notExistTable', rows_to_get, columns_to_get, cond, 1))
    
    try:
           result = client.batch_get_row(request) 
        print ('Result status: %s'%(result.is_all_succeed()))
    
        table_result_0 = result.get_result_by_table(table_name)
        table_result_1 = result.get_result_by_table('notExistTable')
        print ('Check first table\'s result:')
        for item in table_result_0: 
            if item.is_ok:
                print ('Read succeed, PrimaryKey: %s, Attributes: %s' % (item.row.primary_key, item.row.attribute_columns))
            else:
                print ('Read failed, error code: %s, error message: %s' % (item.error_code, item.error_message))
        print ('Check second table\'s result:')
        for item in table_result_1:
            if item.is_ok:
                print ('Read succeed, PrimaryKey: %s, Attributes: %s' % (item.row.primary_key, item.row.attribute_columns))
            else: 
                print ('Read failed, error code: %s, error message: %s' % (item.error_code, item.error_message))
    # Client exceptions are caused by parameter errors or network exceptions.
    except OTSClientError as e:
        print "get row failed, http_status:%d, error_message:%s" % (e.get_http_status(), e.get_error_message())
    # Server exceptions are caused by parameter or throttling errors.
    except OTSServiceError as e:
        print "get row failed, http_status:%d, error_code:%s, error_message:%s, request_id:%s" % (e.get_http_status(), e.get_error_code(), e.get_error_message(), e.get_request_id())                   
    ```

    For the detailed sample code, visit [BatchGetRow@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/batch_get_row.py).


## GetRange

You can call this operation to read rows of data in a forward or backward direction based on a specified primary key range.

You can also set the number of rows you want to read. If the range is large and the number of scanned rows or the volume of data exceeds the limit, the scan stops, and the read rows and the next primary key information are returned. You can also initiate a request to start from where the last operation left off and read the remaining rows based on the next primary key information returned by the previous operation.

The GetRange operation may stop and return data in the following situations:

-   The amount of data scanned reaches 4 MB.
-   The number of rows read reaches 5,000.
-   The number of rows returned reaches the limit.
-   All reserved read throughput is consumed. You have no reserved read throughput to read the next row of data.

**Note:** In Tablestore tables, all rows are sorted by primary key. The primary key of a table sequentially consists of all primary key columns. Therefore, do not assume that the rows are sorted based on a certain primary key column.

-   Operations

    ```
    """
    Description: This operation reads data from multiple rows within the specified range.
    table_name specifies the name of the table.
    direction: the order for this query. Data type: string. Valid values: FORWARD and BACKWARD.
    inclusive_start_primary_key: the primary key from which the read starts. If the specified row exists, the row is included in the response.
    exclusive_end_primary_key: the primary key column at which the read ends. The end primary key column is excluded from the response even if the row exists.
    columns_to_get: optional. This parameter specifies the names of columns you want to read. If you do not specify this parameter, all columns are read. Type: list.
    limit: optional. This parameter specifies the maximum number of rows you want to read. If you do not specify this parameter, all rows are read.
    column_filter: optional. This parameter specifies the filter conditions for columns. Only rows that meet the condition are returned.
    max_version is optional. This parameter specifies the maximum number of versions that can be read. You must specify at least one of max_version and time_range.
    time_range: optional. This parameter specifies the range of versions from which data is read. You must specify at least one of max_version and time_range.
    start_column: optional. This parameter specifies the column from which the read starts, which is used to read wide rows.
    end_column: optional. This parameter specifies the column at which the read ends, which is used to read wide rows.
    token: optional. This parameter specifies the start column of a wide row you want to read next time. The value of this parameter is returned by the last read request, and encoded as binary data.
    
    Response: the results that meet the conditions.
    consumed: the number of CUs consumed by this operation. This parameter specifies an instance of the tablestore.metadata.CapacityUnit class.
    next_start_primary_key indicates the primary key from which you want to start the next get_range operation. Type: dict.
    row_list indicates the list of returned rows of data. Format: [Row, ...].  
    """
    def get_range(self, table_name, direction,
                 inclusive_start_primary_key,
                 exclusive_end_primary_key,
                 columns_to_get=None,
                 limit=None,
                 column_filter=None,
                 max_version=None,
                 time_range=None,
                 start_column=None,
                 end_column=None,
                 token = None):                   
    ```

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |table\_name|The name of the table.|
    |direction|The direction for reading.    -   If the direction is set to FORWARD, the start primary key column value must be smaller than the end primary key column value. The rows are returned based on the primary key column values in ascending order.
    -   If the direction is set to BACKWARD, the start primary key column value must be greater than the end primary key column value. The rows are returned based on the primary key column values in descending order.
Example: A table has two primary keys A and B where the value of A is smaller than that of B. If you set direction to forward, the rows whose primary key column values are greater than or equal to the value of A and smaller than the value of B are returned in ascending order. If you set direction to backward, the rows whose primary key column values are greater than the value of A and smaller than or equal to the value of B are returned in descending order. |
    |inclusive\_start\_primary\_key|The start and end primary keys of the read operation. The start and end primary keys must be valid primary keys or virtual points of the INF\_MIN or INF\_MAX type. The number of columns for the virtual point must be the same as that of the primary key. INF\_MIN indicates an infinitely small value of which all other values of other types are greater. INF\_MAX indicates an infinitely large value of which all other values are smaller.

    -   inclusive\_start\_primary\_key specifies the start primary key. If the row exists, the response contains this row.
    -   exclusive\_end\_primary\_key specifies the end primary key. The response excludes the row no matter whether the row exists.
Rows in tables are sorted based on the primary key column values in ascending order. The range for reading is a left-closed and right-open interval. When data is read in the forward direction, all rows whose primary key column values are greater than or equal to the start primary key column value and smaller than the end primary key column value are returned. |
    |exclusive\_end\_primary\_key|
    |limit|The maximum number of returned rows. This parameter value must be greater than 0. An operation stops when the maximum number of rows is returned in the forward or backward direction, even if part of rows within the specified range are not returned. At this time, you can record the next\_start\_primary\_key value in the response for the next reading. |
    |columns\_to\_get|The collection of columns you want to read. The columns can be primary key columns or attribute columns. If this parameter is not set, rows that contain all columns are returned.

**Note:**

    -   If you query a row of data, the system returns the data in all columns of the row. You can set the columns\_to\_get parameter to return the data in specified columns. If you add col0 and col1 to columns\_to\_get, only the col0 and col1 values are returned.
    -   If the primary key set for a row is within the specified range, but the row does not contain the specified columns, the response does not contain the row of data.
    -   If columns\_to\_get and column\_filter are used at the same time, the system first obtains the columns specified by columns\_to\_get and then filters the returned columns. |
    |max\_version|The maximum number of versions you want to read.**Note:** You must specify at least one of max\_version and time\_range.

    -   If you specify only max\_version, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If you specify only time\_range, all data within a range or a version of data is returned.
    -   If you specify both max\_version and time\_range, data of up to the specified number of versions within the time range is returned from the latest to the earliest. |
    |time\_range|Reads data within a range of versions or a version of data. For more information, see [TimeRange](/intl.en-US/API Reference/Data Types/TimeRange.md).**Note:** You must specify at least one of max\_version and time\_range.

    -   If you specify only max\_version, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If you specify only time\_range, all data within a range or a version of data is returned.
    -   If you specify both max\_version and time\_range, data of up to the specified number of versions within the time range is returned from the latest to the earliest.
    -   To query data within a range, you must set start\_time and end\_time. start\_time specifies the start timestamp. end\_time specifies the end timestamp. The time range is a left-closed and right-open interval, which is \[start\_time, end\_time\).
    -   To query data within a range of versions, set specific\_time. specific\_time specifies a specific timestamp.
Only one of specific\_time and \[start\_time, end\_time\) is required.

Valid values: \[0, INT64.MAX\). Unit: milliseconds. |
    |column\_filter|Filters read results on the server to return rows that meet the conditions in the filter. For more information, see [Configure filter](/intl.en-US/SDK Reference/Python SDK/Table operations/Configure filter.md).**Note:** If columns\_to\_get and column\_filter are used at the same time, the system first obtains the columns specified by columns\_to\_get and then filters the returned columns. |
    |next\_start\_primary\_key|The start primary key for the next reading. The value of next\_start\_primary\_key can be used to determine whether all data is read.     -   If the value of next\_start\_primary\_key is not empty in the response, the next\_start\_primary\_key value can be used as the start primary key for the next GetRange operation.
    -   If the value of next\_start\_primary\_key is empty in the response, all data within the range is returned. |

-   Examples

    The following code provides an example on how to read data within a specified range:

    ```
    # Specify the start primary key.
    inclusive_start_primary_key = [('uid', INF_MIN), ('gid', INF_MIN)]
    
    # Specify the end primary key.
    exclusive_end_primary_key = [('uid', INF_MAX), ('gid', INF_MAX)]
    
    # Query all columns.
    columns_to_get = []
    
    # Set limit to 90 to return a maximum of 90 rows of data. If a total of 100 rows are to be returned, you can set limit to 90 in the first query. The next_start_primary_key value is not None.
    limit = 90
    
    # Configure the filter conditions.
    cond = CompositeColumnCondition(LogicalOperator.AND)
    cond.add_sub_condition(SingleColumnCondition("address", 'China', ComparatorType.EQUAL))
    cond.add_sub_condition(SingleColumnCondition("age", 50, ComparatorType.LESS_THAN))
    
    try:
        # Call the get_range operation.
        consumed, next_start_primary_key, row_list, next_token = client.get_range(
            table_name, Direction.FORWARD,
            inclusive_start_primary_key, exclusive_end_primary_key,
            columns_to_get,
            limit,
            column_filter=cond,
            max_version=1
        time_range = (1557125059000, 1557129059000) # The start_time value is equal to or greater than 1557125059000. The end_time value is smaller than 1557129059000.
        )
    
        all_rows = []
        all_rows.extend(row_list)
    
        # If the next_start_primary_key value is not None, the read continues until all remaining data is read.
        while next_start_primary_key is not None:
            inclusive_start_primary_key = next_start_primary_key
            consumed, next_start_primary_key, row_list, next_token = client.get_range(
                table_name, Direction.FORWARD,
                inclusive_start_primary_key, exclusive_end_primary_key,
                columns_to_get, limit,
                column_filter=cond,
                max_version=1
            )
            all_rows.extend(row_list)
    
        # Display the primary key and attribute columns.
        for row in all_rows:
            print(row.primary_key, row.attribute_columns)
        print('Total rows: ', len(all_rows))
    # Client exceptions are caused by parameter errors or network exceptions.
    except OTSClientError as e:
        print
        "get row failed, http_status:%d, error_message:%s" % (e.get_http_status(), e.get_error_message())
    # Server exceptions are caused by parameter or throttling errors.
    except OTSServiceError as e:
        print
        "get row failed, http_status:%d, error_code:%s, error_message:%s, request_id:%s" % (e.get_http_status(), e.get_error_code(), e.get_error_message(), e.get_request_id())
    ```

    For the detailed sample code, visit [GetRange@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/get_range.py).


