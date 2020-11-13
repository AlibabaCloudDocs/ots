# Query table names

You can call the ListTable operation to query the names of all tables that are created in the current instance.

**Note:** For more information about the ListTable operation, see [ListTable](/intl.en-US/API Reference/Operations/ListTable.md).

## Prerequisites

-   OTSClient is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialize SDK for Java.md).
-   A table is created.

## Examples

The following code provides an example on how to query the names of all tables in an instance:

```
private static void listTable(SyncClient client) {
    ListTableResponse response = client.listTable();
    System.out.println("List of tables:");
    for (String tableName : response.getTableNames()) {
        System.out.println(tableName);
    }
}
```

