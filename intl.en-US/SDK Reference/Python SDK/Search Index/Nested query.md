# Nested query

This topic describes how to use nested query to query the child row data of nested columns. You cannot directly query nested fields. You must specify the NestedQuery object. In NestedQuery, you must specify the path of a nested column and a subquery. The subquery can be of any type.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Python SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|path|The path of the NESTED column. The path is similar to the tree structure. For example, news.title indicates the title subcolumn in the news column of the NESTED type.|
|query|The query on the subcolumn in the NESTED column. The query can be of any query type.|
|score\_mode|The value on which a score is calculated when multiple values exist for a column.|
|table\_name|The name of the table.|
|index\_name|The name of the search index.|

## Examples

The following code provides an example on how to query the value of the n.n1 column in the table that is greater than or equal to 100 and smaller than or equal to 300:

```
nested_query = RangeQuery('n.nl', range_from=100, range_to=300, include_lower=True, include_upper=True)
query = NestedQuery('n', nested_query)
rows, next_token, total_count, is_all_succeed = client.search(
    table_name, index_name, 
    SearchQuery(query, limit=100, get_total_count=True), 
    ColumnsToGet(return_type=ColumnReturnType.ALL)
)
```

