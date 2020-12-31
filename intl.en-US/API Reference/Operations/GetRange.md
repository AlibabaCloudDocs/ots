# GetRange

You can call this operation to read data whose primary keys are within a specified range.

## Request structure

```language-protobuf
message GetRangeRequest {
    required string table_name = 1;
    required Direction direction = 2;
    repeated string columns_to_get = 3;  // If this parameter is not specified, all columns are read.
    optional TimeRange time_range = 4;
    optional int32 max_versions = 5;
    optional int32 limit = 6;
    required bytes inclusive_start_primary_key = 7; // Encode the parameter value in the PlainBuffer format to obtain binary data.
    required bytes exclusive_end_primary_key = 8; // Encode the parameter value in the PlainBuffer format to obtain binary data.
    optional bytes filter = 10;
    optional string start_column = 11;
    optional string end_column = 12;
}
```

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|table\_name|string|Yes|This parameter specifies the name of the table that contains the data to read. |
|direction|[Direction](/intl.en-US/API Reference/Data Types/Direction.md)|Yes|This parameter specifies the query direction.

-   If the query direction is forward, the value of inclusive\_start\_primary must be smaller than that of exclusive\_end\_primary, and the rows in the response are sorted in ascending order of primary key values.
-   If the query direction is backward, the value of inclusive\_start\_primary must be greater than that of exclusive\_end\_primary, and the rows in the response are sorted in descending order of primary key values. |
|columns\_to\_get|repeated string|No|-   This parameter specifies the names of all columns to return. If the parameter is not configured, all columns of each row in the read results are returned.

-   If a duplicate column name exists, the response only includes this column once.

-   The maximum number of strings that can be specified in columns\_to\_get is 128. |
|time\_range|[TimeRange](/intl.en-US/API Reference/Data Types/TimeRange.md)|Either max\_versions or time\_range is required|-   This parameter specifies the range of timestamps to read versions of data.

-   The timestamp in the value of time\_range ranges from 0 to INT64.MAX.

-   To query data of a time range, specify start\_time and end\_time.

-   To query data of a specific timestamp, specify specific\_time.


 Example: If the value of time\_range is \(100, 200\), the timestamp of the returned column data must be within the range of \[100, 200\). |
|max\_versions|int32|Either max\_versions or time\_range is required|This parameter specifies the maximum number of versions of data to return.

 Example: If the value of max\_versions is 2, a maximum of two versions of data is returned for each column. |
|limit|int32|No|-   This parameter specifies the maximum number of rows to read and return in a single operation. If the number of rows queried exceeds this value, the response includes a breakpoint to record the position where read ends in this operation. The next read continues from the recorded position. This value must be greater than 0.

-   Tablestore returns a maximum of 5,000 rows of data if this parameter is not set. The total data size does not exceed 4 MB. |
|inclusive\_start\_primary\_key|bytes|Yes|This parameter specifies a start primary key. The read starts from the row that contains the start primary key. If the row exists, the response contains this row. It is encoded in Plainbuffer. For more information, see [Plainbuffer](/intl.en-US/API Reference/Data Types/PlainBuffer.md). |
|exclusive\_end\_primary\_key|bytes|Yes|-   This parameter specifies an end primary key. The read stops at the row that contains the end primary key. The response does not contain the row regardless of the row existence. It is encoded in Plainbuffer. For more information, see [Plainbuffer](/intl.en-US/API Reference/Data Types/PlainBuffer.md).

-   For GetRange, the type of Column in inclusive\_start\_primary\_key or exclusive\_end\_primary\_key can be set to the following specific values for this operation: INF\_MIN and INF\_MAX. If the type of column is set to INF\_MIN, the value of the column is smaller than that of other columns. If the type of column is set to INF\_MAX, the value of the column is greater than that of other columns. |
|filter|bytes|No|-   This parameter specifies the expression of the filter condition.

-   It indicates the binary data after the filter is serialized in the Protobuf format. For more information, see [Filter](/intl.en-US/API Reference/Data Types/Filter.md). |
|start\_column|string|No|-   This parameter specifies the column from which the read of each row starts. This parameter is used to read wide rows.

-   The response contains the specified start column.

-   The columns are sorted based on their names in alphabetical order.


 Example: A table contains columns a, b, and c. If the value of start\_column is b, the reading starts from column b, and columns b and c are returned. |
|end\_column|string|No|-   This parameter specifies the column where the read of each row ends. This parameter is used to read wide rows.

-   The response does not contain the specified end column.

-   The columns are sorted based on their names in alphabetical order.


 Example: A table contains columns a, b, and c. If the value of end\_column is b, the reading ends at column b, and column a is returned. |

## Response structure

```language-protobuf
message GetRangeResponse {
    required ConsumedCapacity consumed = 1;
    required bytes rows = 2; 
    optional bytes next_start_primary_key = 3; 
}
```

|Parameter|Type|Description|
|---------|----|-----------|
|consumed|[ConsumedCapacity](/intl.en-US/API Reference/Data Types/ConsumedCapacity.md)|This parameter specifies the number of [capacity units \(CUs\)](#section_2ij_qj7_ulf) consumed by this operation. |
|rows|bytes|-   It is encoded in Plainbuffer. For more information, see [Plainbuffer](/intl.en-US/API Reference/Data Types/PlainBuffer.md).

-   If direction in the request is set to DirectionConst::CONST\_FORWARD, all rows are sorted by primary key in ascending order. If direction in the request is set to DirectionConst::CONST\_BACKWARD, all rows are sorted by primary key in descending order.

-   The primary\_key\_columns and attribute\_columns parameters for each row only contains the columns specified in columns\_to\_get. The order of the columns may be inconsistent with that of columns\_to\_get in the request. The order of the columns in primary\_key\_columns may be inconsistent with that of the columns when the table is created.

-   If the specified columns\_to\_get in the request does not contain any primary key column, rows that do not contain any attribute column specified in columns\_to\_get are not included in the response although the primary key column of the rows is within the query range. |
|next\_start\_primary\_key|bytes|-   This parameter specifies the breakpoint information for this GetRange operation. It is encoded in Plainbuffer. For more information, see [Plainbuffer](/intl.en-US/API Reference/Data Types/PlainBuffer.md).

-   If this parameter is not specified, the response to this GetRange operation contains all data within the request range.

-   If this parameter is specified, the response to this GetRange operation includes only the data within the range of \[inclusive\_start\_primary\_key, next\_start\_primary\_key\). To obtain the remaining data, set inclusive\_start\_primary\_key to the value of next\_start\_primary\_key, and retain the value of exclusive\_end\_primary\_key in the original request to perform the subsequent GetRange operation.

**Note:** Tablestore limits the number of rows in the response to a GetRange operation to 5,000 rows, whose size cannot exceed 4 MB. Even if no limit is set in the GetRange request, a next\_start\_primary\_key may still appear in the response. Therefore, when you use GetRange, you must check whether the response contains next\_start\_primary\_key. |

## CU consumption

-   The number of read CUs consumed by the GetRange operation is calculated based on the following formula: Number of read CUs consumed by the GetRange operation = \[\(Size of primary key columns of the row within the query range + Size of actually read attribute columns\)/4 KB\] rounded up. For more information about how to calculate the data size, see [pricing](/intl.en-US/Pricing/Billing overview.md).

-   If the request times out and the results are undefined, CUs may or may not be consumed.

-   If an internal error is returned \(HTTP status code: 5xx\), this operation does not consume CUs. If other errors are returned, one read CU is consumed.


