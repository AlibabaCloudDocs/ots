# DescribeTable

You can call this operation to query the structure information and reserved read/write throughput value of the specified table.

## Request structure:

```language-pb
message DescribeTableRequest {
    required string table_name = 1;
}
			
```

table\_name:

-   Type: string

-   Required: yes

-   This parameter specifies the name of the table you want to query.


## Response structure:

```language-pb
message DescribeTableResponse {
    required TableMeta table_meta = 1;
    required ReservedThroughputDetails reserved_throughput_details = 2;
    required TableOptions table_options = 3;
    optional StreamDetails stream_details = 5;
    repeated bytes shard_splits = 6;
}
			
```

table\_meta:

-   Type: [TableMeta](/intl.en-US/API Reference/Data Types/TableMeta.md)

-   This parameter indicates the schema of the table. The schema is the same as the schema provided when the table is created.


reserved\_throughput\_details:

-   Type: [ReservedThroughputDetails](/intl.en-US/API Reference/Data Types/ReservedThroughputDetails.md)

-   This parameter indicates the reserved read/write throughput settings information of the table. The information contains the current reserved read/write throughput settings, the time these settings were last updated, and the number of times they have been lowered on the current date.


table\_options:

-   Type: [TableOptions](/intl.en-US/API Reference/Data Types/TableOptions.md)

-   This parameter indicates the latest value of the table\_options parameter.


StreamSpecification:

-   Type: [StreamSpecification](/intl.en-US/API Reference/Data Types/StreamSpecification.md)

-   Required: no

-   This parameter indicates whether Stream-related attributes are enabled.


shard\_splits:

-   Type: bytes

-   This parameter indicates the split points of all partitions in the current table.


