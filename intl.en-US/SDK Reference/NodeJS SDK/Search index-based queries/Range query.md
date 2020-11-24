# Range query

You can use RangeQuery to query data that falls within a specified range. When a table contains a TEXT string, Tablestore tokenizes the string and matches tokens by using the specified prefix.

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
|queryType|The query type. To use prefix query, set this parameter to TableStore.QueryType.RANGE\_QUERY.|
|fieldName|The name of the field used to match the query conditions.|
|rangeFrom|The value from which the query starts.|
|rangeTo|The value with which the query ends.|
|includeLower|Specifies whether to include the value of rangeFrom in the response. The value type is Boolean.|
|includeUpper|Specifies whether to include the value of rangeTo in the response. The value type is Boolean.|
|getTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to true. |
|columnToGet|Specifies whether to return all columns of each matched row. You can configure returnType and returnNames for this parameter. -   If you set returnType to TableStore.ColumnReturnType.RETURN\_SPECIFIED, you can configure returnNames to specify the columns to return.
-   If you set returnType to TableStore.ColumnReturnType.RETURN\_ALL, all columns are returned.
-   If you set returnType to TableStore.ColumnReturnType.RETURN\_NONE, only the primary key columns are returned. |

## Examples

```
/**
 * Search the table for rows where the values of Col_Long ranges from 1 to 10, and the range includes 1 but excludes 10. Tablestore sorts these rows by the value of Col_Long in descending order.
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, // You can set limit to 0 if you do not need the specific data but the number of matched rows. A valve of 0 indicates that no data is returned.
        query: { // Set the query type to TableStore.QueryType.RANGE_QUERY.
            queryType: TableStore.QueryType.RANGE_QUERY,
            query: {
                fieldName: "Col_Long",
                rangeFrom: 1,
                includeLower: true, // Include the value of rangeFrom in the range.
                rangeTo: 10,
                includeUpper: false // Exclude the value of rangeTo from the range.
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

