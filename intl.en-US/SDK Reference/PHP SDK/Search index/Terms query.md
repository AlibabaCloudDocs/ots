# Terms query

This query is similar to term query. However, terms query supports multiple terms. This query is also similar to the SQL IN operator. A row of data is returned if one of the keywords matches the field value. Terms query is equivalent to the IN operator in SQL statements.

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
|query\_type|The query type. To use terms query, set this parameter to QueryTypeConst::TERMS\_QUERY.|
|field\_name|The name of the field used to match the query conditions.|
|terms|The keywords used in the query to match the column values.A row meets the query conditions when the value of the specified column exactly matches at least one keyword. |
|sort|The sorting method. The returned rows are sorted based on the specified method. For more information, see [Sorting and paging](/intl.en-US/SDK Reference/PHP SDK/Search index/Sorting and paging.md).|
|columns\_to\_get|Specifies whether to return all columns of each matched row. You can configure return\_type and return\_names for this parameter. -   If you set return\_type to ColumnReturnTypeConst::RETURN\_SPECIFIED, you can configure return\_names to specify the columns to return.
-   If return\_type is set to ColumnReturnTypeConst::RETURN\_ALL, all columns are returned.
-   If you set return\_type to ColumnReturnTypeConst::RETURN\_NONE, only the primary key columns are returned. |

## Examples

```
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index',
    'search_query' => array(
        'offset' => 0,
        'limit' => 5,
        'get_total_count' => true,
        'query' => array(
            'query_type' => QueryTypeConst::TERMS_QUERY,
            'query' => array(
                'field_name' => 'keyword',
                'terms' => array(
                    "keyword",
                    "php"
                )
            )
        ),
        'sort' => array(
            array(
                'field_sort' => array(
                    'field_name' => 'long',
                    'order' => SortOrderConst::SORT_ORDER_DESC,
                    'mode' => SortModeConst::SORT_MODE_AVG
                )
            )
        )
    ),
    'columns_to_get' => array(
        'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
        'return_names' => array('keyword', 'long')
    )
);
$response = $otsClient->search($request);
```

