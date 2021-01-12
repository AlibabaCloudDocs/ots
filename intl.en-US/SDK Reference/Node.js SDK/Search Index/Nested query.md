# Nested query

This topic describes how to use nested query to query the child row data of nested columns. You cannot directly query nested fields. You must specify the NestedQuery object. In NestedQuery, you must specify the path of a nested column and a subquery. The subquery can be of any type.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Node.js SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Node.js SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table.|
|indexName|The name of the search index.|
|path|The path of the NESTED column. The path is similar to the tree structure. For example, news.title indicates the title subcolumn in the news column of the NESTED type.|
|query|The query on the subcolumn in the NESTED column. The query can be of any query type.|
|scoreMode|The value on which a score is calculated when multiple values exist for a column.|

## Examples

The following code provides an example on how to search "Hangzhou" of the Col\_Nested.Sub\_Col\_Keyword column for data:

```
/**
 * Col_Nested: '[{Sub_Col_Keyword: "Hangzhou"},{Sub_Col_Keyword: "Shanghai"}]'
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, // You can set limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
        query: { // Set the query type to TableStore.QueryType.NESTED_QUERY.
            queryType: TableStore.QueryType.NESTED_QUERY,
            query: {
                path: "Col_Nested",
                query: {
                    queryType: TableStore.QueryType.TERM_QUERY,
                    query: {
                        fieldName: "Col_Nested.Sub_Col_Keyword",
                        term: "Hangzhou"
                    }
                },
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

