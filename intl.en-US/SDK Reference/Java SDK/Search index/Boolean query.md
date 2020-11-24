# Boolean query

This topic describes how to use Boolean query to query data based on a combination of filter conditions. This type of query contains one or more subqueries as filter conditions. Tablestore returns the rows that match the subqueries. Each subquery can be of any type, including BoolQuery.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialize SDK for Java.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Java SDK/Search index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|mustQueries|The subqueries that the query result must match. This parameter is equivalent to the AND operator.|
|mustNotQueries|The subqueries that the query result must not match. This parameter is equivalent to the NOT operator.|
|filterQueries|The subqueries that all subfilters must match. Filter is similar to query. The difference is that filter does not calculate the relevance score that meets the number of filters.|
|shouldQueries|The subqueries of which the query result must match at least one. This parameter is equivalent to the OR operator.Only rows that meet the minimum number of subqueries specified in shouldQueries can match the result.

The overall relevance score is higher if more subqueries of shouldQueries are met. |
|minimumShouldMatch|The minimum number of subqueries specified in shouldQueries. Default value: 1.|
|tableName|The name of the table.|
|indexName|The name of the search index.|
|columnsToGet|Specifies whether to return all columns of each matched row. You can configure ReturnAll and Columns for this parameter. By default, ReturnAll is set to false, which indicates that not all columns are returned. If ReturnAll is set to false, you can use Columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If ReturnAll is set to true, all columns are returned. |

## Examples

-   Example 1

    Use BoolQuery to query data that matches the And conditions.

    ```
    /**
     * Use BoolQuery to query data that matches the And conditions.
     * @param client
     */
    public static void andQuery(SyncClient client){
        /**
         * Condition 1: Use RangeQuery to query data where the value of Col_Long is greater than 3.
         */
        RangeQuery rangeQuery = new RangeQuery();
        rangeQuery.setFieldName("Col_Long");
        rangeQuery.greaterThan(ColumnValue.fromLong(3));
    
        /**
         * Condition 2: Use MatchQuery to query data where the value of Col_Keyword matches "hangzhou".
         */
        MatchQuery matchQuery = new MatchQuery();
        matchQuery.setFieldName("Col_Keyword");
        matchQuery.setText("hangzhou");
    
        SearchQuery searchQuery = new SearchQuery();
        {
            /**
             * Create a BoolQuery where the query result meets both Conditions 1 and 2.
             */
            BoolQuery boolQuery = new BoolQuery();
            boolQuery.setMustQueries(Arrays.asList(rangeQuery, matchQuery));
            searchQuery.setQuery(boolQuery);
            searchQuery.setGetTotalCount(true);
    
            SearchRequest searchRequest = new SearchRequest("sampleTable", "sampleSearchIndex", searchQuery);
    
            // You can set the columnsToGet parameter to specify the columns to return or specify to return all columns. If you do not set this parameter, only the primary key columns are returned.
            //SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
            //columnsToGet.setReturnAll(true); // Set ReturnAll to true to return all columns.
            //columnsToGet.setColumns(Arrays.asList("ColName1","ColName2")); // Set Columns to return specified columns.
            //searchRequest.setColumnsToGet(columnsToGet);
    
            SearchResponse resp = client.search(searchRequest);
            System.out.println("TotalCount: " + resp.getTotalCount()); //// Display the total number of matched rows.
            System.out.println("Row: " + resp.getRows());
        }
    }
    ```

-   Example 2

    Use BoolQuery to query data that matches the Or conditions.

    ```
    /**
     * Use BoolQuery to query data that matches the Or conditions.
     * @param client
     */
    public static void orQuery(SyncClient client) {
    
        /**
         * Condition 1: Use RangeQuery to query data where the value of Col_Long is greater than 3.
         */
        RangeQuery rangeQuery = new RangeQuery();
        rangeQuery.setFieldName("Col_Long");
        rangeQuery.greaterThan(ColumnValue.fromLong(3));
    
        /**
         * Condition 2: Use MatchQuery to query data where the value of Col_Keyword matches "hangzhou".
         */
        MatchQuery matchQuery = new MatchQuery();
        matchQuery.setFieldName("Col_Keyword");
        matchQuery.setText("hangzhou");
    
        SearchQuery searchQuery = new SearchQuery();
        {
            /**
         * Create a BoolQuery where the query result meets at least one of Conditions 1 and 2.
         */
        BoolQuery boolQuery = new BoolQuery();
        boolQuery.setShouldQueries(Arrays.asList(rangeQuery, matchQuery));
        boolQuery.setMinimumShouldMatch(1); // Specify that the result meets at least one of the conditions.
        searchQuery.setQuery(boolQuery);
        searchQuery.setGetTotalCount(true);
        SearchRequest searchRequest = new SearchRequest("sampleTable", "sampleSearchIndex", searchQuery);
    
        // You can set the columnsToGet parameter to specify the columns to return or specify to return all columns. If you do not set this parameter, only the primary key columns are returned.
        //SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
        columnsToGet.setReturnAll(true); // Set ReturnAll to true to return all columns.
        //columnsToGet.setColumns(Arrays.asList("ColName1","ColName2")); // Set Columns to return specified columns.
        //searchRequest.setColumnsToGet(columnsToGet);
    
        SearchResponse resp = client.search(searchRequest);
        System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows.
        System.out.println("Row: " + resp.getRows());
        }
    }
    ```

