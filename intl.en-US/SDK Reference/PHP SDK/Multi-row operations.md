# Multi-row operations

Tablestore SDKs provide multi-row operations such as BatchWriteRow, BatchGetRow, and GetRange.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A data table is created. Data is written to the table.

## BatchWriteRow

You can call this operation to write several rows of data to one or more tables by sending one request. The BatchWriteRow operation consists of PutRow, UpdateRow, and DeleteRow operations. The process of constructing a suboperation is the same as that of calling the PutRow, UpdateRow, and DeleteRow operations. BatchWriteRow supports conditional updates.

Each suboperation of BatchWriteRow is independently performed, and the result for each suboperation is independently returned.

-   When multiple rows are written, part of rows may fail to be written. If the operation fails for part of rows, Tablestore does not return exceptions. However, information about the index and error messages of the failed rows is contained in BatchWriteRowResponse. Therefore, when you call the BatchWriteRow operation, you must check the returned row to determine whether the status of each row is successful. If you do not check the returned value, failures of operations on part of rows are ignored.
-   When the server detects incorrect parameters during operations, BatchWriteRow may return a parameter exception when none of the operations are performed for the request.

-   Operations

    ```
    /**
     * Write, update, or delete the specified rows of data.
     * Be aware that if the BatchWriteRow operation fails for part of rows, the system does not return an exception. However, information about the failed rows is contained in $response. For more information, see the sample response to the BatchWriteRow operation.
     * @api
     * @param [] $request The request parameters.
     * @return [] The response.
     * @throws OTSClientException The exception that occurs when a parameter error occurs or the Tablestore server returns a verification error.
     * @throws OTSServerException The exception that occurs when the Tablestore server returns an error.
     */
    public function batchWriteRow(array $request);              
    ```

-   Request parameters

    BatchWriteRow is a combination of PutRow, UpdateRow, and DeleteRow.

    -   Hierarchies are created for tables. Multiple tables can be processed at a time.

        The values of the tables parameter are based on tables. This parameter specifies information about tables and rows on which the write, modification, or delete operations are performed. For more information about parameters, see [Single-row operations](/intl.en-US/SDK Reference/PHP SDK/Single-row operations.md).

    -   The operation\_type parameter is added to distinguish between different operation types.

        You can use OperationTypeConst::CONST\_PUT to set the operation type to PUT, OperationTypeConst::CONST\_UPDATE to UPDATE, and OperationTypeConst::CONST\_DELETE to DELETE.

        -   If the operation type is PUT, primary\_key and attribute\_columns are valid.
        -   If the operation type is UPDATE, primary\_key and update\_of\_attribute\_columns are valid.
        -   If the operation type is DELETE, primary\_key is valid.
-   Request format

    ```
    $result = $client->batchWriteRow([
        'tables' => [                                            // Set the hierarchies of tables.
            [
                'table_name' => '<string>',                      // Set the name of the table.
                'operation_type' => <OperationType>,
                'condition' => [
                    'row_existence' => <RowExistence>,   
                    'column_condition' => <ColumnCondition>
                ],
                'primary_key' => [                               // Set the primary key.
                    ['<string>', <PrimaryKeyValue>], 
                    ['<string>', <PrimaryKeyValue>],
                    ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
                ], 
                'attribute_columns' => [                        // This parameter is required when the operation type is PUT.
                        ['<string>', <ColumnValue>], 
                        ['<string>', <ColumnValue>, <ColumnType>],
                        ['<string>', <ColumnValue>, <ColumnType>, <integer>]
                ],
                'update_of_attribute_columns' => [               // This parameter is required when the operation type is UPDATE.
                    'PUT' => [
                        ['<string>', <ColumnValue>], 
                        ['<string>', <ColumnValue>, <ColumnType>],
                        ['<string>', <ColumnValue>, <ColumnType>, <integer>]
                    ],
                    'DELETE' => [
                        ['<string>', <integer>], 
                        ['<string>', <integer>], 
                        ['<string>', <integer>], 
                        ['<string>', <integer>]
                    ],
                    'DELETE_ALL' => [
                        '<string>',
                        '<string>',
                        '<string>',
                        '<string>'
                    ],
                ],
                'return_content' => [
                    'return_type' => <ReturnType>
                ]
            ],
            // Set other tables.
        ]
    ]);        
    ```

