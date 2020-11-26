# GetRow

GetRow reads a single data row based on a given primary key.

## Request structure:

```
message GetRowRequest {
    required string table_name = 1;
    required bytes primary_key = 2;   // The Plainbuffer encoding is binary
    repeated string columns_to_get = 3; // If it is not specified, all columns are read
    optional TimeRange time_range = 4;
    optional int32 max_versions = 5;
    optional bytes filter = 7;
    optional string start_column = 8;
    optional string end_column = 9;
    optional bytes token = 10;
}
```

table\_name:

-   Type: Bytes

-   Required parameter: Yes

-   The name of the table containing the data to be read.


primary\_key:

-   Type: Bytes

-   Required parameter: Yes

-   All primary key columns in the row, including the primary key names and values. The primary key columns are encoded in Plainbuffer format. For more information, see [Plainbuffer](ZH-CN_TP_20466_V1.md#) encoding.


columns\_to\_get:

-   Type: Repeated string

-   Required parameter: No

-   The names of all columns to be returned. If it is null, all columns of this row are returned.

-   If the specified column does not exist, data of the column is not returned.

-   If a duplicate column name is given, the returned results only include this column once.

-   In columns\_to\_get, the maximum number of strings is 128.


time\_range:

-   Type: [TimeRange](ZH-CN_TP_20488_V1.md#)

-   Required parameter: Either time\_range or max\_versions is required.

-   The range of time stamps to read versions of data.

-   The minimum and maximum values of the time stamp are 0 and INT64.MAX , respectively.

-   To query data of a time range, specify start\_time and end\_time.

-   To query data of a specific time stamp, specify specific\_time.

-   Example: If the value of time\_range is \(100, 200\), the time stamp of the returned column data must be within \[100, 200\).


max\_versions:

-   Type: int32

-   Required parameter: Either time\_range or max\_versions is required.

-   The maximum number of versions of data to be returned.

-   Example: If the value of max\_versions is 2, data of a maximum of two versions is returned for each column.


filter:

-   Type: Bytes

-   Required parameter: No

-   The filtering condition expression.

-   It indicates the binary data after the [Filter](ZH-CN_TP_20461_V1.md#) is serialized in Protobuf format.


start\_column:

-   Type: String

-   Required parameter: No

-   The starting column to be read, which is used for reading a wide row.

-   The returned results contain the current starting column.

-   The column names are sorted lexicographically.

-   Example: If a table contains columns “a”, “b”, and “c” and the value of start\_column is “b”, the reading starts from column “b”, and columns “b” and “c” are returned.


end\_column:

-   Type: String

-   Required parameter: No

-   The ending column to be read, which is used for reading a wide row.

-   The returned results do not contain the current ending column.

-   The column names are sorted lexicographically.

-   Example: If a table contains columns “a”, “b”, and “c” and the value of end\_column is “b”, the reading ends at column “b”, and column “a” is returned.


## Response message structure:

```
message GetRowResponse {
    required ConsumedCapacity consumed = 1;
    required bytes row = 2; // The Plainbuffer encoding is binary
}
```

consumed:

-   Type: [CapacityUnit](ZH-CN_TP_20453_V1.md#)

-   The capacity units consumed by this operation.


row:

-   Type: Bytes

-   The read data is encoded in Plainbuffer format. For more information, see [Plainbuffer](ZH-CN_TP_20466_V1.md#) encoding.

-   If this row does not exist, null is returned.


## Capacity unit consumption:

-   If the requested row does not exist, one read capacity unit is consumed.

-   If the requested row exists, the number of read capacity units consumed is the sum of the size of all primary key columns in the row and the size of actually read attribute columns divided by 4 KB, and then rounded up. For data sizing, see [product pricing](../DNots1876307/ZH-CN_TP_20253_V1.md#).

-   If the request times out and the results are undefined, a capacity unit may or may not be consumed.

-   If an internal error code is returned \(HTTP status code: 5XX\), no capacity units are consumed. If other errors are returned, one read capacity unit is consumed.


