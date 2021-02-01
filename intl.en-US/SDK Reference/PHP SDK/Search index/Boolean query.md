# Boolean query

This topic describes how to use Boolean query to query data based on a combination of subqueries. Tablestore returns the rows that match the subqueries. Each subquery can be of any type, including BoolQuery.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/PHP SDK/Search index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|table\_name|The name of the table.|
|index\_name|The name of the search index.|
|must\_queries|Only rows that meet all subquery conditions are returned. This parameter is equivalent to the AND operator.|
|must\_not\_queries|Only rows that do not meet any subquery conditions are returned. This parameter is equivalent to the NOT operator.|
|filter\_queries|Only rows that meet all subfilters are returned. Compared with query, filter does not calculate the relevance score based on the number of subfilters that the row meet.|
|should\_queries|The list of subqueries the query result can or cannot match. This parameter is equivalent to the OR operator.Only rows that meet the minimum number of subquery conditions specified by should\_queries are returned.

The overall relevance score is higher if more subquery conditions specified by should\_queries are met. |
|minimum\_should\_match|The minimum number of subquery conditions specified by should\_queries that row data must match. Default value: 1.|

## Examples

In the following example, condition A requires that the value of the keyword column exactly matches 'keyword', and condition B requires that the value of the long column is greater than or equal to 100 and smaller than 101.

-   Example 1

    The following code provides an example on how to use Boolean query to query rows that meet condition A and condition B:

    ```
    $request = array(
        'table_name' => 'php_sdk_test',
        'index_name' => 'php_sdk_test_search_index',
        'search_query' => array(
            'offset' => 0,
            'limit' => 2,
            'get_total_count' => true,
            'query' => array(
                'query_type' => QueryTypeConst::BOOL_QUERY,
                'query' => array(
                    'must_queries' => array(
                        array(
                            'query_type' => QueryTypeConst::TERM_QUERY,
                            'query' => array(
                                'field_name' => 'keyword',
                                'term' => 'keyword'
                            )
                        ),
                        array(
                            'query_type' => QueryTypeConst::RANGE_QUERY,
                            'query' => array(
                                'field_name' => 'long',
                                'range_from' => 100,
                                'include_lower' => true,
                                'range_to' => 101,
                                'include_upper' => false
                            )
                        )
                    ),
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
            'return_names' => array('keyword', 'long')
        )
    );
    $response = $otsClient->search($request);
    ```

-   Example 2

    The following code provides an example on how to use Boolean query A AND ! to query rows that do not meet condition A or condition B:

    ```
    $request = array(
        'table_name' => 'php_sdk_test',
        'index_name' => 'php_sdk_test_search_index',
        'search_query' => array(
            'offset' => 0,
            'limit' => 2,
            'get_total_count' => true,
            'query' => array(
                'query_type' => QueryTypeConst::BOOL_QUERY,
                'query' => array(
                    'must_not_queries' => array(
                        array(
                            'query_type' => QueryTypeConst::TERM_QUERY,
                            'query' => array(
                                'field_name' => 'keyword',
                                'term' => 'keyword'
                            )
                        ),
                        array(
                            'query_type' => QueryTypeConst::RANGE_QUERY,
                            'query' => array(
                                'field_name' => 'long',
                                'range_from' => 100,
                                'include_lower' => true,
                                'range_to' => 101,
                                'include_upper' => false
                            )
                        )
                    ),
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
            'return_names' => array('keyword', 'long')
        )
    );
    $response = $otsClient->search($request);
    ```

-   Example 3

    The following code provides an example on how to use Boolean query to filter the query result and return rows that meet condition A or condition B. filter does not calculate the relevance score based on the number of subfilters that the row meet.

    ```
    $request = array(
        'table_name' => 'php_sdk_test',
        'index_name' => 'php_sdk_test_search_index',
        'search_query' => array(
            'offset' => 0,
            'limit' => 2,
            'get_total_count' => true,
            'query' => array(
                'query_type' => QueryTypeConst::BOOL_QUERY,
                'query' => array(
                    'filter_queries' => array(
                        array(
                            'query_type' => QueryTypeConst::TERM_QUERY,
                            'query' => array(
                                'field_name' => 'keyword',
                                'term' => 'keyword'
                            )
                        ),
                        array(
                            'query_type' => QueryTypeConst::RANGE_QUERY,
                            'query' => array(
                                'field_name' => 'long',
                                'range_from' => 100,
                                'include_lower' => true,
                                'range_to' => 101,
                                'include_upper' => false
                            )
                        )
                    ),
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
            'return_names' => array('keyword', 'long')
        )
    );
    $response = $otsClient->search($request);
    ```

