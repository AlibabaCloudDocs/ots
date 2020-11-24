# Sorting and paging

You can specify IndexSort when you create a search index and specify a sorting method when you query data. You can use limit and offset or tokens for paging.

## Index sorting

The matched data is sorted based on the IndexSort field value when search indexes are used in a query. Search indexes of the NEST type do not support IndexSort. Tablestore returns the query result based on the default IndexSort field value, which is the order of primary key columns. You can specify the IndexSort field when you create a search index. If you do not specify the IndexSort field, Tablestore returns the query result based on the order of primary key columns.

**Note:** Search indexes of the NEST type do not support IndexSort.

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
    Ascending order:
    var searchQuery = new SearchQuery();
    searchQuery.Sort = new Sort(new List<ISorter>() { new PrimaryKeySort() });
    
    Descending order:
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


## Specify a paging method

You can use limit and offset or token for paging when you query returned results.

-   Use the Limit and Offset parameters

    When the total number of rows to obtain is smaller than 10,000, you can specify the Limit and Offset parameters for paging. The sum of the Limit and Offset parameter values cannot exceed 10,000.

    ```
    var searchQuery = new SearchQuery();
    searchQuery.Query = new MatchAllQuery();
    searchQuery.Limit = 100;
    searchQuery.Offset = 100;
    ```

-   Use a token

    If Tablestore is not able to complete reading data that meets the filter condition, the server returns nextToken. You can use nextToken to continue reading the subsequent data.

    When you use the token, the sorting method is the same as that used in the previous request. The system sorts data based on the IndexSort field by default or based on the method that you have specified. Therefore, you cannot set the sorting method if you use a token. You cannot set offset when a token is used. Data is returned page by page, which results in a slow query.

    ```
    /// <summary>
    /// If you use a token for paging, the system reads all data and returns the data in a list.
    /// </summary>
    /// <param name="otsClient"></param>
    public static SearchResponse ReadMoreRowsWithToken(OTSClient otsClient)
    {
        var searchQuery = new SearchQuery();
        searchQuery.Query = new MatchAllQuery();
    
        var request = new SearchRequest(TableName, IndexName, searchQuery);
    
        var response = otsClient.Search(request);
        var rows = response.Rows;
        while (response.NextToken ! =null) { // The system stops reading when the value of NextToken is null. null indicates that the system reads all data.
        {
            request.SearchQuery.Token = response.NextToken;
            response = otsClient.Search(request);
            rows.AddRange(response.Rows);
        }
    
        return response;
    }
    ```


