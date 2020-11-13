# Query table names

You can call the ListTable operation to query the names of all tables that are created in the current instance.

**Note:** For more information about the ListTable operation, see [ListTable](/intl.en-US/API Reference/Operations/ListTable.md).

## Prerequisites

-   TableStoreClient is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A table is created.

## Operations

```
// List all table names. If the operation succeeds, names of all tables are returned.
ListTable() (*ListTableResponse, error)
            
```

## Examples

The following code provides an example on how to query the names of all tables in an instance:

```
tables, err := client.ListTable()
if err != nil {
    fmt.Println("Failed to list table")
} else {
    fmt.Println("List table result is")
    for _, table := range (tables.TableNames) {
        fmt.Println("TableName: ", table)
    }
}
            
```

For the detailed sample code, visit [ListTable@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/TableOperation.go).

