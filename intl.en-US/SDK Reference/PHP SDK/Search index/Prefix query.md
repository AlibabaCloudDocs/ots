# Prefix query

You can use prefix query to query data that matches a specified prefix. If the type of a field is TEXT, Tablestore tokenizes the string and matches tokens by using the specified prefix.

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
|get\_total\_count|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to True. |
|query\_type|The query type. To use prefix query, set this parameter to QueryTypeConst::PREFIX\_QUERY.|
|field\_name|The name of the field used to match the query conditions.|
|prefix|The value of the prefix.If the field used to match the query conditions is a TEXT field, the field values are tokenized. A row meets the query conditions when the tokenized value of the specified field contains at least one term that contains the specified prefix. |
|sort|The sorting method. The returned rows are sorted based on the specified method. For more information, see [Sorting and paging](/intl.en-US/SDK Reference/PHP SDK/Search index/Sorting and paging.md).|
|columns\_to\_get|Specifies whether to return all columns of each matched row. You can configure return\_type and return\_names for this parameter. -   If you set return\_type to ColumnReturnTypeConst::RETURN\_SPECIFIED, you can use return\_names to specify the columns to return.
-   If return\_type is set to ColumnReturnTypeConst::RETURN\_ALL, all columns are returned.
-   If you set return\_type to ColumnReturnTypeConst::RETURN\_NONE, only the primary key columns are returned. |

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
            'query_type' => QueryTypeConst::PREFIX_QUERY,
            'query' => array(
                'field_name' => 'keyword',
                'prefix' => 'key'
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
        'return_type' => ColumnReturnTypeConst::RETURN_ALL,
        'return_names' => array('keyword')
    )
);
$response = $otsClient->search($request);
```

