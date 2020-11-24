# Boolean query

This topic describes how to use Boolean query to query data based on a combination of filter conditions. This type of query contains one or more subqueries as filter conditions. Tablestore returns the rows that match the subqueries. Each subquery can be of any type, including BoolQuery.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/PHP SDK/Search index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|table\_name|The name of the table.|
|index\_name|The name of the search index.|
|must\_queries|Only row data that matches all subqueries is returned. This parameter is equivalent to the AND operator.|
|must\_not\_queries|Only row data that must not match all subqueries is returned. This parameter is equivalent to the NOT operator.|
|filter\_queries|Only row data that matches all subfilters is returned. filter is similar to query. The difference is that filter does not calculate the relevance score based on the number of subfilters that the row meet.|
|should\_queries|The list of subqueries the query result can or cannot match. This parameter is equivalent to the OR operator.Only rows that meet the minimum number of subquery conditions specified in should\_queries can match the result.

The overall relevance score is higher if more subqueries of should\_queries are met. |
|minimum\_should\_match|The minimum number that row data must match for subquery conditions in should\_queries.Default value: 1. |

## Examples

In the following example, the condition A is that the value of the keyword column matches that of the 'keyword', and the condition B is that the value of the long column is greater than or equal to 100 and smaller than 101.

-   MustQueries\(A AND B\)

    The following code provides an example on how to query rows that meet both condition A and B:

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

-   MustNotQueries\(! A AND ! B\)

    The following code provides an example on how to query rows that do not meet condition A or B:

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

-   FilterQueries\(A AND B\)

    The following code provides an example on how to query rows that meet both condition A and B. Data of rows does not calculate the relevance score based on the number that meets conditions.

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

    The following code provides an example on how to query rows that meet condition A or B:

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


