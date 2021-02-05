# DeleteTable

You can call this operation to delete a specified table from the current instance.

## Request structure

```
message DeleteTableRequest {
    required string table_name = 1;
}
```

table\_name

-   Type: string
-   Required: yes
-   The name of the table you want to delete

## Response structure

```
message DeleteTableResponse {
}
```

If the response of DeleteTable does not contain an error, the table has been deleted.

