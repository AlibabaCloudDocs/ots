# CreateTable

You can call this operation to create a table based on the specified table structure information.

## Request structure

```
message CreateTableRequest {
    required TableMeta table_meta = 1;
    required ReservedThroughput reserved_throughput = 2; 
    optional TableOptions table_options = 3;
    optional StreamSpecification stream_spec = 5;
}
```

## Request parameters

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|table\_meta|[TableMeta](/intl.en-US/API Reference/Data Types/TableMeta.md)|Yes|The structure information of the table to be created. The table\_name value must be unique within the instance. In primary\_key, the number of ColumnSchema must range from one to four, the ColumnSchema names must comply with [Naming conventions and data types](/intl.en-US/Function Introduction/Wide Column model/Naming conventions and data types.md), and the type value can only be STRING, INTEGER, or BINARY.

 After a table is created, its schema cannot be modified. |
|reserved\_throughput|[ReservedThroughput](/intl.en-US/API Reference/Data Types/ReservedThroughput.md)|Yes|The initial reserved read/write throughput for the table to be created. The maximum reserved throughput is 5,000 for each table.

 The reserved read/write throughput settings for a table can be modified by using the UpdateTable operation. |
|table\_options|[TableOptions](/intl.en-US/API Reference/Data Types/TableOptions.md)|Yes|Specifies TimeToLive and MaxVersions. |
|stream\_spec|[StreamSpecification](/intl.en-US/API Reference/Data Types/StreamSpecification.md)|No|Specifies whether to enable Stream-related attributes. |

## Response structure

```
message CreateTableResponse {
}
```

## Usage notes

-   No read/write operation can be immediately performed on a table after it is created. Typically, you can perform read/write operations on a new table one minute after it is created.

-   A single instance can contain up to 64 tables. If you must raise the upper limit of the table quantity for a single instance, [submit a ticket](https://selfservice.console.aliyun.com/ticket/createIndex).