-   Response parameters

    Each table in the tables parameter in the response corresponds to the table specified in the request. The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |table\_name|The name of the table.|
    |is\_ok|Indicates whether the operation for this row was successful.     -   A value of true indicates that the row is written. In this case, the error parameter is invalid.
    -   A value of false indicates that the row fails to be written. |
    |error|The error information in the response when the operation failed.     -   The error code in the response when the operation on the single row failed.
    -   The error message in the response when the operation on the single row failed. |
    |consumed|The number of capacity units \(CUs\) consumed by this operation. capacity\_unit: the number of read and write CUs consumed.

    -   read: the read throughput
    -   write: the write throughput |
    |primary\_key|The value of the primary key, which is consistent with that in the request.If return\_type is set to RT\_PK, values of all primary key columns are returned. return\_type is used for auto-increment of primary key columns. |
    |attribute\_columns|The values of attribute columns. The attribute column names are consistent with those specified in the request. At present, the value of this parameter is empty.|

-   Response format

    ```
    [
        'tables' => [
            [
                'table_name' => '<string>',
                'rows' => [
                    [
                        'is_ok' => true || false,
                        'error' => [
                            'code' => '<string>',
                            'message' => '<string>',
                        ]
                        'consumed' => [
                            'capacity_unit' => [
                                'read' => <integer>,
                                'write' => <integer>
                            ]
                        ],
                        'primary_key' => [ 
                            ['<string>', <PrimaryKeyValue>], 
                            ['<string>', <PrimaryKeyValue>],
                            ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
                        ],
                        'attribute_columns' => []
                    ],
                    // Set other rows.
                ]
            ],
            // Set other tables.
        ]
    ]           
    ```

-   Examples

    The following code provides an example on how to write 30 rows of data to three tables at a time. Each table is written with 10 rows.

    ```
    // Write data to three tables. Each table is written with 10 rows.
    $tables = array();
    for($i = 0; $i < 3; $i++) {
        $rows = array();
        for($j = 0; $j < 10; $j++) {
            $rows[] = [
                'operation_type' => OperationTypeConst::CONST_PUT, // Set the operation type to PUT.
                'condition' => RowExistenceExpectationConst::CONST_IGNORE,
                'primary_key' => [
                    ['pk0', $i],
                    ['pk1', $j]
                ],
                'attribute_columns' => [
                    ['Col0', 4],
                    ['Col2', 'Beijing']
                ]
            ];
        }
        $tables[] = [
            'table_name' => 'SampleTable' . $i,
            'rows' => $rows
        ];
    }
    $request = [
        'tables' => $tables
    ];
    $response = $otsClient->batchWriteRow ($request);
    // Process each returned table.
    foreach ($response['tables'] as $tableData) {
      print "Handling table {$tableData['table_name']} ...\n";
    
      // Process the result returned by PutRow on the table.
      $putRows = $tableData['rows'];
    
      foreach ($putRows as $rowData) {
    
        if ($rowData['is_ok']) {
          // Data is written.
          print "Capacity Unit Consumed: {$rowData['consumed']['capacity_unit']['write']}\n";
        } else {
    
          // Handle errors.
          print "Error: {$rowData['error']['code']} {$rowData['error']['message']}\n";
        }
      }
    }           
    ```

    The following table lists the files that contain detailed sample code.

    |File|Description|
    |:---|:----------|
    |[BatchWriteRow1.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRow1.php)|Shows you how to perform multiple PUT operations in BatchWriteRow.|
    |[BatchWriteRow2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRow2.php)|Shows you how to perform multiple UPDATE operations in BatchWriteRow.|
    |[BatchWriteRow3.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRow3.php)|Shows you how to perform multiple DELETE operations in BatchWriteRow.|
    |[BatchWriteRow4.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRow4.php)|Shows you how to perform the UPDATE, PUT, and DELETE operations in BatchWriteRow.|
    |[BatchWriteRowWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRowWithColumnFilter.php)|Shows you how to use BatchWriteRow with conditional update.|


