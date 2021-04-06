# Wildcard query

You can use asterisks \(\*\) and question marks \(?\) as wildcards to search for data. An asterisk \(\*\) can represent any number of characters. A question mark \(?\) can represent any single character. The value to query can start with an asterisk \(\*\) or a question mark \(?\). For example, you can match query results such as "tablestore" by searching for "table\*e".

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/PHP SDK/Search index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|table\_name|The name of the table.|
|index\_name|The name of the search index.|
|offset|Specifies the position from which the current query starts.|
|limit|Specifies the maximum number of items that the current query returns.|
|get\_total\_count|Specifies whether to return the total number of matched rows. The default value is false, which indicates that the total number of rows in the table is not returned. If get\_total\_count is set to true, the query performance is affected. |
|query\_type|The query type. Set the query type to QueryTypeConst::WILDCARD\_QUERY.|
|field\_name|The column name.|
|value|The string that contains wildcards. The string cannot exceed 20 characters in length.|
|sort|The sorting method. Sort based on the specified method. For more information, see [Sorting and pagination](/intl.en-US/SDK Reference/PHP SDK/Search index/Sorting and pagination.md).|
|columns\_to\_get|Specifies whether to return all columns. -   If you set return\_type to ColumnReturnTypeConst::RETURN\_SPECIFIED, you must use return\_names to specify the columns to be returned.
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
            'query_type' => QueryTypeConst::WILDCARD_QUERY,
            'query' => array(
                'field_name' => 'keyword',
                'value' => 'key*'
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
        'return_names' => array('keyword')
    )
);
$response = $otsClient->search($request);
```