-   ShouldQueries\(A OR B\)

    The following code provides an example on how to query rows that meet condition A or condition B:

    ```
    $request = array(
        'table_name' => 'php_sdk_test',
        'index_name' => 'php_sdk_test_search_index',
        'search_query' => array(
            'offset' => 0,
            'limit' => 2,
            'get_total_count' => true,
            'query' => array(
                'query_type' => QueryTypeConst::BOOL_QUERY,
                'query' => array(
                    'should_queries' => array(
                        array(
                            'query_type' => QueryTypeConst::TERM_QUERY,
                            'query' => array(
                                'field_name' => 'keyword',
                                'term' => 'keyword'
                            )
                        ),
                        array(
                            'query_type' => QueryTypeConst::RANGE_QUERY,
                            'query' => array(
                                'field_name' => 'long',
                                'range_from' => 100,
                                'include_lower' => true,
                                'range_to' => 101,
                                'include_upper' => false
                            )
                        )
                    ),
                    'minimum_should_match' => 1
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
            'return_names' => array('keyword', 'long')
        )
    );
    $response = $otsClient->search($request);
    ```

-   Example 4

    The following code provides an example on how to perform a Boolean query that includes multiple BoolQueries. In \(col2<4 or col3<5\) or \(col2 = 4 and \(col3 = 5 or col3 =6\)\), each BoolQuery is connected by AND or OR.

    ```
    $request = array(
        'table_name' => 'php_sdk_test',
        'index_name' => 'php_sdk_test_index',
        'search_query' => [
            'offset' => 0,
            'limit' => 10,
            'get_total_count' => false,
            'query' => [
                'query_type' => QueryTypeConst::BOOL_QUERY,
                'query' => [
                    // Final combination: (col2<4 or col3<5) or (col2 = 4 and (col3 = 5 or col3 =6))
                    'should_queries' => [
                        [
                            'query_type' => QueryTypeConst::BOOL_QUERY,
                            'query' => [
                                // Combination 1: col2 < 4 or col3 < 5
                                'should_queries' => [
                                    [
                                        'query_type' => QueryTypeConst::RANGE_QUERY,
                                        // Condition 1: col2 < 4
                                        'query' => [
                                            'field_name' => 'col2',
                                            'range_to' => 4
                                        ]
                                    ],
                                    [
                                        'query_type' => QueryTypeConst::RANGE_QUERY,
                                        // Condition 2: col3 < 5
                                        'query' => [
                                            'field_name' => 'col3',
                                            'range_to' => 5
                                        ]
                                    ]
                                ]
                            ]
                        ],
                        [
                            'query_type' => QueryTypeConst::BOOL_QUERY,
                            // Combination 2: (col2 = 4 and (col3 = 5 or col3 =6))
                            'query' => [
                                'must_queries' => [
                                    [
                                        'query_type' => QueryTypeConst::TERM_QUERY,
                                        // Condition 3: col2 = 4
                                        'query' => [
                                            'field_name' => 'col2',
                                            'term' => 4
                                        ]
                                    ],
                                    [
                                        'query_type' => QueryTypeConst::BOOL_QUERY,
                                        // Combination 3: (col3 = 5 or col3 =6))
                                        'query' => [
                                            'should_queries' => [
                                                [
                                                    'query_type' => QueryTypeConst::TERM_QUERY,
                                                    // Condition 4: col3=5
                                                    'query' => [
                                                        'field_name' => 'col3',
                                                        'term' => 5
                                                    ]
                                                ],
                                                [
                                                    'query_type' => QueryTypeConst::TERM_QUERY,
                                                    // Condition 5: col3=6
                                                    'query' => [
                                                        'field_name' => 'col3',
                                                        'term' => 6
                                                    ]
                                                ]
                                            ],
                                            'minimum_should_match' => 1
                                        ]
                                    ]
                                ]
                            ]
                        ]
                    ],
                    'minimum_should_match' => 1
                ]
            ]
        ],
        // You can set the columns_to_get parameter to specify the columns to return or specify that all columns are returned. If you do not set this parameter, only the primary key columns are returned.
        'columns_to_get' => [ 
            //'return_type' => ColumnReturnTypeConst::RETURN_ALL // Specify that all columns are returned.
            // Specify that the col1 and col2 columns are returned.
            'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED, 
            'return_names' => array('col1', 'col2')
        ]
    );
    ```


