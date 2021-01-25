# ListTable

You can call this operation to query the names of all tables that are created in the current instance.

## Usage notes

If a table has just been created, its table name appears in ListTableResponse, but this does not necessarily mean that this table can be read or written at the moment.

## Request structure

```
message ListTableRequest {
}            
```

## Response structure

```
message ListTableResponse {
    repeated string table_names = 1;
}            
```

|Parameter|Type|Description|
|---------|----|-----------|
|table\_names|repeated String|The names of all tables in the current instance.|

## Use Tablestore SDKs

You can use the following Tablestore SDKs to list the names of tables:

-   Tablestore SDK for Java: [Query table names](/intl.en-US/SDK Reference/Java SDK/Table/Query table names.md)
-   Tablestore SDK for Go: [Query table names](/intl.en-US/SDK Reference/Go SDK/Table/Query table names.md)
-   Tablestore SDK for Python: [Query table names](/intl.en-US/SDK Reference/Python SDK/Table/Query table names.md)
-   Tablestore SDK for Node.js: [Query table names](/intl.en-US/SDK Reference/Node.js SDK/Table/Query table names.md)
-   Tablestore SDK for .NET: [Query table names](/intl.en-US/SDK Reference/.NET SDK/Table/Query table names.md)
-   Tablestore SDK for PHP: [Query table names](/intl.en-US/SDK Reference/PHP SDK/Table operations/Query table names.md)

