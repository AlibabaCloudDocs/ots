# Range query

You can use RangeQuery to query data that falls within a specified range. When a table contains a TEXT string, Tablestore tokenizes the string and matches tokens by using the specified prefix.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/PHP SDK/Search index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|table\_name|The name of the table.|
|index\_name|The name of the search index.|
|offset|The position from which the current query starts.|
|limit|The maximum number of rows that the current query returns.To query only the number of matched rows but not detailed data, you can set limit to 0. In this case, Tablestore returns the number of matched rows without data from the table. |
|get\_total\_count|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to False, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to True. |
|query\_type|The query type. To use range query, set this parameter to QueryTypeConst::RANGE\_QUERY.|
|field\_name|The name of the field used to match the query conditions.|
|range\_from|The value from which the query starts.|
|range\_to|The value with which the query ends.|
|include\_lower|Specifies whether to include the value of range\_from in the response. The value type is Boolean.|
|include\_upper|Specifies whether to include the value of range\_to in the response. The value type is Boolean.|
|sort|The sorting method. The returned rows are sorted based on the specified method. For more information, see [Sorting and paging](/intl.en-US/SDK Reference/PHP SDK/Search index/Sorting and paging.md).|
|columns\_to\_get|Specifies whether to return all columns of each matched row. You can configure return\_type and column\_names for this parameter.-   If you set return\_type to ColumnReturnType.SPECIFIED, you can configure column\_names to specify the columns to return.
-   If you set return\_type to ColumnReturnType.ALL, all columns are returned.
-   If you set return\_type to ColumnReturnType.NONE, only the primary key columns are returned. |

## Examples

```
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index',
    'search_query' => array(
        'offset' => 0,
        'limit' => 2,
        'get_total_count' => true,
        'query' => array(
            'query_type' => QueryTypeConst::RANGE_QUERY,
            'query' => array(
                'field_name' => 'long',
                'range_from' => 100,
                'include_lower' => true,
                'range_to' => 101,
                'include_upper' => false
            )
        ),
        'sort' => array(
            array(
                'field_sort' => array(
                    'field_name' => 'keyword',
                    'order' => SortOrderConst::SORT_ORDER_ASC
                )
            ),
        )
    ),
    'columns_to_get' => array(
        'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
        'return_names' => array('double', 'long', 'keyword')
    )
);
$response = $otsClient->search($request);
```

