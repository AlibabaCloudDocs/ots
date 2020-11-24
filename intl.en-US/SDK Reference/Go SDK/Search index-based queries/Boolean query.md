# Boolean query

This topic describes how to use Boolean query to query data based on a combination of filter conditions. This type of query contains one or more subqueries as filter conditions. Tablestore returns the rows that match the subqueries. Each subquery can be of any type, including BoolQuery.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|MustQueries|Only row data that matches all subqueries is returned. This parameter is equivalent to the AND operator.|
|MustNotQueries|Only row data that must not match all subqueries is returned. This parameter is equivalent to the NOT operator.|
|FilterQueries|Only row data that matches all subfilters is returned. Filter is similar to Query. The difference is that Filter does not calculate the relevance score based on the number of subfilters that the row meet.|
|ShouldQueries|The list of subqueries the query result can or cannot match. This parameter is equivalent to the OR operator.Only rows that meet the minimum number of subquery conditions specified in ShouldQueries can match the result.

The overall relevance score is higher if more subqueries of ShouldQueries are met. |
|MinimumShouldMatch|The minimum number that row data must match for subquery conditions in ShouldQueries. Default value: 1.|

## Examples

The following code provides an example on how to use BoolQuery to query data based on a combination of filter conditions:

```
/**
 * Use BoolQuery to query data based on a combination of filter conditions.
 */
func BoolQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
    searchRequest := &tablestore.SearchRequest{}
    searchRequest.SetTableName(tableName)
    searchRequest.SetIndexName(indexName)

    /**
     * Condition 1: The query type is RangeQuery. The query condition is that the value of the Col_Keyword column is greater than 3.
     */
    rangeQuery := &search.RangeQuery{}
    rangeQuery.FieldName = "Col_Long"
    rangeQuery.GT(3)

    /**
     * Condition 2: The query type is MatchQuery. The query condition is that the value of the Col_Keyword column must match "hangzhou".
     */
    matchQuery := &search.MatchQuery{}
    matchQuery.FieldName = "Col_Keyword"
    matchQuery.Text = "hangzhou"

    {
        /**
         * Construct a BoolQuery where the row data must meet both "Condition 1" and "Condition 2".
         */
        boolQuery := &search.BoolQuery{
            MustQueries: []search.Query{
                rangeQuery,
                matchQuery,
            },
        }
        searchQuery := search.NewSearchQuery()
        searchQuery.SetQuery(boolQuery)
        searchRequest.SetSearchQuery(searchQuery)
        searchResponse, err := client.Search(searchRequest)
        if err != nil {
            fmt.Printf("%#v", err)
            return
        }
        fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
        fmt.Println("TotalCount: ", searchResponse.TotalCount) // Display the total number of matched rows.
        fmt.Println("RowCount: ", len(searchResponse.Rows))
    }
    {
        /**
         * Construct a BoolQuery where the row data meet at least "Condition 1" or "Condition 2".
         */
        boolQuery := &search.BoolQuery{
            ShouldQueries: []search.Query{
                rangeQuery,
                matchQuery,
            },
            MinimumShouldMatch: proto.Int32(1),
        }
        searchQuery := search.NewSearchQuery()
        searchQuery.SetQuery(boolQuery)
        searchRequest.SetSearchQuery(searchQuery)
        searchResponse, err := client.Search(searchRequest)
        if err != nil {
            fmt.Printf("%#v", err)
            return
        }
        fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
        fmt.Println("TotalCount: ", searchResponse.TotalCount) // Display the total number of matched rows.
        fmt.Println("RowCount: ", len(searchResponse.Rows))
    }
}           
```