## BatchGetRow

You can call this operation to read several rows of data from one or more tables by sending one request. The BatchGetRow operation consists of multiple GetRow operations. The process of constructing a suboperation is the same as that of calling the GetRow operation. BatchGetRow supports filters.

Be aware that BatchGetRow uses the same parameter configurations for all rows. For example, if ColumnsToGet is set to colA, only the value of colA is read for all the rows.

The execution and returning of results of each suboperation are carried out independently.

When multiple rows are read, part of rows may fail to be read. If the operation fails for part of rows, the system does not return exceptions. However, information about the error messages of the failed rows is stored in BatchGetRowResponse. Therefore, when you call the BatchGetRow operation, you must check the returned values to determine whether the status of each row is successful.

-   Operations

    ```
    /**
     * Read the specified rows of data.
     * Be aware that if the BatchGetRow operation fails for part of rows, Tablestore does not return an exception. However, information about the failed rows is contained in $response. For more information, see the sample response to the BatchGetRow operation.
     * @api
     * @param [] $request The request parameters.
     * @return [] The response.
     * @throws OTSClientException The exception that occurs when a parameter error occurs or the Tablestore server returns a verification error.
     * @throws OTSServerException The exception that occurs when the Tablestore server returns an error.
     */
    public function batchGetRow(array $request);  
                
    ```

-   Request parameters

    BatchGetRow and GetRow have the following differences:

    -   Hierarchies are created for tables. Data from multiple tables can be read at a time.

        The values of the tables parameter are based on tables. This parameter specifies information about tables and rows on which various operations are performed. For more information about parameters, see [Single-row operations](/intl.en-US/SDK Reference/PHP SDK/Single-row operations.md).

    -   primary\_key is changed to primary\_keys, which allows you to set primary keys for multiple rows and read data from multiple rows at a time.
-   Request format

    ```
    $result = $client->batchGetRow([
        'tables' => [                                            // Set the hierarchies of tables.
            [
                'table_name' => '<string>',                      // Set the name of the table.
                'primary_keys' => [                              // Set the primary key.
                    [
                        ['<string>', <PrimaryKeyValue>], 
                        ['<string>', <PrimaryKeyValue>],
                        ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
                    ], 
                    // Set other primary keys.
                ]
                'max_versions' => <integer>,
                'time_range' => [
                    'start_time' => <integer>,
                    'end_time' => <integer>,
                    'specific_time' => <integer>
                ],
                'start_column' => '<string>',
                'end_column' => '<string>',
                'token' => '<string>',
                'columns_to_get' => [
                    '<string>',
                    '<string>',
                    //...   
                ],
                'column_filter' =>  <ColumnCondition>
            ],
            // Set other data tables.
        ]
    ]);
    
                
    ```

-   Response parameters

    Each table in the tables parameter in the response corresponds to the table specified in the request. The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |table\_name|The name of the table.|
    |is\_ok|Indicates whether the operation for this row was successful.     -   A value of true indicates that the row is read. In this case, the error parameter is invalid.
    -   A value of false indicates that the row fails to be read. In this case, consumed, primary\_key, and attribute\_columns become invalid. |
    |error|The error information in the response when the operation failed.     -   The error code in the response when the operation on the single row failed.
    -   The error message in the response when the operation on the single row failed. |
    |consumed|The number of CUs consumed by this operation. capacity\_unit: the number of read and write CUs consumed.

    -   read: the read throughput
    -   write: the write throughput |
    |primary\_key|The value of the primary key, which is consistent with that in the request.**Note:** If the row does not exist, primary\_key is an empty list \[\]. |
    |attribute\_columns|The values of attribute columns.**Note:** If the row does not exist, attribute\_columns is an empty list \[\].

    -   Each attribute column is specified by parameters in the following sequence: the attribute column name, attribute column value \(ColumnValue\), attribute column value type \(ColumnType\), and timestamp.

