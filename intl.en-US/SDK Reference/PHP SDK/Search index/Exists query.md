# Exists query

This query is also called a NULL query or NULL-value query. This query is used for sparse data to determine whether a column in a row exists. For example, you can query the rows in which the value of the address column is not empty.

**Note:**

-   If you want to query whether a column is empty, you must use ExistsQuery in combination with must\_not\_queries in BoolQuery.
-   Assume that a column does not exist. The city column is used in the example.
    -   The city column does not exist in the row.
    -   The city column is an empty array. \("city" = "\[\]"\).

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/PHP SDK/Search index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|table\_name|The name of the table.|
|index\_name|The name of the search index.|
|field\_name|The name of the column.|
|query\_type|Sets the query type to QueryTypeConst::EXISTS\_QUERY.**Note:** You cannot directly query NEST columns. To query a NEST column, you must encapsulate NestedQuery by specifing the path of the NEST field and a subquery. The subquery can be a query of any type. For more information, see [Nested query](/intl.en-US/SDK Reference/PHP SDK/Search index/Nested query.md). |

## Examples

-   Example 1

    The following code provides an example on how to query the rows where the text column contains values:

    ```
    $request = array(
        'table_name' => 'php_sdk_test',
        'index_name' => 'php_sdk_test_search_index',
        'search_query' => array(
            'offset' => 0,
            'limit' => 2,
            'get_total_count' => true,
            'query' => array(
                'query_type' => QueryTypeConst::EXISTS_QUERY,
                'query' => array(
                    'field_name' => 'text'
                )
            )
        ),
        'columns_to_get' => array(
            'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
            'return_names' => array('keyword', 'long', 'array')
        )
    );
    $response = $otsClient->search($request);
    ```

-   Example 2

    The following code provides an example on how to query data that contains values in the nested column of NEST:

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
                'query' => array(
                    'path' => "nested",
                    'query' => array(
                        'query_type' => QueryTypeConst::EXISTS_QUERY,
                        'query' => array(
                            'field_name' => 'nested.nested_long',
                        )
                    ),
                    'score_mode' => ScoreModeConst::SCORE_MODE_AVG
                )
            )
        ),
        'columns_to_get' => array(
            'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
            'return_names' => array('nested')
        )
    );
    $response = $this->otsClient->search($request);
    ```


