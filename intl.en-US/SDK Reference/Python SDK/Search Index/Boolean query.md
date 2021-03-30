# Boolean query

This topic describes how to use Boolean query to query data based on a combination of subqueries. Tablestore returns the rows that match the subqueries. Each subquery can be of any type, including BoolQuery.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Python SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|must\_queries|Only rows that meet all subquery conditions are returned. This parameter is equivalent to the AND operator.|
|must\_not\_queries|Only rows that do not meet any subquery conditions are returned. This parameter is equivalent to the NOT operator.|
|filter\_queries|Only rows that meet all subfilters are returned. Compared with query, filter does not calculate the relevance score based on the number of subfilters that the row meet.|
|should\_queries|The list of subqueries the query result can or cannot match. This parameter is equivalent to the OR operator. Only rows that meet the minimum number of subquery conditions specified by should\_queries are returned.

The overall relevance score is higher if more subquery conditions specified by should\_queries are met. |
|minimum\_should\_match|The minimum number of subquery conditions specified by should\_queries that row data must match. Default value: 1.|
|table\_name|The name of the table.|
|index\_name|The name of the search index.|

## Examples

-   Perform Boolean query by using Tablestore SDK for Python V5.2.1 or later

    By default, if you use Tablestore SDK for Python V5.2.1 or later to perform a Boolean query, SearchResponse objects are returned. The following code provides a request sample:

    ```
    # k > 'key100' and (l > 110 and l < 200) and not (k = 'key121')
    # and should_queries(k > 'key120' or l < 300, minimum_should_match=2)
    bool_query = BoolQuery(
        must_queries=[
            RangeQuery('k', range_from='key100', include_lower=False),
            // Set BoolQuery.
            BoolQuery(
                // Set subqueries that row data must match.
                must_queries=[
                    RangeQuery('l', range_from=110, include_lower=False),
                    RangeQuery('l', range_to=200, include_upper=False)
                ],
            )
        ],
        // Set subqueries that row data must not match.
        must_not_queries=[
            TermQuery('k', 'key121')
        ],
        should_queries=[
            RangeQuery('k', range_from='key120', include_lower=False),
            RangeQuery('l', range_to=300, include_upper=130)
        ],
        minimum_should_match=2
    )
    
    // Construct a query by specifying query parameters, including sort, limit, and get_total_count.
    search_response = client.search(
        table_name, index_name, 
        SearchQuery(
            bool_query, 
            sort=Sort(sorters=[FieldSort('l', SortOrder.ASC)]), 
            limit=100, 
            get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```

    You can use the following sample request to return results of the Tuple type:

    ```
    # k > 'key100' and (l > 110 and l < 200) and not (k = 'key121')
    # and should_queries(k > 'key120' or l < 300, minimum_should_match=2)
    bool_query = BoolQuery(
        must_queries=[
            RangeQuery('k', range_from='key100', include_lower=False),
            // Set BoolQuery.
            BoolQuery(
                // Set subqueries that row data must match.
                must_queries=[
                    RangeQuery('l', range_from=110, include_lower=False),
                    RangeQuery('l', range_to=200, include_upper=False)
                ],
            )
        ],
        // Set subqueries that row data must not match.
        must_not_queries=[
            TermQuery('k', 'key121')
        ],
        should_queries=[
            RangeQuery('k', range_from='key120', include_lower=False),
            RangeQuery('l', range_to=300, include_upper=130)
        ],
        minimum_should_match=2
    )
    
    // Construct a query by specifying query parameters, including sort, limit, and get_total_count.
    rows, next_token, total_count, is_all_succeed, agg_results, group_by_results = client.search(
        table_name, index_name, 
        SearchQuery(
            bool_query, 
            sort=Sort(sorters=[FieldSort('l', SortOrder.ASC)]), 
            limit=100, 
            get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    ).v1_response()
    ```

-   Perform Boolean query by using Tablestore SDK for Python of a version earlier than 5.2.1

    By default, if you use Tablestore SDK for Python of a version earlier than 5.2.1 to perform a Boolean query, results of the Tuple type are returned. The following code provides a request sample:

    ```
    # k > 'key100' and (l > 110 and l < 200) and not (k = 'key121')
    # and should_queries(k > 'key120' or l < 300, minimum_should_match=2)
    bool_query = BoolQuery(
        must_queries=[
            RangeQuery('k', range_from='key100', include_lower=False),
            // Set BoolQuery.
            BoolQuery(
                // Set subqueries that row data must match.
                must_queries=[
                    RangeQuery('l', range_from=110, include_lower=False),
                    RangeQuery('l', range_to=200, include_upper=False)
                ],
            )
        ],
        // Set subqueries that row data must not match.
        must_not_queries=[
            TermQuery('k', 'key121')
        ],
        should_queries=[
            RangeQuery('k', range_from='key120', include_lower=False),
            RangeQuery('l', range_to=300, include_upper=130)
        ],
        minimum_should_match=2
    )
    
    // Construct a query by specifying query parameters, including sort, limit, and get_total_count.
    rows, next_token, total_count, is_all_succeed = client.search(
        table_name, index_name, 
        SearchQuery(
            bool_query, 
            sort=Sort(sorters=[FieldSort('l', SortOrder.ASC)]), 
            limit=100, 
            get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```