A timestamp is a 64-bit integer, which indicates a version of data.

    -   The attribute columns in the response are sorted by attribute column name in ascending alphabetical order. The versions of the attribute columns are sorted by timestamp in descending order.
    -   The order of attribute columns may be different between the request and response. |
    |next\_token|The start position of a wide row in the next read operation. This feature is unavailable.|

-   Response format

    ```
    [
        'tables' => [
            [
                'table_name' => '<string>',
                'rows' => [
                    [
                        'is_ok' => true || false,
                        'error' => [
                            'code' => '<string>',
                            'message' => '<string>',
                        ]
                        'consumed' => [
                            'capacity_unit' => [
                                'read' => <integer>,
                                'write' => <integer>
                            ]
                        ],
                        'primary_key' => [ 
                            ['<string>', <PrimaryKeyValue>], 
                            ['<string>', <PrimaryKeyValue>],
                            ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
                        ],  
                        'attribute_columns' => [
                                ['<string>', <ColumnValue>, <ColumnType>, <integer>]
                                ['<string>', <ColumnValue>, <ColumnType>, <integer>]
                                ['<string>', <ColumnValue>, <ColumnType>, <integer>]
                        ],
                        'next_token' => '<string>'
                    ],
                    // Set other rows.
                ]
            ],
            // Set other tables.
        ]
    ]
                
    ```

-   Examples

    The following code provides an example on how to read 30 rows of data from three tables at a time. 10 rows of data are read from each table.

    ```
    // Read data from three tables. Read 10 rows of data from each table.
    $tables = array();
    for($i = 0; $i < 3; $i++) {
        $primary_keys = array();
        for($j = 0; $j < 10; $j++) {
            $primary_keys[] = [
                ['pk0', $i],
                ['pk1', $j]
            ];
        }
        $tables[] = [
            'table_name' => 'SampleTable' . $i,
            'max_versions' => 1,
            'primary_keys' => $primary_keys
        ];
    }
    $request = [
        'tables' => $tables
    ];
    $response = $otsClient->batchGetRow ($request);
    
    // Process each returned table.
    foreach ($response['tables'] as $tableData) {
      print "Handling table {$tableData['table_name']} ...\n";
    
      // Process each row of data in the table.
      foreach ($tableData['rows'] as $rowData) {
    
        if ($rowData['is_ok']) {
    
          // Process the data that is read.
            $row = json_encode($rowData['primary_key']);
            print "Handling row: {$row}\n";
    
        } else {
    
          // Handle errors.
          print "Error: {$rowData['error']['code']} {$rowData['error']['message']}\n";
        }
      }
    }            
    ```

    The following table lists the files that contain detailed sample code.

    |File|Description|
    |:---|:----------|
    |[BatchGetRow1.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRow1.php)|Shows you how to use BatchGetRow to read multiple rows from a table.|
    |[BatchGetRow2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRow2.php)|Shows you how to use BatchGetRow to read multiple rows from multiple tables.|
    |[BatchGetRow3.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRow3.php)|Shows you how to use BatchGetRow to read specified columns for multiple rows from a table.|
    |[BatchGetRow4.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRow4.php)|Shows you how to use BatchGetRow to process returned results.|
    |[BatchGetRowWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRowWithColumnFilter.php)|Show you how to use BatchGetRow with filters.|


## GetRange

You can call this operation to read rows of data in a forward or backward direction based on a specified primary key range.

