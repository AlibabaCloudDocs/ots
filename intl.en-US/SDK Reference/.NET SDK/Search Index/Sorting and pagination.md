# Sorting and pagination

You can specify IndexSort when you create a search index and specify a sorting method when you query data. You can use limit and offset or tokens for pagination.

## Index sorting

By default, a search index is sorted based on the specified IndexSort field. When you use a search index to query data, IndexSort determines the default order in which the matched data is returned.

When you create a search index, you can customize the IndexSort value. By default, if you do not specify IndexSort, the primary key order is used.

**Notice:** Search indexes of the NEST type do not support IndexSort.

## Specify a sorting method

Sorting can be enabled only for fields for which EnableSortAndAgg is set to true.

You can specify a sorting method for each query. Search index-based queries support the following sorting methods: You can also specify multiple sorting methods based on different priorities.

-   ScoreSort

    You can use ScoreSort to sort the query result based on the BM25-based keyword relevance score. ScoreSort is applicable to scenarios such as full-text search.

    **Note:** You must set ScoreSort to sort the query result by keyword relevance score. Otherwise, the query result is sorted based on the value of the IndexSort field.

    ```
    var searchQuery = new SearchQuery();
    searchQuery.Sort = new Sort(new List<ISorter>() { new ScoreSort() });
    ```

-   PrimaryKeySort

    You can use PrimaryKeySort to sort the query result based on the order of primary key columns.

    ```
    // Sort the query result based on the primary key columns in ascending order.
    var searchQuery = new SearchQuery();
    searchQuery.Sort = new Sort(new List<ISorter>() { new PrimaryKeySort() });
    
    // Sort the query result based on the primary key columns in descending order.
    var searchQuery = new SearchQuery();
    searchQuery.Sort = new Sort(new List<ISorter>() { new PrimaryKeySort(SortOrder.DESC) });
    ```

-   FieldSort

    You can use FieldSort to sort the query result based on a specified column.

    ```
    var searchQuery = new SearchQuery();
    var fieldSort = new FieldSort("col", SortOrder.ASC);
    searchQuery.Sort = new Sort(new List<ISorter>() { fieldSort });
    ```

    You can sort the query result based on the value of multiple columns.

    ```
    var searchQuery = new SearchQuery();
    var col1Sort = new FieldSort("col", SortOrder.ASC);
    var col2Sort = new FieldSort("co2", SortOrder.ASC);
    searchQuery.Sort = new Sort(new List<ISorter>() { col1Sort, col2Sort });
    ```

-   GeoDistanceSort

    You can use GeoDistanceSort to sort the query result by geographical location.

    ```
    var searchQuery = new SearchQuery();
    var geoDistanceSort = new GeoDistanceSort("geoCol", new List<string>(){"0","0"});
    searchQuery.Sort = new Sort(new List<ISorter>() { geoDistanceSort });
    ```


## Specify a pagination method

You can use Limit and Offset or Token for pagination when you obtain returned results.

-   Use the Limit and Offset parameters

    When the total number of rows to obtain is smaller than 10,000, you can specify the Limit and Offset parameters for pagination. The sum of the Limit and Offset parameter values cannot exceed 10,000. The maximum value of Limit is 100.

    **Note:** To set Limit to a value greater than 100, see [How do I increase the limit on the rows of data returned by calling the Search operation to 1,000?](/intl.en-US/FAQ/General FAQ/How do I increase the limit on the rows of data returned by calling the Search operation
         to 1,000?.md).

    If you use Limit and Offset for pagination but do not configure the Limit and Offset values, the default values are used. The default value of Limit is 10. The default value of Offset is 0.

    ```
    var searchQuery = new SearchQuery();
    searchQuery.Query = new MatchAllQuery();
    searchQuery.Limit = 100;
    searchQuery.Offset = 100;
    ```

-   Use a token

    We recommend that you use a token for deep pagination because this method has no limits on the pagination depth.

    If Tablestore cannot complete reading data that meets the query condition, the server returns NextToken. You can use NextToken to continue reading the subsequent data.

    By default, you can only page backward when you use a token. However, you can cache and use the previous token to implement forward pagination because a token is valid during the query.

    When you use the token, the sorting method is the same as that used in the previous request. Tablestore sorts data based on the IndexSort field by default or based on the method that you have specified. Therefore, you cannot set the sorting method if you use a token. You cannot set Offset when a token is used. Data is returned page by page, which results in a slow query.

    **Note:** Search indexes of the NEST type do not support IndexSort. If you use a search index of the NEST type to query data and require pagination, you must specify the sorting method to return data in the query conditions. Otherwise, Tablestore does not return NextToken when all data that meets the query conditions is not completely read.

    ```
    /// <summary>
    /// If you use a token for pagination, Tablestore reads all data and returns the data in a list.
    /// </summary>
    /// <param name="otsClient"></param>
    public static SearchResponse ReadMoreRowsWithToken(OTSClient otsClient)
    {
        var searchQuery = new SearchQuery();
        searchQuery.Query = new MatchAllQuery();
    
        var request = new SearchRequest(TableName, IndexName, searchQuery);
    
        var response = otsClient.Search(request);
        var rows = response.Rows;
        while (response.NextToken ! =null) // Tablestore stops reading when the value of NextToken is null. null indicates that Tablestore reads all data.
        {
            request.SearchQuery.Token = response.NextToken;
            response = otsClient.Search(request);
            rows.AddRange(response.Rows);
        }
    
        return response;
    }
    ```


