# Query the description of tables

You can call the DescribeTable operation to query the description such as the schema information and the reserved read and write throughput of a table.

**Note:** For more information about the DescribeTable operation, see [DescribeTable](/intl.en-US/API Reference/Operations/DescribeTable.md).

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A table is created.

## Operations

```
def describe_table(self, table_name):
```

Returned value: the description of the table. describe\_table\_response indicates the description of the table, which is an instance of the ots2.metadata.DescribeTableResponse class.

## Parameters

|Parameter|Description|
|---------|-----------|
|table\_name|The name of the table.|

## Examples

The following code provides an example on how to query the description of a table:

```
try:
    describe_response = ots_client.describe_table('myTable')
    # If no exception is returned, the description is obtained and the following information is displayed.
    print "describe table succeeded."
    print ('TableName: %s' % describe_response.table_meta.table_name)
    print ('PrimaryKey: %s' % describe_response.table_meta.schema_of_primary_key)
    print ('Reserved read throughput: %s' % describe_response.reserved_throughput_details.capacity_unit.read)
    print ('Reserved write throughput: %s' % describe_response.reserved_throughput_details.capacity_unit.write)
    print ('Last increase throughput time: %s' % describe_response.reserved_throughput_details.last_increase_time)
    print ('Last decrease throughput time: %s' % describe_response.reserved_throughput_details.last_decrease_time)
    print ('table options\'s time to live: %s' % describe_response.table_options.time_to_live)
    print ('table options\'s max version: %s' % describe_response.table_options.max_version)
    print ('table options\'s max_time_deviation: %s' % describe_response.table_options.max_time_deviation)
except Exception:
    # If the task fails, an exception is returned for you to handle.
    print "describe table failed."
            
```

For the detailed sample code, visit [DescribeTable@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/table_operations.py).