You can also set the number of rows you want to read. If the range is large and the number of scanned rows or the volume of data exceeds the limit, the scan stops, and the read rows and the next primary key information are returned. You can also initiate a request to start from where the last operation left off and read the remaining rows based on the next primary key information returned by the previous operation.

The GetRange operation may stop and return data in the following situations:

-   The amount of read data reaches 4 MB.
-   The number of read rows reaches 5,000.
-   The number of returned rows reaches the limit.
-   All reserved read throughput is consumed. You have no reserved read throughput to read the next row of data.

**Note:** In Tablestore tables, all rows are sorted by primary key. The primary key of a table sequentially consists of all primary key columns. Therefore, do not assume that the rows are sorted based on a specific primary key column.

-   Operations

    ```
    /**
     * Read data between the start and the end primary key columns.
     * Be aware that the server may truncate this range. You need to determine whether to call the GetRange operation again based on the next_start_primary_key value in the response.
     * You can specify the maximum number of rows to read.
     * When you specify the start and end primary key columns, you can use INF_MIN to indicate the minimum value and INF_MAX the maximum value. For more information, see the following code samples:
     * @api
     * @param [] $request The request parameters.
     * @return [] The response.
     * @throws OTSClientException The exception that occurs when a parameter error occurs or the Tablestore server returns a verification error.
     * @throws OTSServerException The exception that occurs when the Tablestore server returns an error.
     */
    public function getRange(array $request);            
    ```

-   Parameters

    GetRange is different from GetRow in the following items:

    -   The primary\_key parameter is changed to the inclusive\_start\_primary\_key and exclusive\_end\_primary\_key parameters. The range is a left-closed and right-open interval.
    -   The direction parameter is added to specify the direction for reading.
    -   The limit parameter is added to limit the number of returned rows.
    |Parameter|Description|
    |---------|-----------|
    |table\_name|The name of the table.|
    |inclusive\_start\_primary\_key|The start and end primary key columns of the read operation. The start and end primary key columns must be valid primary key columns or virtual points of the INF\_MIN or INF\_MAX type. The number of columns for the virtual point must be the same as that of the primary key. INF\_MIN indicates an infinitely small value of which all other values of other types are larger. INF\_MAX indicates an infinitely large value of which all other values of other types are smaller.

    -   inclusive\_start\_primary\_key specifies the start primary key. If the row exists, the response contains this row.
    -   exclusive\_end\_primary\_key specifies the end primary key. The response excludes the row no matter whether the row exists.
Rows in tables are sorted based on the primary key column values in ascending order. The range for reading is a left-closed and right-open interval. When data is read in the forward direction, all rows whose primary key column values are greater than or equal to the start primary key column value and smaller than the end primary key column value are returned. |
    |exclusive\_end\_primary\_key|
    |direction|The direction in which to read data.    -   If DirectionConst::CONST\_FORWARD is set, the start primary key column value must be smaller than the end primary key column value. The rows are returned based on the primary key column values in ascending order.
    -   If DirectionConst::CONST\_BACKWARD is set, the start primary key column value must be greater than the end primary key column value. The rows are returned based on the primary key column values in descending order.
