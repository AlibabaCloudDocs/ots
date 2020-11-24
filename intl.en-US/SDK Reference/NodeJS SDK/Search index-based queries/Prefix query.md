# Prefix query

You can use prefix query to query data that matches a specified prefix. When a table contains a TEXT string, Tablestore tokenizes the string and matches tokens by using the specified prefix.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/NodeJS SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/NodeJS SDK/Search index-based queries/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table.|
|indexName|The name of the search index.|
|offset|The position from which the current query starts.|
|limit|The maximum number of rows that the current query returns.To query only the number of matched rows but not detailed data, you can set limit to 0. In this case, Tablestore returns the number of matched rows without data from the table. |
|queryType|The query type. To use prefix query, set this parameter to TableStore.QueryType.PREFIX\_QUERY.|
|fieldName|The name of the field used to match the query conditions.|
|prefix|The value of the prefix.If the field used to match the query conditions is a TEXT field, the field values are tokenized. A row meets the query conditions when the tokenized value of the specified field contains at least one term that contains the specified prefix. |
|getTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to true. |
|columnToGet|Specifies whether to return all columns of each matched row. You can configure returnType and returnNames for this parameter.-   If you set returnType to TableStore.ColumnReturnType.RETURN\_SPECIFIED, you can configure returnNames to specify the columns to return.
-   If you set returnType to TableStore.ColumnReturnType.RETURN\_ALL, all columns are returned.
-   If you set returnType to TableStore.ColumnReturnType.RETURN\_NONE, only the primary key columns are returned. |

## Examples

```
/**
 * Search the table for rows where the value of Col_Keyword contains the prefix that exactly matches "hang", such as "hangzhou".
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, // You can set limit to 0 if you do not need the specific data but the number of matched rows. A valve of 0 indicates that no data is returned.
        query: { // Set the query type to TableStore.QueryType.PREFIX_QUERY.
            queryType: TableStore.QueryType.PREFIX_QUERY,
            query: {
                fieldName: "Col_Keyword",
                prefix: "hang" // Set the prefix to "hang" to match terms such as "hangzhou" or "hangzhoushi".
            }
        },
        getTotalCount: true // The value of TotalCount indicates the number of rows that match the query conditions. By default, TotalCount is set to false, which indicates that the total number of matched rows is not returned. Set TotalCount to true in this example.
    },
    columnToGet: { // You can set RETURN_SPECIFIED to return the specified columns, RETURN_ALL to return all columns, or RETURN_NONE to return no attribute columns from the table.
        returnType: TableStore.ColumnReturnType.RETURN_ALL
    }
}, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', JSON.stringify(data, null, 2));
});
            
```

