# Boolean query

This topic describes how to use Boolean query to query data based on a combination of filter conditions. This type of query contains one or more subqueries as filter conditions. Tablestore returns the rows that match the subqueries. Each subquery can be of any type, including BoolQuery.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/NodeJS SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/NodeJS SDK/Search index-based queries/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table.|
|indexName|The name of the search index.|
|mustQueries|Only row data that matches all subqueries is returned. This parameter is equivalent to the AND operator.|
|mustNotQueries|Only row data that must not match all subqueries is returned. This parameter is equivalent to the NOT operator.|
|filterQueries|Only row data that matches all subfilters is returned. filter is similar to query. The difference is that filter does not calculate the relevance score based on the number of subfilters that the row meet.|
|shouldQueries|The list of subqueries the query result can or cannot match. This parameter is equivalent to the OR operator.Only rows that meet the minimum number of subquery conditions specified in shouldQueries can match the result.

The overall relevance score is higher if more subqueries of shouldQueries are met. |
|minimumShouldMatch|The minimum number that row data must match for subquery conditions in shouldQueries. Default value: 1.|

## Examples

The following code provides an example on how to construct BoolQuery to query data based on a combination of filter conditions:

|Condition|Description|
|---------|-----------|
|mustQueries: \(A AND B\)|Sets the query condition that the row data must meet both "Condition 1" and "Condition 2".|
|shouldQueries: \(A OR B\)|Sets the query condition that the row data meet at least "Condition 1" or "Condition 2".minimumShouldMatch defines the minimum number of shouldQueries that must be met. Default value: 1. |
|filterQueries: \(A AND B\)|The subqueries that all subfilters must match. filter is similar to query. The difference is that filter does not calculate the relevance score based on the number of subfilters that the row meet.|
|mustNotQueries: \(! A AND ! B\)|Sets the query condition that the row data meet neither "Condition 1" nor "Condition 2".|

```
/**
 * Construct a BoolQuery to query data based on a combination of filter conditions.
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, // You can set limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
        query: { // Set the query type to TableStore.QueryType.BOOL_QUERY.
            queryType: TableStore.QueryType.BOOL_QUERY,
            query: {
                mustQueries: [ // mustQueries, shouldQueries, and mustNotQueries are optional. 
                    { // Condition 1: The query type is RangeQuery. The query condition is that the value of the Col_Long column is greater than 3.
                        queryType: TableStore.QueryType.RANGE_QUERY,
                        query: {
                            fieldName: "Col_Long",
                            rangeFrom: 3,
                            includeLower: true
                        }
                    },
                    { // Condition 2: The query type is MatchQuery. The query condition is that the value of the Col_Keyword column must match "Hangzhou".
                        queryType: TableStore.QueryType.TERM_QUERY,
                        query: {
                            fieldName: "Col_Keyword",
                            term: "hangzhou"
                        }
                    }
                ],
                minimumShouldMatch: 1 // The minimum number of conditions that must be met. This parameter is valid only when you specify shouldQueries.
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

