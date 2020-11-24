# Sorting and paging

You can specify IndexSort when you create a search index and specify a sorting method when you query data. You can use limit and offset or tokens for paging.

## Index sorting

The matched data is sorted based on the IndexSort field value when search indexes are used in a query. Search indexes of the NEST type do not support IndexSort. Tablestore returns the query result based on the default IndexSort field value, which is the order of primary key columns. You can specify the IndexSort field when you create a search index. If you do not specify the IndexSort field, Tablestore returns the query result based on the order of primary key columns.

**Note:** Search indexes of the NEST type do not support IndexSort.

## Specify a sorting method

Sorting can be enabled only for fields for which enable\_sort\_and\_agg is set to true.

You can specify a sorting method for each query. Search index-based queries support the following sorting methods: You can also specify multiple sorting methods based on different priorities.

-   ScoreSort

    You can use ScoreSort to sort the query result based on the BM25-based keyword relevance score. ScoreSort is applicable to scenarios such as full-text search.

    **Note:** You must set ScoreSort to sort the query result by keyword relevance score. Otherwise, the query result is sorted based on the value of the IndexSort field.

    Set sorting fields:

    ```
    sort = Sort(
        sorters=[ScoreSort(sort_order=SortOrder.DESC)]
    )
    client.search(
        table_name, index_name, SearchQuery(query, sort=sort, limit=100, get_total_count=True), ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```

-   PrimaryKeySort

    You can use PrimaryKeySort to sort the query result based on the order of primary key columns.

    Set sorting fields:

    ```
    sort = Sort(
        sorters=[PrimaryKeySort(sort_order=SortOrder.DESC)]
    )
    client.search(
        table_name, index_name, SearchQuery(query, sort=sort, limit=100, get_total_count=True), ColumnsToGet(return_type=ColumnReturnType.ALL)
    ) = PrimaryKeySort(sort_order=SortOrder.DESC)
    ```

-   FieldSort

    You can use FieldSort to sort the query result based on a specified column.

    Set sorting fields:

    ```
    sort = Sort(
        sorters=[FieldSort('l', SortOrder.ASC)]
    )
    
    client.search(
        table_name, index_name, SearchQuery(query, sort=sort, limit=100, get_total_count=True), ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```

    Sort NEST fields:

    ```
    sort = Sort(
        sorters=[
            FieldSort(
                'n.nl', 
                sort_order=SortOrder.ASC, 
                nested_filter=NestedFilter('n', RangeQuery('n.nl', range_from=150, range_to=200))
            )
        ]
    )
    client.search(
        table_name, index_name, SearchQuery(query, sort=sort, limit=100, get_total_count=True), ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```

    You can sort the query result based on the value of multiple columns.

    Set sorting fields:

    ```
    sort = Sort(
        sorters=[
            FieldSort('a', SortOrder.ASC),
            FieldSort('b', SortOrder.ASC)
        ]
    )
    
    client.search(
        table_name, index_name, SearchQuery(query, sort=sort, limit=100, get_total_count=True), ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```

-   GeoDistanceSort

    You can use GeoDistanceSort to sort the query result by geographical location.

    Set sorting fields:

    ```
    sort = Sort(
        sorters=[GeoDistanceSort('g', ['32.5,116.5', '32.0,116.0'], sort_order=SortOrder.DESC, sort_mode=SortMode.MAX)]
    )
    
    client.search(
        table_name, index_name, SearchQuery(query, sort=sort, limit=100, get_total_count=True), ColumnsToGet(return_type=ColumnReturnType.ALL)
    )    
    ```


## Specify a paging method

You can use limit and offset or token for paging when you query returned results.

-   Use the limit and offset parameters

    When the total number of rows to obtain is smaller than 10,000, you can specify the limit and offset parameters for pagination. The sum of the limit and offset parameter values cannot exceed 10,000.

    ```
    query = RangeQuery('k', 'key100', 'key500', include_lower=False, include_upper=False)
    rows, next_token, total_count, is_all_succeed = client.search(
        table_name, index_name, 
        SearchQuery(query, offset=100, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    )      
    ```

-   Use a token

    If Tablestore is not able to complete reading data that meets the filter condition, the server returns next\_token. You can use next\_token to continue reading the subsequent data.

    When you use the token, the sorting method is the same as that used in the previous request. The system sorts data based on the IndexSort field by default or based on the method that you have specified. Therefore, you cannot set the sorting method if you use a token. You cannot set offset when a token is used. Data is returned page by page, which results in a slow query.

    ```
    query = MatchAllQuery()
    all_rows = []
    next_token = None
    # first round
    rows, next_token, total_count, is_all_succeed = client.search(table_name, index_name,
            SearchQuery(query, next_token=next_token, limit=100, get_total_count=True),
            columns_to_get=ColumnsToGet(['k', 't', 'g', 'ka', 'la'], ColumnReturnType.SPECIFIED))
    all_rows.extend(rows)
    # loop
    while next_token:
        rows, next_token, total_count, is_all_succeed = client.search(table_name, index_name,
            SearchQuery(query, next_token=next_token, sort=None, limit=100, get_total_count=True),
            columns_to_get=ColumnsToGet(['k', 't', 'g', 'ka', 'la'], ColumnReturnType.SPECIFIED))
        all_rows.extend(rows)
    
    print 'Total rows:', len(all_rows)
    ```


