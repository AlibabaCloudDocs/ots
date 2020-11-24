# Match query

You can use match query to query a table based on approximate matches. Tablestore tokenizes the values in TEXT columns and the keywords you use to perform match queries based on the analyzer you set. Therefore, Tablestore can perform match queries based on the tokenized text.

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
|queryType|The query type. To use match query, set this parameter to TableStore.QueryType.MATCH\_QUERY.|
|fieldName|The name of the column used to match the query conditions.Match query applies to TEXT columns. |
|text|The keyword used for the query to match the column values.If the column used for the match query is a TEXT column, the keyword is tokenized into multiple keywords based on the analyzer you set when you create the search index. By default, single-word tokenization is performed if you do not set the analyzer when you create the search index.

For example, you can specify a TEXT column for a query, and set the analyzer to perform single-word tokenization. If you use the phrase "this is" as the keyword for a query, you can obtain query results such as "..., this is tablestore", "is this tablestore", "tablestore is cool", "this", and "is". |
|operator|The logical operator. By default, OR is used as the logical operator, which indicates that a row matches the query conditions when one of the analyzed keywords is matched.If you set operator to AND, a row meets the query condition only when all tokenized keywords are matched. |
|minimumShouldMatch|The minimum number of matched tokenized keywords contained in a column value.A row is returned only when the value of the queried column in the row contains at least the minimum number of matched tokenized keywords.

**Note:** minimumShouldMatch must be used together with the OR logical operator. |
|getTotalCount|Specify whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to true. |
|columnToGet|Specifies whether to return all columns of each matched rows. You can configure returnType and returnNames for this parameter.-   If you set returnType to TableStore.ColumnReturnType.RETURN\_SPECIFIED, you can use returnNames to specify the columns to return.
-   If you set returnType to TableStore.ColumnReturnType.RETURN\_ALL, all columns are returned.
-   If you set returnType to TableStore.ColumnReturnType.RETURN\_NONE, only the primary key columns are returned. |

## Examples

```
/**
 * Search the table for rows where the value of Col_Keyword matches "hangzhou". Tablestore returns the number of rows that match the phrase and parts of the matched rows in this query.
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, // You can set limit to 0 if you do not need the specific data but the number of matched rows. The valve of 0 indicates that no data is returned.
        query: { // Set the query type to MatchQuery.
            queryType: TableStore.QueryType.MATCH_QUERY,
            query: {
                fieldName: "Col_Keyword", // Set the column used to match the query conditions
                text: "hangzhou" // Set the keyword used for the query to match the column values.
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