Example: A table contains two primary keys A and B where the value of A is smaller than that of B. If you set direction to forward, the rows whose primary key values are greater than or equal to the value of A and smaller than the value of B are returned in ascending order. If you set direction to backward, the rows whose primary key values are greater than or equal to the value of B and smaller than the value of A are returned in descending order. |
    |limit|The maximum number of rows that can be returned. The value of this parameter must be greater than 0. An operation stops when the maximum number of rows are returned in the forward or backward direction, even if part of rows within the specified range are not returned. At this time, you can record the next\_start\_primary\_key value in the response for the next reading. |
    |max\_versions|The maximum number of versions that can be read.**Note:** You must specify at least one of max\_version and time\_range.

    -   If you specify only max\_version, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If you specify only time\_range, data whose version number is within the specified range or a specified version of data is returned.
    -   If you specify both max\_version and time\_range, data of up to the specified number of versions within the time range is returned from the latest to the earliest. |
    |time\_range|Specifies a range of versions to read or a version of data to read. For more information, see [TimeRange](/intl.en-US/API Reference/Data Types/TimeRange.md).**Note:** You must specify at least one of max\_version and time\_range.

    -   If you specify only max\_version, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If you specify only time\_range, data whose version number is within the specified range or a specified version of data is returned.
    -   If you specify both max\_version and time\_range, data of up to the specified number of versions within the time range is returned from the latest to the earliest.
    -   If you specify a range of versions to read, you must set start\_time and end\_time. start\_time indicates the start timestamp. end\_time indicates the end timestamp. The specified range includes the start timestamp and excludes the end timestamp.
    -   If you want to query data of a specified version, you must set specific\_time. specific\_time indicates a specified timestamp.
You can set either of specific\_time or \[start\_time, end\_time\).

The timestamp in the value of time\_range ranges from 0 to INT64.MAX. |
    |columns\_to\_get|The names of the columns to read. You can specify the names of primary key columns and the names of attribute columns. If you do not specify this parameter, all data in the row is returned.

**Note:**

    -   By default, Tablestore returns the data from all columns of the row when you query a row. You can set the columns\_to\_get parameter to return the data in specified columns. For example, if you include col0 and col1 in columns\_to\_get, only the values of col0 and col1 are returned.
    -   If a row is within the specified range of primary key values to read but does not contain the specified columns to return, the row is not included in the response.
    -   If you use columns\_to\_get and column\_filter together, Tablestore first queries the columns specified by columns\_to\_get, and then returns rows that meet the filter conditions. |
    |start\_column|The column from which you want to start reading. This parameter is used to read wide rows. The response contains the current start column.The columns are sorted based on their names in alphabetical order. Example: A table contains columns a, b, and c. If the value of start\_column is b, the reading starts from column b, and columns b and c are returned. |
    |end\_column|The column at which you want to end reading. This parameter is used to read wide rows. The response does not contain the current end column.The columns are sorted based on their names in alphabetical order. Example: A table contains columns a, b, and c. If the value of end\_column is b, the reading ends at column b, and column a is returned. |
    |token|The start position of a wide row in the next read operation. This feature is unavailable.|
    |column\_filter|You can set filter conditions to filter the queried results on the server side. Only rows that meet the specified filter conditions are returned. For more information, see [Filter](/intl.en-US/SDK Reference/Python SDK/Table/Filter.md).**Note:** If you use columns\_to\_get and column\_filter together, Tablestore first queries the columns specified by columns\_to\_get, and then returns rows that meet the filter conditions. |

-   Request format

    ```
    $result = $client->getRange([
        'table_name' => '<string>',                                     // Set the name of the table.
        'inclusive_start_primary_key' => [                              // Set the start primary key.
            ['<string>', <PrimaryKeyValue>], 
            ['<string>', <PrimaryKeyValue>],
            ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
        ], 
        'exclusive_end_primary_key' => [                                // Set the end primary key.
            ['<string>', <PrimaryKeyValue>], 
            ['<string>', <PrimaryKeyValue>],
            ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
        ], 
        'direction' => <Direction>,                                     // Set the direction for reading.
        'limit' => <Direction>,
        'max_versions' => <integer>,
        'time_range' => [
            'start_time' => <integer>,
            'end_time' => <integer>,
            'specific_time' => <integer>
        ],
        'start_column' => '<string>',
        'end_column' => '<string>',
        'token' => '<string>',
        'columns_to_get' => [
            '<string>',
            '<string>',
            //...   
        ],
        'column_filter' =>  <ColumnCondition>
    ]);      
    ```

