# Sorting and pagination

You can specify IndexSort when you create a search index and specify a sorting method when you query data. You can use limit and offset or tokens for pagination.

## Index sorting

By default, a search index is sorted based on the specified IndexSort field. When you use a search index to query data, IndexSort determines the default order in which the matched data is returned.

When you create a search index, you can customize the IndexSort value. By default, if you do not specify IndexSort, the primary key order is used.

**Note:** Search indexes of the NEST type do not support IndexSort.

## Specify a sorting method

Sorting can be enabled only for fields for which enableSortAndAgg is set to true.

You can specify a sorting method for each query. Search index-based queries support the following sorting methods: You can also specify multiple sorting methods based on different priorities.

-   ScoreSort

    You can use ScoreSort to sort the query result based on the BM25-based keyword relevance score. ScoreSort is applicable to scenarios such as full-text search.

    **Note:** You must set ScoreSort to sort the query result by keyword relevance score. Otherwise, the query result is sorted based on the value of the IndexSort field.

    ```
    SearchQuery searchQuery = new SearchQuery();
    searchQuery.setSort(new Sort(Arrays.asList(new ScoreSort())));
    ```

-   PrimaryKeySort

    You can use PrimaryKeySort to sort the query result based on the order of primary key columns.

    ```
    SearchQuery searchQuery = new SearchQuery();
    searchQuery.setSort(new Sort(Arrays.asList(new PrimaryKeySort()))); // Sort the query result in the ascending order.
    //searchQuery.setSort(new Sort(Arrays.asList(new PrimaryKeySort(SortOrder.DESC)))); // Sort the query result in the descending order.
    ```

-   FieldSort

    You can use FieldSort to sort the query result based on a specified column.

    ```
    SearchQuery searchQuery = new SearchQuery();
    searchQuery.setSort(new Sort(Arrays.asList(new FieldSort("col", SortOrder.ASC))));
    ```

    You can sort the query result based on the value of multiple columns.

    ```
    SearchQuery searchQuery = new SearchQuery();
    searchQuery.setSort(new Sort(Arrays.asList(
        new FieldSort("col1", SortOrder.ASC), new FieldSort("col2", SortOrder.ASC))));
    ```

-   GeoDistanceSort

    You can use GeoDistanceSort to sort the query result by geographical location.

    ```
    SearchQuery searchQuery = new SearchQuery();
    // Sort the result based on the distance from the value in the GEOPOINT geo column to the coordinate pairs (0,0).
    Sort.Sorter sorter = new GeoDistanceSort("geo", Arrays.asList("0, 0"));
    searchQuery.setSort(new Sort(Arrays.asList(sorter)));
    ```


## Specify a pagination method

You can use limit and offset or use tokens to paninate query results.

-   Use the limit and offset parameters

    When the total number of rows to obtain is smaller than 10,000, you can specify the limit and offset parameters for pagination. The sum of the limit and offset parameter values cannot exceed 10,000. The maximum value of limit is 100.

    **Note:** To set limit to a value greater than 100, see [How do I increase the limit on the rows of data returned by calling the Search operation to 1,000?](/intl.en-US/FAQ/General FAQ/How do I increase the limit on the rows of data returned by calling the Search operation
         to 1,000?.md).

    If you use limit and offset for pagination but do not configure the limit and offset values, the default values are used. The default value of limit is 10. The default value of offset is 0.

    ```
    SearchQuery searchQuery = new SearchQuery();
    searchQuery.setQuery(new MatchAllQuery());
    searchQuery.setLimit(100);
    searchQuery.setOffset(100);
    ```

-   Use a token

    We recommend that you use a token for deep pagination because this method has no limits on the pagination depth.

    If Tablestore cannot complete reading all data that meets the query conditions, Tablestore returns nextToken. You can use nextToken to continue reading the subsequent data.

    By default, you can only page backward when you use a token. However, you can cache and use the previous token to implement forward pagination because a token is valid during the query.

    When you use the token, the sorting method is the same as that used in the previous request. Tablestore sorts data based on the IndexSort field by default or based on the method that you have specified. Therefore, you cannot set the sorting method if you use a token. You cannot set offset when a token is used. Data is returned page by page, which results in a slow query.

    **Note:** Search indexes of the NEST type do not support IndexSort. If you use a search index of the NEST type to query data and require pagination, you must specify the sorting method to return data in the query conditions. Otherwise, Tablestore does not return nextToken when only part of data that meets the query conditions is returned.

    ```
    
    /**
     * If you use a token for pagination, Tablestore reads all data and returns the data in a list.
     * @param client
     */
    private static void readMoreRowsWithToken(SyncClient client) {
        SearchQuery searchQuery = new SearchQuery();
        searchQuery.setQuery(new MatchAllQuery());
        searchQuery.setGetTotalCount(true);// Specify that the total number of matched rows is returned.
    
        SearchRequest searchRequest = new SearchRequest("sampleTable", "sampleSearchIndex", searchQuery);
    
        SearchResponse resp = client.search(searchRequest);
        if (! resp.isAllSuccess()) {
            throw new RuntimeException("not all success");
        }
        List<Row> rows = resp.getRows();
        while (resp.getNextToken()! =null) { // Tablestore stops reading when the value of nextToken is null. null indicates that Tablestore reads all data.
            // Set token in this request to the nextToken value in the previous response.
            searchRequest.getSearchQuery().setToken(resp.getNextToken()); 
            resp = client.search(searchRequest);
            if (! resp.isAllSuccess()) {
                throw new RuntimeException("not all success");
            }
            rows.addAll(resp.getRows());
        }
        System.out.println("RowSize: " + rows.size());
        System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows instead of the number of returned rows.
    }
    ```


