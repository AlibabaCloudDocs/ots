# Sorting and pagination

You can specify IndexSort when you create a search index and specify a sorting method when you query data. You can use limit and offset or tokens for pagination.

## Index sorting

By default, a search index is sorted based on the specified IndexSort field. When you use a search index to query data, IndexSort determines the default order in which the matched data is returned.

When you create a search index, you can customize the IndexSort value. By default, if you do not specify IndexSort, the primary key order is used.

**Notice:** Search indexes of the NEST type do not support IndexSort.

## Specify a sorting method

Sorting can be enabled only for fields for which enable\_sort\_and\_agg is set to true.

You can specify a sorting method for each query. Search index-based queries support the following sorting methods: You can also specify multiple sorting methods based on different priorities.

-   ScoreSort

    You can use ScoreSort to sort the query result based on the BM25-based keyword relevance score. ScoreSort is applicable to scenarios such as full-text search.

    **Note:** You must set ScoreSort to sort the query result by keyword relevance score. Otherwise, the query result is sorted based on the value of the IndexSort field.

    ```
    'sort' => array(
        array(
            'score_sort' => array(
                'order' => SortOrderConst::SORT_ORDER_DESC
            )
        ),
    )
    ```

-   PrimaryKeySort

    You can use PrimaryKeySort to sort the query result based on the order of primary key columns.

    ```
    'sort' => array(
        array(
            'pk_sort' => array(
                'order' => SortOrderConst::SORT_ORDER_ASC
            )
        ),
    )
    ```

-   FieldSort

    You can use FieldSort to sort the query result based on a specified column.

    ```
    'sort' => array(
        array(
            'field_sort' => array(
                'field_name' => 'keyword',
                'order' => SortOrderConst::SORT_ORDER_ASC,
                'mode' => SortModeConst::SORT_MODE_AVG,
            )
        ),
    )
    ```

-   GeoDistanceSort

    You can use GeoDistanceSort to sort the query result by geographical location.

    ```
    'sort' => array(
        array(
            'geo_distance_sort' => array(
                'field_name' => 'geo',
                'order' => SortOrderConst::SORT_ORDER_ASC,
                'distance_type' => GeoDistanceTypeConst::GEO_DISTANCE_PLANE,
                'points' => array('0.6,0.6')
            )
        ),
    )
    ```

-   Combination of multiple sorting methods

    You can sort the query result based on the order of multiple columns.

    ```
    'sort' => array(
        array(
            'field_sort' => array(
                'field_name' => 'keyword',
                'order' => SortOrderConst::SORT_ORDER_ASC,
                'mode' => SortModeConst::SORT_MODE_AVG,
            )
        ),
        array(
            'pk_sort' => array(
                'order' => SortOrderConst::SORT_ORDER_ASC
            )
        ),
    )
    ```


## Specify a pagination method

You can use limit and offset or use tokens to split returned query results into pages.

-   Use the limit and offset parameters

    When the total number of rows to obtain is smaller than 10,000, you can specify the limit and offset parameters for pagination. The sum of the limit and offset parameter values cannot exceed 10,000. The maximum value of limit is 100.

    **Note:** To set limit to a value greater than 100, see [How do I increase the limit on the rows of data returned by calling the Search operation to 1,000?](/intl.en-US/FAQ/General FAQ/How do I increase the limit on the rows of data returned by calling the Search operation
         to 1,000?.md).

    If you use limit and offset for pagination but do not configure the limit and offset values, the default values are used. The default value of limit is 10. The default value of offset is 0.

    ```
    $request = array(
        'table_name' => 'php_sdk_test',
        'index_name' => 'php_sdk_test_search_index',
        'search_query' => array(
            'offset' => 10,
            'limit' => 10,
            'get_total_count' => true,
            'query' => array(
                'query_type' => QueryTypeConst::MATCH_ALL_QUERY
            ),
            'sort' => array(
                array(
                    'field_sort' => array(
                        'field_name' => 'keyword',
                        'order' => SortOrderConst::SORT_ORDER_ASC
                    )
                ),
            ),
            'token' => null,
        ),
        'columns_to_get' => array(
            'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
            'return_names' => array('col1', 'col2')
        )
    );
    $response = $otsClient->search($request);
    ```

-   Use a token

    We recommend that you use a token for deep pagination because this method has no limits on the pagination depth.

    If Tablestore is not able to complete reading data that meets the filter condition, the server returns next\_token. You can use next\_token to continue reading the subsequent data.

    By default, you can only page backward when you use a token. However, you can cache and use the previous token to implement forward pagination because a token is valid during the query.

    When you use the token, the sorting method is the same as that used in the previous request. Tablestore sorts data based on the IndexSort field by default or based on the method that you have specified. Therefore, you cannot set the sorting method if you use a token. You cannot set offset when a token is used. Data is returned page by page, which results in a slow query.

    **Note:** Search indexes of the NEST type do not support IndexSort. If you use a search index of the NEST type to query data and require pagination, you must specify the sorting method to return data in the query conditions. Otherwise, Tablestore does not return next\_token when all data that meets the query conditions is not completely read.

    ```
    $request = array(
        'table_name' => 'php_sdk_test',
        'index_name' => 'php_sdk_test_search_index',
        'search_query' => array(
            'offset' => 0,
            'limit' => 10,
            'get_total_count' => true,
            'query' => array(
                'query_type' => QueryTypeConst::FUNCTION_SCORE_QUERY,
                'query' => array(
                    'query' => array(
                        'query_type' => QueryTypeConst::TERM_QUERY,
                        'query' => array(
                            'field_name' => 'keyword',
                            'term' => 'keyword'
                        )
                    ),
                    'field_value_factor' => array(
                        'field_name' => 'long'
                    )
                )
            ),
            'sort' => array(
                array(
                    'score_sort' => array(
                        'order' => SortOrderConst::SORT_ORDER_DESC
                    )
                ),
            )
        ),
        'columns_to_get' => array(
            'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
            'return_names' => array('keyword', 'long')
        )
    );
    
    $response = $otsClient->search($request);
    print "total_hits: " . $response['total_hits'] . "\n";
    print json_encode($response['rows'], JSON_PRETTY_PRINT);
    
    while($response['next_token'] ! = null) {
        $request['search_query']['token'] = $response['next_token'];
        $request['search_query']['sort'] = null;// If you set next_token, you do not need to specify the sorting method because the token includes the sorting information.
        $response = $otsClient->search($request);
        print json_encode($response['rows'], JSON_PRETTY_PRINT);
    }
    ```


