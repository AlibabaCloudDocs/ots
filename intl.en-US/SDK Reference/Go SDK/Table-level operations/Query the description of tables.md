# Query the description of tables

You can call the DescribeTable operation to query the description such as the schema information and the reserved read and write throughput of a table.

**Note:** For more information about the DescribeTable operation, see [DescribeTable](/intl.en-US/API Reference/Operations/DescribeTable.md).

## Prerequisites

-   TableStoreClient is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A table is created.

## Operations

```
// Query the description of a table by using the table name.
DescribeTable(request *DescribeTableRequest) (*DescribeTableResponse, error)
            
```

## Parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table.|

## Examples

The following code provides an example on how to query the description of a table:

```
describeTableReq := new(tablestore.DescribeTableRequest)
describeTableReq.TableName = tableName
describ, err := client.DescribeTable(describeTableReq)
if err != nil {
    fmt.Println("failed to update table with error:", err)
} else {
    fmt.Println("DescribeTableSample finished. Table meta:", describ.TableOption.MaxVersion, describ.TableOption.TimeToAlive)
}

            
```

For the detailed sample code, visit [DescribeTable@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/TableOperation.go).

