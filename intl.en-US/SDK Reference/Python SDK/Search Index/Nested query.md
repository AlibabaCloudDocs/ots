# Nested query

This topic describes how to use nested query to query the child row data of nested columns. You cannot directly query nested fields. You must specify the NestedQuery object. In NestedQuery, you must specify the path of a nested column and a subquery. The subquery can be of any type.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A data table is created, and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Python SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|path|The path of the nested column. The path is similar to the tree structure. For example, news.title indicates the title subcolumn in the news column of the nested type.|
|query|The query on the subcolumn in the nested column. The query can be of any query type.|
|score\_mode|Specifies which value is used to calculate the score when a column contains multiple values.|
|table\_name|The name of the table.|
|index\_name|The name of the search index.|

## Examples

Use nested query to query the value of the n.n1 column that is greater than or equal to 100 and smaller than or equal to 300.

-   Perform nested query by using Tablestore SDK for Python V5.2.1 or later

    By default, if you use Tablestore SDK for Python V5.2.1 or later to perform a nested query, SearchResponse objects are returned. The following code provides a request sample:

    ```
    nested_query = RangeQuery('n.nl', range_from=100, range_to=300, include_lower=True, include_upper=True)
    query = NestedQuery('n', nested_query)
    search_response = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```

    You can use the following sample request to return results of the Tuple type:

    ```
    nested_query = RangeQuery('n.nl', range_from=100, range_to=300, include_lower=True, include_upper=True)
    query = NestedQuery('n', nested_query)
    rows, next_token, total_count, is_all_succeed, agg_result, group_by_results = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    ).v1_response()
    ```

-   Perform nested query by using Tablestore SDK for Python of a version earlier than 5.2.1

    By default, if you use Tablestore SDK for Python of a version earlier than 5.2.1 to perform a nested query, results of the Tuple type are returned. The following code provides a request sample:

    ```
    nested_query = RangeQuery('n.nl', range_from=100, range_to=300, include_lower=True, include_upper=True)
    query = NestedQuery('n', nested_query)
    rows, next_token, total_count, is_all_succeed = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```