-   Response parameters

    |Parameter|Description|
    |---------|-----------|
    |consumed|The number of CUs consumed by this operation. capacity\_unit: the number of read and write CUs consumed.

    -   read: the read throughput
    -   write: the write throughput |
    |primary\_key|The value of the primary key, which is consistent with that in the request.|
    |attribute\_columns|The values of attribute columns.    -   Each attribute column is specified by parameters in the following sequence: the attribute column name, attribute column value \(ColumnValue\), attribute column value type \(ColumnType\), and timestamp.

A timestamp is a 64-bit integer, which indicates a version of data. Unit: milliseconds.

    -   The attribute columns in the response are sorted by attribute column name in ascending alphabetical order. The versions of the attribute columns are sorted by timestamp in descending order.
    -   The order of attribute columns may be different between the request and response. |
    |next\_start\_primary\_key|The start primary key for the next reading. The value of next\_start\_primary\_key can be used to determine whether all data is read.     -   If the value of next\_start\_primary\_key is not empty in the response, the next\_start\_primary\_key value can be used as the start primary key for the next GetRange operation.
    -   If the value of next\_start\_primary\_key is empty in the response, all data within the range is returned. |

-   Response format

    ```
    [
        'consumed' => [
            'capacity_unit' => [
                'read' => <integer>,
                'write' => <integer>
            ]
        ],
        'next_start_primary_key' => [ 
            ['<string>', <PrimaryKeyValue>], 
            ['<string>', <PrimaryKeyValue>],
            ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
        ], 
        'rows' => [
            [
                'primary_key' => [ 
                    ['<string>', <PrimaryKeyValue>], 
                    ['<string>', <PrimaryKeyValue>],
                    ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
                ],  
                'attribute_columns' => [
                        ['<string>', <ColumnValue>, <ColumnType>, <integer>]
                        ['<string>', <ColumnValue>, <ColumnType>, <integer>]
                        ['<string>', <ColumnValue>, <ColumnType>, <integer>]
                ]
            ],
            // Set other rows.
        ]
    ]
                
    ```

-   Examples

    The following code provides an example on how to read data within a specified range:

    ```
    // Search for rows whose PK0 value is within [1, 4).
    // You must provide complete primary key columns to set the range. If the query range does not involve the range of a column value, set the column value to an infinitely great (INF_MAX) or small (INF_MIN) value.
    $startPK = [
        ['PK0', 1], 
        ['PK1', null, PrimaryKeyTypeConst::CONST_INF_MIN]  // INF_MIN indicates the minimum value.
    ];
    // You must provide complete primary key columns to set the range. If the query range does not involve the range of a column value, set the column value to an infinitely great (INF_MAX) or small (INF_MIN) value.
    $endPK = [
        ['PK0', 4], 
        ['PK1', null, PrimaryKeyTypeConst::CONST_INF_MAX]  // INF_MAX indicates the maximum value.
    ];
    $request = [
        'table_name' => 'SampleTable',
        'max_versions' => 1,                          // Set this parameter to read the latest version.
        'direction' => DirectionConst::CONST_FORWARD, // Query data in the forward direction.
        'inclusive_start_primary_key' => $startPK,    // Set the start primary key.
        'exclusive_end_primary_key' => $endPK,        // Set the end primary key.
        'limit' => 10                                 // Specify that a maximum of 10 columns are returned.
    ];
    $response = $otsClient->getRange ($request);
    print "Read CU Consumed: {$response['consumed']['capacity_unit']['read']}\n";
    
    foreach ($response['rows'] as $rowData) {
      // Process each row of data.
    }           
    ```

    The following table lists the files that contain detailed sample code.

    |File|Description|
    |:---|:----------|
    |[GetRange1.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRange1.php)|Shows you how to use GetRange.|
    |[GetRange2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRange2.php)|Shows you how to use GetRange to obtain the specified columns.|
    |[GetRange3.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRange3.php)|Shows you how to use GetRange to obtain the specified number of rows.|
    |[GetRangeWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRangeWithColumnFilter.php)|Shows you how you use GetRange with filter.|


