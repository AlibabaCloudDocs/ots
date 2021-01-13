# UpdateTable

You can call the UpdateTable operation to update the reserved read/write throughput of a table.

## Operations

```
// Update the configurations of tableoptions and reservedthroughput for a table.
UpdateTable(request *UpdateTableRequest) (*UpdateTableResponse, error)
			
```

## Examples

The following code provides an example on how to update the maximum number of versions of a table to five:

```
updateTableReq := new(tablestore.UpdateTableRequest)
updateTableReq.TableName = tableName
updateTableReq.TableOption = new(tablestore.TableOption)
updateTableReq.TableOption.TimeToAlive = -1
updateTableReq.TableOption.MaxVersion = 5

_, err := client.UpdateTable(updateTableReq)

if (err ! = nil) {
    fmt.Println("failed to update table with error:", err)
} else {
    fmt.Println("update finished")
}
			
```

For the complete sample code, visit [UpdateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/TableOperation.go).

