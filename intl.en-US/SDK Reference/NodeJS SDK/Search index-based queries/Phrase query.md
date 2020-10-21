# Phrase query

Match phrase query is similar to match query, but evaluates the position between multiple tokens. A row meets the query condition only when the order and position of the tokens in the row match those of the tokens contained in the tokenized keyword.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/NodeJS SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/NodeJS SDK/Search index-based queries/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table.|
|indexName|The name of the search index.|
|offset|The position from which the current query starts.|
|limit|The maximum number of rows that the current query returns.To query only the number of matched rows but not detailed data, you can set limit to 0. In this case, Tablestore returns the number of matched rows without data from the table. |
|queryType|The query type. To use phrase query, set this parameter to TableStore.QueryType.MATCH\_PHRASE\_QUERY.|
|fieldName|The name of the column used to match the query conditions.Match query applies to TEXT columns. |
|text|The keyword used for the query to match the column values.If the column used for the match query is a TEXT column, the keyword is tokenized into multiple keywords based on the analyzer you set when you create the search index. By default, single-word tokenization is performed if you do not set the analyzer when you create the search index.

For example, if you use the phrase "this is" as the keyword for a query, you can obtain query results such as "..., this is tablestore" and "this is a table". Query results such as "this table is ..." or "is this a table" cannot be obtained. |
|getTotalCount|Specify whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to true. |
|columnToGet|Specifies whether to return all columns of each matched rows. -   If you set returnType to TableStore.ColumnReturnType.RETURN\_SPECIFIED, you can use returnNames to specify the columns to return.
-   If you set returnType to TableStore.ColumnReturnType.RETURN\_ALL, all columns are returned.
-   If you set returnType to TableStore.ColumnReturnType.RETURN\_NONE, only the primary key columns are returned. |

## Examples

```
/**
 * Search the table for rows where the value of Col_Text matches the whole phase "hangzhou shanghai" in order.
 * Tablestore returns the number of rows that match the phrase and parts of the matched rows in this query.
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, // You can set limit to 0 if you do not need the specific data but the number of matched rows. The valve of 0 indicates that no data is returned.
        query: {// Set the query type to TableStore.QueryType.MATCH_PHRASE_QUERY.
            queryType: TableStore.QueryType.MATCH_PHRASE_QUERY,
            query: {
                fieldName: "Col_Text", // Set the column used to match the query conditions.
                text: "hangzhou shanghai" // Set the keyword used for the query to match the column values.
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

