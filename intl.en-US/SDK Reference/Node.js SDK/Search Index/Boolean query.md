# Boolean query

This topic describes how to use Boolean query to query data based on a combination of filter conditions. This type of query contains one or more subqueries as filter conditions. Tablestore returns the rows that match the subqueries. Each subquery can be of any type, including BoolQuery.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Node.js SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Node.js SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table.|
|indexName|The name of the search index.|
|mustQueries|The list of subqueries that the query result must match. This parameter is equivalent to the AND operator.|
|mustNotQueries|The list of subqueries that the query result must not match. This parameter is equivalent to the NOT operator.|
|filterQueries|The list of subqueries. Only row data that matches all subfilters is returned. filter is similar to query. The difference is that filter does not calculate the relevance score based on the number of subfilters that the row meets.|
|shouldQueries|The list of subqueries the query result can or cannot match. This parameter is equivalent to the OR operator.Only rows that meet the minimum number of subquery conditions specified in shouldQueries can match the result.

If more subquery conditions of shouldQueries are met, the overall relevance score is higher. |
|minimumShouldMatch|The minimum number that row data must match for subquery conditions in shouldQueries. Default value: 1|

## Examples

The following code provides an example on how to construct BoolQuery to query data based on a combination of conditions:

```
var client = require('../client');
var TableStore = require('.. /../index.js');
var Long = TableStore.Long;
/**
 * Use boolean query to implement the following operations: (col2<4 or col3<5) or (col2 = 4 and (col3 = 5 or col3 =6)). Logic:
 * boolQuery1 = rangeQuery(col2<4) or rangeQuery(col3<5)
 * boolQuery2 = termQuery(col3=5) or (col3=6)
 * boolQuery3 = termQuery(col2=4) and boolquery2
 * boolQuery4 = boolQuery1 or boolQuery3
 */
client.search({
    tableName: "sampleTable",
    indexName: "sampleSearchIndex",
    searchQuery: {
        offset: 0, // Query the offset value.
        limit: 10, // If you do not need the specific data but need the number of rows, you can set limit to 0. The valve of 0 indicates that no data is returned.
        getTotalCount: false, // The value of TotalCount indicates the number of rows that match the query conditions. By default, TotalCount is set to false, which indicates that the total number of matched rows is not returned. Retain the default value in this example.
        query: { // Construct boolQuery4. Specify the query condition to meet at least the condition of boolQuery1 or boolQuery3.
            queryType: TableStore.QueryType.BOOL_QUERY,
            query: {
                shouldQueries: [ // mustQueries, shouldQueries, and mustNotQueries are optional.
                    { // Construct boolQuery1. Specify the query condition to meet at least the condition of Query Condition 1 or Query Condition 2.
                        queryType: TableStore.QueryType.BOOL_QUERY,
                        query: {
                            // Set shouldQueries to query the rows that contain the col2 column whose value is smaller than 4 or contain the col3 column whose value is smaller than 5.
                            shouldQueries:[
                                {
                                    // Query Condition 1: Use rangeQuery to query the rows that contain the col2 column whose value is smaller than 4.
                                    queryType: TableStore.QueryType.RANGE_QUERY,
                                    query:{
                                        fieldName: "col2",
                                        rangeTo: 4
                                    }
                                },
                                {
                                    // Query Condition 2: Use rangeQuery to query the rows that contain the col3 column whose value is smaller than 5.                                    queryType: TableStore.QueryType.RANGE_QUERY,
                                    query:{
                                        fieldName: "col3",
                                        rangeTo: 5
                                    }
                                }
                            ],
                            minimumShouldMatch:1

                        }
                    },
                    { // Construct boolQuery3. Specify the query condition to meet the conditions of Query Condition 3 and boolQuery2.
                        queryType: TableStore.QueryType.BOOL_QUERY,
                        query: {
                            mustQueries: [
                                // Set mustQueries to query the rows that contain the col2 column whose value is equal to 4 and contain the col3 column whose value is equal to 5 or 6.
                                {
                                    // Query Condition 3: Use termQuery to query the rows that contain the col2 column whose value is equal to 4.
                                    queryType:TableStore.QueryType.TERM_QUERY,
                                    query: {
                                        fieldName : "col2",
                                        term: 4
                                    }
                                },
                                { // Construct boolQuery2: Specify the query condition to meet at least the condition of Query Condition 4 or Query Condition 5.
                                    queryType: TableStore.QueryType.BOOL_QUERY,
                                    query: {
                      TERM                  // Set shouldQueries to query the rows that contain the col3 column whose value is equal to 5 or 6.
                                        shouldQueries:[
                                            {
                                                // Query Condition 4: Use termQuery to query the rows that contain the col3 column whose value is equal to 5.
                                                queryType: TableStore.QueryType.TERM_QUERY,
                                                query:{
                                                    fieldName:"col3",
                                                    term: 5
                                                }
                                            },
                                            {
                                                // Query Condition 5: Use termQuery to query the rows that contain the col3 column whose value is equal to 6.                                                queryType: TableStore.QueryType.TERM_QUERY,
                                                query:{
                                                    fieldName:"col3",
                                                    term: 6
                                                }
                                            }
                                        ],
                                        minimumShouldMatch:1
                                    }
                                }
                            ]
                        }
                    }
                ],
                minimumShouldMatch: 1 // The minimum number of conditions that must be met. This parameter is valid only when you specify shouldQueries.
            }
        },
    },
    columnToGet: { // Set RETURN_SPECIFIED to return a specified number of columns, RETURN_ALL to return all columns, or RETURN_NONE to return no data.       
        returnType: TableStore.ColumnReturnType.RETURN_SPECIFIED,
        returnNames: ["col2", "col3", "col4"]
    }
}, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', JSON.stringify(data, null, 2));
});
```

