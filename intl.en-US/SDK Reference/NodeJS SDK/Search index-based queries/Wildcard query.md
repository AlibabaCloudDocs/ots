# Wildcard query

You can use asterisks \(\*\) and question marks \(?\) as wildcards to search for data. An asterisk \(\*\) can represent any number of characters. A question mark \(?\) can represent any single character. The value to query can start with an asterisk \(\*\) or a question mark \(?\). For example, you can match query results such as "tablestore" by searching for "table\*e".

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
|limit|The maximum number of rows that the current query returns.|
|queryType|The query type. Set this parameter to TableStore.QueryType.WILDCARD\_QUERY.|
|fieldName|The name of the column.|
|value|The string that contains wildcards. The string can be up to 10 bytes in length.|
|getTotalCount|Specifies whether to return the total number of matched rows. The default value is false, which indicates that the total number of matched rows is not returned. Query performance is affected when this parameter is set to true. |
|columnToGet|Specifies whether to return all columns of each matched row. -   If you set returnType to TableStore.ColumnReturnType.RETURN\_SPECIFIED, you must specify the columns to return.
-   If you set returnType to TableStore.ColumnReturnType.RETURN\_ALL, all columns are returned.
-   If you set returnType to TableStore.ColumnReturnType.RETURN\_NONE, only the primary key columns are returned. |

## Examples

```
/**
 * Search the table for rows where the value of Col_Keyword matches "table*e".
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, // You can set limit to 0 if you do not need the specific data but need the number of matched rows. The value of 0 indicates that no data is returned.
        query: { // Set the query type to TableStore.QueryType.WILDCARD_QUERY.
            queryType: TableStore.QueryType.WILDCARD_QUERY,
            query: {
                fieldName: "Col_Keyword",
                value: "table*e" // wildcardQuery can contain wildcards.
            }
        },
        getTotalCount: true // The value of TotalCount indicates the number of rows that match the query conditions. By default, TotalCount is set to false, which indicates that the total number of matched rows is not returned. Set TotalCount to true in this example.
    },
    columnToGet: { // You can set RETURN_SPECIFIED to return the specified columns, RETURN_ALL to return all columns, or RETURN_NONE to return no data from the table.
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

