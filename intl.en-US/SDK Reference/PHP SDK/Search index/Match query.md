# Match query

You can use match query to query a table based on approximate matches. Tablestore tokenizes the values in TEXT columns and the keywords you use to perform match queries based on the analyzer you set. Therefore, Tablestore can perform match queries based on the tokenized text.

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
|get\_total\_count|Specify whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to true. |
|query\_type|The query type. To use match query, set this parameter to QueryTypeConst::MATCH\_QUERY.|
|field\_name|The name of the column used to match the query conditions.Match query applies to TEXT columns. |
|text|The keyword used for the query to match the column values.If the column used for the match query is a TEXT column, the keyword is tokenized into multiple keywords based on the analyzer you set when you create the search index. By default, single-word tokenization is performed if you do not set the analyzer when you create the search index.

For example, you can specify a TEXT column for a query, and set the analyzer to perform single-word tokenization. If you use the phrase "this is" as the keyword for a query, you can obtain query results such as "..., this is tablestore", "is this tablestore", "tablestore is cool", "this", and "is". |
|operator|The logical operator. By default, OR is used as the logical operator, which indicates that a row matches the query conditions when one of the analyzed keywords is matched.If you set operator to AND, a row meets the query condition only when all tokenized keywords are matched. |
|minimum\_should\_match|The minimum number of matched tokenized keywords contained in a column value.A row is returned only when the value of the queried column in the row contains at least the minimum number of matched tokenized keywords.

**Note:** minimum\_should\_match must be used together with the OR logical operator. |
|columns\_to\_get|Specifies whether to return all columns of each matched rows. You can configure return\_type and return\_names for this parameter. -   If you set return\_type to ColumnReturnTypeConst::RETURN\_SPECIFIED, you can use return\_names to specify the columns to return.
-   If you set return\_type to ColumnReturnTypeConst::RETURN\_ALL, all columns are returned.
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
            'query_type' => QueryTypeConst::MATCH_QUERY,
            'query' => array(
                'field_name' => 'text',
                'text' => 'ots text php keyword',
//              'operator' => QueryOperatorConst::PBAND,
                'operator' => QueryOperatorConst::PBOR,// Use minimum_should_match with OR.
                'minimum_should_match' => 3
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
        'return_names' => array('text')
    )
);
$response = $otsClient->search($request);
```

