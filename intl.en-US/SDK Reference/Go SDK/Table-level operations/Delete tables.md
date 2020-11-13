# Delete tables

You can call the DeleteTable operation to delete a specified table from the current instance.

**Note:** For more information about the DeleteTable operation, see [DeleteTable](/intl.en-US/API Reference/Operations/DeleteTable.md).

## Prerequisites

-   TableStoreClient is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A table is created.
-   The index tables and search indexes for the table are deleted.

## Operations

```
DeleteTable(request *DeleteTableRequest) (*DeleteTableResponse, error)
```

## Parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table.|

## Examples

The following code provides an example on how to delete a specified table:

```
deleteReq := new(tablestore.DeleteTableRequest)
deleteReq.TableName = tableName
_, err := client.DeleteTable(deleteReq)
if (err != nil) {
    fmt.Println("Failed to delete table with error:", err)
} else {
    fmt.Println("Delete table finished")
}
            
```

For the complete sample code, visit [DeleteTable@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/TableOperation.go).