-   Example 3

    Use BoolQuery to query data that matches the Not condition.

    ```
    /**
     * Use BoolQuery to query data that matches the Not condition.
     * @param client
     */
    public static void notQuery(SyncClient client) {
    
        /**
         * Condition 1: Use MatchQuery to query data where the value of Col_Keyword matches "hangzhou".
         */
        MatchQuery matchQuery = new MatchQuery();
        matchQuery.setFieldName("Col_Keyword");
        matchQuery.setText("hangzhou");
    
        SearchQuery searchQuery = new SearchQuery();
        {
            /**
             * Create a BoolQuery where the query result does not meet Condition 1.
             */
            BoolQuery boolQuery = new BoolQuery();
            boolQuery.setMustNotQueries(Arrays.asList(matchQuery));
            searchQuery.setQuery(boolQuery);
            searchQuery.setGetTotalCount(true);
            SearchRequest searchRequest = new SearchRequest("sampleTable", "sampleSearchIndex", searchQuery);
    
            // You can set the columnsToGet parameter to specify the columns to return or specify to return all columns. If you do not set this parameter, only the primary key columns are returned.
            //SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
            //columnsToGet.setReturnAll(true); // Set ReturnAll to true to return all columns.
            //columnsToGet.setColumns(Arrays.asList("ColName1","ColName2")); // Set Columns to return specified columns.
            //searchRequest.setColumnsToGet(columnsToGet);
    
            SearchResponse resp = client.search(searchRequest);
            System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows.
            System.out.println("Row: " + resp.getRows());
        }
    }
    ```

-   Example 4

    Perform a Boolean query that includes multiple BoolQueries. In \(col2 < 4 OR col3 < 5\) or \(col2 = 4 AND \(col3 = 5 OR col3 = 6\)\), each BoolQuery is connected by AND or OR.

    ```
    /**
     * (col2<4 or col3<5) or (col2 = 4 and (col3 = 5 or col3 =6))
     * In the preceding example, each BoolQuery is connected by AND or OR.
     * @param client
    */
    private static void boolQuery2(SyncClient client){
            // Condition 1: col2 < 4
            RangeQuery rangeQuery1 = new RangeQuery();
            rangeQuery1.setFieldName("col2");
            rangeQuery1.lessThan(ColumnValue.fromLong(4));
    
            // Condition 2: col3 < 5
            RangeQuery rangeQuery2 = new RangeQuery();
            rangeQuery2.setFieldName("col3");
            rangeQuery2.lessThan(ColumnValue.fromLong(5));
    
            // Condition 3: col2 = 4
            TermQuery termQuery = new TermQuery();
            termQuery.setFieldName("col2");
            termQuery.setTerm(ColumnValue.fromLong(4));
    
            // Condition 4: col3 = 5 OR col3 = 6
            TermsQuery termsQuery = new TermsQuery();
            termsQuery.setFieldName("col3");
            termsQuery.addTerm(ColumnValue.fromLong(5));
            termsQuery.addTerm(ColumnValue.fromLong(6));
    
            SearchQuery searchQuery = new SearchQuery();
    
            List<Query> queryList1 = new ArrayList<>();
            queryList1.add(rangeQuery1);
            queryList1.add(rangeQuery2);
    
            // Combination 1: col2 < 4 OR col3 < 5
            BoolQuery boolQuery1 = new BoolQuery();
            boolQuery1.setShouldQueries(queryList1);
    
            // Combination 2: col2 = 4 AND (col3 = 5 OR col3 = 6)
            List<Query> queryList2 = new ArrayList<>();
            queryList2.add(termQuery);
            queryList2.add(termsQuery);
    
            BoolQuery boolQuery2 = new BoolQuery();
            boolQuery2.setMustQueries(queryList2);
    
            // Final combination: (col2 < 4 OR col3 < 5) or (col2 = 4 AND (col3 = 5 OR col3 = 6))
    
            List<Query> queryList3 = new ArrayList<>();
            queryList3.add(boolQuery1);
            queryList3.add(boolQuery2);
    
            BoolQuery boolQuery = new BoolQuery();
            boolQuery.setShouldQueries(queryList3);
    
            searchQuery.setQuery(boolQuery);
            SearchRequest searchRequest = new SearchRequest("sampleTable", "sampleSearchIndex", searchQuery);
    
            // You can set the columnsToGet parameter to specify the columns to return or specify to return all columns. If you do not set this parameter, only the primary key columns are returned.
            //SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
            columnsToGet.setReturnAll(true); // Set ReturnAll to true to return all columns.
            //columnsToGet.setColumns(Arrays.asList("ColName1","ColName2")); // Set Columns to return specified columns.
            //searchRequest.setColumnsToGet(columnsToGet);
    
            SearchResponse response = client.search(searchRequest);
            System.out.println(response.getRows());
    
        }
    ```


