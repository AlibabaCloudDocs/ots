# Nested query

This topic describes how to use nested query to query the child row data of nested columns. You cannot directly query nested fields. You must specify the NestedQuery object. In NestedQuery, you must specify the path of a nested column and a subquery. The subquery can be of any type.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/PHP SDK/Search index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|table\_name|The name of the table.|
|index\_name|The name of the search index.|
|path|The path of the subcolumn in the nested column. The path is similar to the tree structure. For example, news.title indicates the title subcolumn in the nested news column.|
|query|The query on the subcolumn in the nested column. The query can be of any query type.|
|score\_mode|Specifies which value is used to calculate the score when a column contains multiple values.|

## Examples

The following code provides an example on how to search the nested.nested\_keyword column for data that matches "sub":

```
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index',
    'search_query' => array(
        'offset' => 0,
        'limit' => 2,
        'get_total_count' => true,
        'query' => array(
            'query_type' => QueryTypeConst::NESTED_QUERY,
            'score_mode' => ScoreModeConst::SCORE_MODE_AVG,
            'query' => array(
                'path' => "nested",
                'query' => array(
                    'query_type' => QueryTypeConst::TERM_QUERY,
                    'query' => array(
                        'field_name' => 'nested.nested_keyword',
                        'term' => 'sub'
                    )
                )
            )
        ),
        'sort' => array(
            array(
                'field_sort' => array(
                    'field_name' => 'nested.nested_long',
                    'order' => SortOrderConst::SORT_ORDER_DESC,
                    'nested_filter' => array(
                        'path' => "nested",
                        'query' => array(
                            'query_type' => QueryTypeConst::TERM_QUERY,
                            'query' => array(
                                'field_name' => 'nested.nested_keyword',
                                'term' => 'sub'
                            )
                        )
                    )
                )
            ),
        )
    ),
    'columns_to_get' => array(
        'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
        'return_names' => array('nested')
    )
);
$response = $otsClient->search($request);
```

