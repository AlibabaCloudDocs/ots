# GetRow

You can call this operation to read data from a row based on the specified primary key.

## Request structure

```
message GetRowRequest {
    required string table_name = 1;
    required bytes primary_key = 2;   // The PlainBuffer encoding is binary.
    repeated string columns_to_get = 3; // If this parameter is not specified, all columns are read.
    optional TimeRange time_range = 4;
    optional int32 max_versions = 5;
    optional bytes filter = 7;
    optional string start_column = 8;
    optional string end_column = 9;
    optional bytes token = 10;
}
```

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|table\_name|string|Yes|This parameter specifies the name of the table that contains the data to read.|
|primary\_key|bytes|Yes|This parameter specifies all primary key columns in the row, including the names and values of the primary key columns. The primary key columns are encoded in the PlainBuffer format. For more information, see [Plainbuffer](/intl.en-US/API Reference/Data Types/PlainBuffer.md).|
|columns\_to\_get|repeated string|No|-   This parameter specifies the names of all columns to return. If the parameter is not configured, all columns of this row are returned.
-   If the specified column does not exist, data in this column is not returned.
-   If a duplicate column name exists, the response only includes this column once.
-   The maximum number of strings that can be specified in columns\_to\_get is 128. |
|time\_range|[TimeRange](/intl.en-US/API Reference/Data Types/TimeRange.md)|At least one of time\_range and max\_versions is required|-   This parameter specifies the range of timestamps to read versions of data.
-   The timestamp in the value of time\_range ranges from 0 to INT64.MAX.
-   To query data of a time range, specify start\_time and end\_time.
-   To query data of a specific timestamp, specify specific\_time.

 Example: If the value of start\_time is 100 and the value of end\_time is 200, the timestamp of the returned column data must be within the range of \[100, 200\). |
|max\_versions|int32|At least one of time\_range and max\_versions is required|This parameter specifies the maximum number of versions of data to return.

 Example: If the value of max\_versions is 2, a maximum of two versions of data is returned for each column. |
|filter|bytes|No|This parameter specifies the expression of the filter condition.

 It indicates the binary data after the filter is serialized in the Protobuf format. For more information, see [Filter](/intl.en-US/API Reference/Data Types/Filter.md). |
|start\_column|string|No|-   This parameter specifies the column from which the read of each row starts. This parameter is used to read wide rows.

-   The response contains the specified start column.

-   The columns are sorted based on their names in alphabetical order.


 Example: If a table contains columns a, b, and c, and the value of start\_column is b, the read of data starts from column b. Columns b and c are returned. |
|end\_column|string|No|-   This parameter specifies the column where the read of each row ends. This parameter is used to read wide rows.

-   The response does not contain the specified end column.

-   The columns are sorted based on their names in alphabetical order.


 Example: If a table contains columns a, b, and c, and the value of end\_column is b, the read of data stops at column b. Only column a is returned. |

## Response structure

```
message GetRowResponse {
    required ConsumedCapacity consumed = 1;
    required bytes row = 2; // The PlainBuffer encoding is binary.
}
```

|Parameter|Type|Description|
|---------|----|-----------|
|consumed|[CapacityUnit](/intl.en-US/API Reference/Data Types/CapacityUnit.md)|This parameter indicates the number of capacity units \(CUs\) consumed by this operation. |
|row|bytes|This parameter indicates the data that is read by this operation. The read data is encoded in the PlainBuffer format. For more information, see [Plainbuffer](/intl.en-US/API Reference/Data Types/PlainBuffer.md).

 If this row does not exist, null is returned. |

## CU consumption

-   If the requested row does not exist, one read CU is consumed.

-   If the requested row exists, the number of consumed read CUs is calculated based on the formula: Number of consumed read CUs = \[\(Size of all primary key columns in the row + Size of actually read attribute columns\)/4 KB\] rounded up. For more information about how to calculate the data size, see [pricing](/intl.en-US/Pricing/Billing overview.md).

-   If the request times out and the results are undefined, CUs may or may not be consumed.

-   If an internal error code is returned \(HTTP status code: 5XX\), this operation does not consume CUs. If other errors are returned, one read CU is consumed.


