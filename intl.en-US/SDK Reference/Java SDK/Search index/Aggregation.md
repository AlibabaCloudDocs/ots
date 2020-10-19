# Aggregation

You can use aggregation to obtain the minimum value, maximum value, sum, and average of a column, and the count and distinct count of rows. You can also use aggregation to group the obtained results by column value, range, geographical location, and filter, and implement nesting. You can also use multiple aggregations to perform complex queries.

## Minimum value

The aggregation method used to return the minimum value of a column. This method is similar to the SQL MIN function.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |aggregationName|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |fieldName|The name of the column used to perform the aggregation operation. Only the LONG and DOUBLE data types are supported.|
    |missing|The default value for the column used for the aggregation operation on a row when the column value is empty.

     -   If the missing parameter is not specified, the row is ignored.
    -   If the missing parameter is set, the value of this parameter is used as the column value of the row. |

-   Examples

    ```
    /**
     *  The price of each product is listed in the product table. Query the minimum price of the products produced in Zhejiang.
     *  The equivalent SQL statement: SELECT min(column_price) FROM product where place_of_production = "Zhejiang".
     */
    public void min(SyncClient client) {
        // Create a query request.
        SearchRequest searchRequest = SearchRequest.newBuilder()
            .tableName("tableName")
            .indexName("indexName")
            .searchQuery(
                SearchQuery.newBuilder()
                    .query(QueryBuilders.term("place_of_production","Zhejiang"))
                    .limit(0) // If you want to obtain only the aggregate results, you can set the number of matched results to return to 0 to reduce the response time.
                    .addAggregation(AggregationBuilders.min("min_agg_1", "column_price").missing(100))
                    .build())
            .build();
        // Execute the query request.
        SearchResponse resp = client.search(searchRequest);
        // Obtain the aggregate results.
        System.out.println(resp.getAggregationResults().getAsMinAggregationResult("min_agg_1").getValue());
    }
    ```


## Maximum value

The aggregation method used to return the maximum value of a column. This method is similar to the SQL MAX function.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |aggregationName|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |fieldName|The name of the column used to perform the aggregation operation. Only the LONG and DOUBLE data types are supported.|
    |missing|The default value for the column used for the aggregation operation on a row when the column value is empty.

     -   If the missing parameter is not specified, the row is ignored.
    -   If the missing parameter is set, the value of this parameter is used as the column value of the row. |

-   Examples

    ```
    /**
     * The price of each product is listed in the product table. Query the maximum price of the products produced in Zhejiang.
     * The equivalent SQL statement: SELECT max(column_price) FROM product where place_of_production = "Zhejiang".
     */
    public void max(SyncClient client) {
        // Create a query request.
        SearchRequest searchRequest = SearchRequest.newBuilder()
            .tableName("tableName")
            .indexName("indexName")
            .searchQuery(
                SearchQuery.newBuilder()
                    .query(QueryBuilders.term("place_of_production", "Zhejiang"))
                    .limit(0) // If you want to obtain only the aggregate results, you can set the number of matched results to return to 0 to reduce the response time.
                    .addAggregation(AggregationBuilders.max("max_agg_1", "column_price").missing(0))
                    .build())
            .build();
        // Execute the query request.
        SearchResponse resp = client.search(searchRequest);
        // Obtain the aggregate results.
        System.out.println(resp.getAggregationResults().getAsMaxAggregationResult("max_agg_1").getValue());
    }
    ```


## Sum

The aggregation method used to return the sum of all rows for a numeric column. This method is similar to the SQL SUM function.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |aggregationName|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |fieldName|The name of the column used to perform the aggregation operation. Only the LONG and DOUBLE data types are supported.|
    |missing|The default value for the column used for the aggregation operation on a row when the column value is empty.

     -   If the missing parameter is not specified, the row is ignored.
    -   If the missing parameter is set, the value of this parameter is used as the column value of the row. |

-   Examples

    ```
    /**
     * The sales volume of each product is listed in the product table. Query the total number of the sold products that are produced in Zhejiang. Set the value of the missing parameter to 10.
     * The equivalent SQL statement: SELECT sum(column_price) FROM product where place_of_production="Zhejiang".
     */
    public void sum(SyncClient client) {
        // Create a query request.
        SearchRequest searchRequest = SearchRequest.newBuilder()
            .tableName("tableName")
            .indexName("indexName")
            .searchQuery(
                SearchQuery.newBuilder()
                    .query(QueryBuilders.term("place_of_production", "Zhejiang"))
                    .limit(0) // If you want to obtain only the aggregate results, you can set the number of matched results to return to 0 to reduce the response time.
                    .addAggregation(AggregationBuilders.sum("sum_agg_1", "column_number").missing(10))
                    .build())
            .build();
        // Execute the query request.
        SearchResponse resp = client.search(searchRequest);
        // Obtain the aggregate results.
        System.out.println(resp.getAggregationResults().getAsSumAggregationResult("sum_agg_1").getValue());
    }
    ```


## Average

The aggregation method used to return the average value of all rows for a numeric column. This method is similar to the SQL AVG function.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |aggregationName|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |fieldName|The name of the column used to perform the aggregation operation. Only the LONG and DOUBLE data types are supported.|
    |missing|The default value for the column used for the aggregation operation on a row when the column value is empty.

     -   If the missing parameter is not specified, the row is ignored.
    -   If the missing parameter is set, the value of this parameter is used as the column value of the row. |

-   Examples

    ```
    /**
     * The sales volume of each product is listed in the product table. Query the average price of the products produced in Zhejiang.
     * The equivalent SQL statement: SELECT avg(column_price) FROM product where place_of_production = "Zhejiang".
     */
    public void avg(SyncClient client) {
        // Create a query request.
        SearchRequest searchRequest = SearchRequest.newBuilder()
            .tableName("tableName")
            .indexName("indexName")
            .searchQuery(
                SearchQuery.newBuilder()
                    .query(QueryBuilders.term("place_of_production", "Zhejiang"))
                    .limit(0) // If you want to obtain only the aggregate results, you can set the number of matched results to return to 0 to reduce the response time.
                    .addAggregation(AggregationBuilders.avg("avg_agg_1", "column_number"))
                    .build())
            .build();
        // Execute the query request.
        SearchResponse resp = client.search(searchRequest);
        // Obtain the aggregate results.
        System.out.println(resp.getAggregationResults().getAsAvgAggregationResult("avg_agg_1").getValue());
    }
    ```


## Count

The aggregation method used to return the total number of values of a specified column or the total number of rows in the table. This method is similar to the SQL COUNT function.

**Note:** You can use the following methods to query the total number of rows in a table or the number of rows that match the query conditions:

-   Use the count feature of aggregation. Set the value of count to \* in the request.
-   Use the query feature to obtain the number of rows that match the query conditions. Set the setGetTotalCount parameter to true in the query. Use match all query to obtain the total number of rows in a table.

You can set the value of count to the name of a column to query the number of rows that contain the column in a table. This method applies to sparse columns.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |aggregationName|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |fieldName|The name of the column used to perform the aggregation operation. Only the LONG, DOUBLE, BOOLEAN, KEYWORD, and GEOPOINT data types are supported.|

-   Examples

    ```
    /**
     * Punishment records of merchants are recorded in the merchant table. Query the number of merchants in Zhejiang who have punishment records. If merchants have no punishment records, the merchants do not have a value for the specified column.
     * The equivalent SQL statement: SELECT count(column_history) FROM product where place_of_production="Zhejiang".
     */
    public void count(SyncClient client) {
        // Create a query request.
        SearchRequest searchRequest = SearchRequest.newBuilder()
            .tableName("tableName")
            .indexName("indexName")
            .searchQuery(
                SearchQuery.newBuilder()
                    .query(QueryBuilders.term("place", "Zhejiang"))
                    .limit(0)
                    .addAggregation(AggregationBuilders.count("count_agg_1", "column_history"))
                    .build())
            .build();
        // Execute the query request.
        SearchResponse resp = client.search(searchRequest);
        // Obtain the aggregate results.
        System.out.println(resp.getAggregationResults().getAsCountAggregationResult("count_agg_1").getValue());
    }
    ```


## Distinct count

The aggregation method used to return the number of distinct values for a column. This method is similar to the SQL COUNT\(distinct\) function.

**Note:** The number of distinct values is an approximate number.

-   When the number of rows is smaller than 10,000, the calculated result is an exact value.
-   When the number of rows is equal to or greater than 100 million, the error rate is 2%.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |aggregationName|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |fieldName|The name of the column used to perform the aggregation operation. Only the LONG, DOUBLE, BOOLEAN, KEYWORD, and GEOPOINT data types are supported.|
    |missing|The default value for the column used for the aggregation operation on a row when the column value is empty.     -   If the missing parameter is not specified, the row is ignored.
    -   If the missing parameter is set, the value of this parameter is used as the column value of the row. |

-   Examples

    ```
    /**
     * Query the number of distinct provinces from which the products come.
     * The equivalent SQL statement: SELECT count(distinct column_place) FROM product.
     */
    public void distinctCount(SyncClient client) {
        // Create a query request.
        SearchRequest searchRequest = SearchRequest.newBuilder()
            .tableName("tableName")
            .indexName("indexName")
            .searchQuery(
                SearchQuery.newBuilder()
                    .query(QueryBuilders.matchAll())
                    .limit(0)
                    .addAggregation(AggregationBuilders.distinctCount("dis_count_agg_1", "column_place"))
                    .build())
            .build();
        // Execute the query request.
        SearchResponse resp = client.search(searchRequest);
        // Obtain the aggregate results.
        System.out.println(resp.getAggregationResults().getAsDistinctCountAggregationResult("dis_count_agg_1").getValue());
    }
    ```


## Group by column value

The method used to group query results based on column values. The same values are grouped together. The value of each group and the number of corresponding values are returned.

**Note:** The calculated number may be slightly different from the actual number when the number of values in a group is too large.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |groupByName|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |fieldName|The name of the column used to perform the aggregation operation. Only the LONG, DOUBLE, BOOLEAN, and KEYWORD data types are supported.|
    |groupBySorter|The sorting rules for items in a group. By default, group items are sorted in descending order. When you set multiple sorting rules, data is sorted based on the order in which the rules are added. Supported parameters:     -   Sort by value in ascending alphabetical order
    -   Sort by value in descending alphabetical order
    -   Sort by the count of rows in ascending order
    -   Sort by the count of rows in descending order
    -   Sort based on values obtained from the sub-aggregation in ascending order
    -   Sort based on values obtained from the sub-aggregation in descending order |
    |size|The number of returned groups.|
    |subAggregation and subGroupBy|The sub-aggregation. The sub-aggregation operation is performed based on the grouping results.     -   Scenario

Query the number of products in each category and the maximum and minimum product prices in each category.

    -   Method

First group query results by product category, and then add two sub-aggregations to obtain the maximum and minimum product prices in each category.

    -   Result
        -   Fruits: 5. The maximum price is CNY 15. The minimum price is CNY 3.
        -   Toiletries: 10. The maximum price is CNY 98. The minimum price is CNY 1.
        -   Electronic devices: 3. The maximum price is CNY 8,699. The minimum price is CNY 2,300.
        -   Other products: 15. The maximum price is CNY 1,000. The minimum price is CNY 80. |

-   Example 1

    ```
    /**
     * Query the number of products and the maximum and minimum product prices in each category.
     * Example of returned results: Fruits: 5. The minimum price is CNY 3, and the maximum price is CNY 15. Toiletries: 10. The minimum price is CNY 1, and the maximum price is CNY 98. Electronic devices: 3. The minimum price is CNY 2,300, and the maximum price is CNY 8,699. Other products: 15. The minimum price is CNY 80,
     and the maximum price is CNY 1,000.
     */
    public void groupByField(SyncClient client) {
        // Create a query request.
        SearchRequest searchRequest = SearchRequest.newBuilder()
            .tableName("tableName")
            .indexName("indexName")
            .searchQuery(
                SearchQuery.newBuilder()
                    .query(QueryBuilders.matchAll())
                    .limit(0)
                    .addGroupBy(GroupByBuilders
                        .groupByField("name1", "column_type")
                        .addSubAggregation(AggregationBuilders.min("subName1", "column_price"))
                        .addSubAggregation(AggregationBuilders.max("subName2", "column_price"))
                    )
                    .build())
            .build();
        // Execute the query request.
        SearchResponse resp = client.search(searchRequest);
        // Obtain the aggregate results.
        for (GroupByFieldResultItem item : resp.getGroupByResults().getAsGroupByFieldResult("name1").getGroupByFieldResultItems()) {
            // Display values.
            System.out.println(item.getKey());
            // Display the number of rows.
            System.out.println(item.getRowCount());
            // Display the minimum prices.
            System.out.println(item.getSubAggregationResults().getAsMinAggregationResult("subName1").getValue());
            // Display the maximum prices.
            System.out.println(item.getSubAggregationResults().getAsMaxAggregationResult("subName2").getValue());
        }
    }
    ```

-   Example 2

    ```
        /**
         * Group data based on multiple columns.
         * Search index supports the use of nested GroupByFields instead of the GROUP BY syntax in SQL to group data based on multiple columns.
         * The following code provides an example on how to group data based on multiple columns, which serves the same purpose as SQL statement select a,d, sum(b), sum(c) from user group by a,d.
         */
        public void GroupByMultiField() {
            SearchRequest searchRequest = SearchRequest.newBuilder()
                .tableName("tableName")
                .indexName("indexName")
                .returnAllColumns(true)   // Set the parameter to false and specify addColumnsToGet to improve performance.
                //.addColumnsToGet("col_1","col_2")
                .searchQuery(SearchQuery.newBuilder()
                    .query(QueryBuilders.matchAll())   // Add filter conditions as the WHERE clause. You can use QueryBuilders.bool() to perform nested queries.
                    .addGroupBy(
                        GroupByBuilders
                            .groupByField("unique name_1", "field_a")
                            .size(20)
                            .addSubGroupBy(
                                GroupByBuilders
                                    . groupByField("unique name_2", "field_d")
                                    .size(20)
                                    .addSubAggregation(AggregationBuilders.sum("unique name_3", "field_b"))
                                    .addSubAggregation(AggregationBuilders.sum("unique name_4", "field_c"))
                            )
                    )
                    .build())
                .build();
            SearchResponse response = client.search(searchRequest);
            // Query rows that meet the specified conditions.
            List<Row> rows = response.getRows();
            // Obtain the aggregate results.
            GroupByFieldResult groupByFieldResult1 = response.getGroupByResults().getAsGroupByFieldResult("unique identifier_1");
            for (GroupByFieldResultItem resultItem : groupByFieldResult1.getGroupByFieldResultItems()) {
                System.out.println("field_a key:" + resultItem.getKey() + " Count:" + resultItem.getRowCount());
                // Obtain the sub-aggregate results.
                GroupByFieldResult subGroupByResult = resultItem.getSubGroupByResults().getAsGroupByFieldResult("unique identifier_2");
                for (GroupByFieldResultItem item : subGroupByResult.getGroupByFieldResultItems()) {
                    System.out.println("field_a " + resultItem.getKey() + " field_d key:" + item.getKey() + " Count:" + item.getRowCount());
                    double sumOf_field_b = item.getSubAggregationResults().getAsSumAggregationResult("unique identifier_3").getValue();
                    double sumOf_field_c = item.getSubAggregationResults().getAsSumAggregationResult("unique name_4").getValue();
                    System.out.println("sumOf_field_b:" + sumOf_field_b);
                    System.out.println("sumOf_field_c:" + sumOf_field_c);
                }
            }
        }
    ```

-   Example 3

    ```
        /**
         * Set sorting rules for aggregation.
         * Method: Set sorting rules for GroupBySorter. When you set multiple sorting rules, data is sorted based on the order in which the rules are added. GroupBySorter supports sorting in ascending or descending order.
         * By default, data is sorted based on the count of rows in descending order (GroupBySorter.rowCountSortInDesc()).
         */
        public void groupByFieldWithSort(SyncClient client) {
            // Create a query request.
            SearchRequest searchRequest = SearchRequest.newBuilder()
                .tableName("tableName")
                .indexName("indexName")
                .searchQuery(
                    SearchQuery.newBuilder()
                        .query(QueryBuilders.matchAll())
                        .limit(0)
                        .addGroupBy(GroupByBuilders
                            .groupByField("name1", "column_type")
                            //.addGroupBySorter(GroupBySorter.subAggSortInAsc("subName1")) // Sort the data based on the values from sub-aggregate results in ascending order.
                            .addGroupBySorter(GroupBySorter.groupKeySortInAsc())           // Sort the data based on the values obtained from aggregate results in ascending order.
                            //.addGroupBySorter(GroupBySorter.rowCountSortInDesc())             // Sort the data based on the count of rows obtained from aggregate results in descending order.
                            .size(20)
                            .addSubAggregation(AggregationBuilders.min("subName1", "column_price"))
                            .addSubAggregation(AggregationBuilders.max("subName2", "column_price"))
                        )
                        .build())
                .build();
            // Execute the query request.
            SearchResponse resp = client.search(searchRequest);
        }
    ```


## Group by range

The method used to group query results based on value ranges of a column. Column values that fall within a specified range are grouped together. The number of values in each range is returned.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |groupByName|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |fieldName|The name of the column used to perform the aggregation operation. Only the LONG and DOUBLE data types are supported.|
    |range\[double\_from, double\_to\)|The value ranges for grouping. The value range can start from Double.MIN\_VALUE and end at Double.MAX\_VALUE. |
    |subAggregation and subGroupBy|The sub-aggregation. The sub-aggregation operation is performed based on the grouping results. For example, after you group query results by sales volume and then by province, you can obtain the result of which province has the largest proportion in a specified range of sales volume. You must specify GroupByField in GroupByRange to run this query. |

-   Examples

    ```
    /**
     * Group sales volume based on ranges [0, 1000), [1000, 5000), and [5000, ∞) to obtain the sales volume in each range.
     */
    public void groupByRange(SyncClient client) {
        // Create a query request.
        SearchRequest searchRequest = SearchRequest.newBuilder()
            .tableName("tableName")
            .indexName("indexName")
            .searchQuery(
                SearchQuery.newBuilder()
                    .query(QueryBuilders.matchAll())
                    .limit(0)
                    .addGroupBy(GroupByBuilders
                        .groupByRange("name1", "column_number")
                        .addRange(0, 1000)
                        .addRange(1000, 5000)
                        .addRange(5000, Double.MAX_VALUE)
                    )
                    .build())
            .build();
        // Execute the query request.
        SearchResponse resp = client.search(searchRequest);
        // Obtain the aggregate results.
        for (GroupByRangeResultItem item : resp.getGroupByResults().getAsGroupByRangeResult("name1").getGroupByRangeResultItems()) {
    
            // Display the number of rows.
            System.out.println(item.getRowCount());
        }
    }
    ```


## Group by geographical location

The method used to group query results based on their geographical distances to a central point. Query results in distances that fall within a specified range are grouped together. The number of items in each range is returned.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |groupByName|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |fieldName|The name of the column used to perform the aggregation operation. Only the GEOPOINT data type is supported.|
    |origin\(double lat, double lon\)|The longitude and latitude of the central point. double lat specifies the latitude of the central point. double lon specifies the longitude of the central point. |
    |range\[double\_from, double\_to\)|The ranges for grouping. Unit: m. The value range can start from Double.MIN\_VALUE and end at Double.MAX\_VALUE. |
    |subAggregation and subGroupBy|The sub-aggregation. The sub-aggregation operation is performed based on the grouping results.|

-   Examples

    ```
    /**
     * Group people based on their geographical distances to a Wanda Plaza to obtain the result of the number of people in each distance range. Group the distances based on ranges [0, 1000), [1000, 5000), and [5000, Double.MAX_VALUE). The unit is in meters.
     */
    public void groupByGeoDistance(SyncClient client) {
        // Create a query request.
        SearchRequest searchRequest = SearchRequest.newBuilder()
            .tableName("tableName")
            .indexName("indexName")
            .searchQuery(
                SearchQuery.newBuilder()
                    .query(QueryBuilders.matchAll())
                    .limit(0)
                    .addGroupBy(GroupByBuilders
                        .groupByGeoDistance("name1", "column_geo_point")
                        .origin(3.1, 6.5)
                        .addRange(0, 1000)
                        .addRange(1000, 5000)
                        .addRange(5000, Double.MAX_VALUE)
                    )
                    .build())
            .build();
        // Execute the query request.
        SearchResponse resp = client.search(searchRequest);
        // Obtain the aggregate results.
        for (GroupByGeoDistanceResultItem item : resp.getGroupByResults().getAsGroupByGeoDistanceResult("name1").getGroupByGeoDistanceResultItems()) {
           // Display the number of rows.
            System.out.println(item.getRowCount());
        }
    }
    ```


## Group by filter

The method used to filter the query results and group them together to obtain the result of the number of items that match each filter. Results are returned in the order in which the filters are added.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |groupByName|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |filter|The filters for the query. Results are returned in the order in which the filters are added.|
    |subAggregation and subGroupBy|The sub-aggregation. The sub-aggregation operation is performed based on the grouping results.|

-   Examples

    ```
    /**
     * Add the following three filters to obtain the number of items that match each filter: The sales volume exceeds 100, the production place is Zhejiang, and the description contains Hangzhou.
     */
    public void groupByFilter(SyncClient client) {
        // Create a query request.
        SearchRequest searchRequest = SearchRequest.newBuilder()
            .tableName("tableName")
            .indexName("indexName")
            .searchQuery(
                SearchQuery.newBuilder()
                    .query(QueryBuilders.matchAll())
                    .limit(0)
                    .addGroupBy(GroupByBuilders
                        .groupByFilter("name1")
                        .addFilter(QueryBuilders.range("number").greaterThanOrEqual(100))
                        .addFilter(QueryBuilders.term("place","Zhejiang"))
                        .addFilter(QueryBuilders.match("text","Hangzhou"))
                    )
                    .build())
            .build();
        // Execute the query request.
        SearchResponse resp = client.search(searchRequest);
        // Obtain the aggregate results based on the order of filter conditions.
        for (GroupByFilterResultItem item : resp.getGroupByResults().getAsGroupByFilterResult("name1").getGroupByFilterResultItems()) {
            // Display the number of rows.
            System.out.println(item.getRowCount());
        }
    }
    ```


## Nesting

GroupBy supports nesting. You can add sub-aggregations to a GroupBy.

Nesting is used to perform sub-aggregations within a group. Take two-level nesting aggregations as an example:

-   GroupBy + SubGroupBy: Groups items by province and then by city to obtain data for each city in each province.
-   GroupBy + SubAggregation: Groups items by province to obtain the maximum value of a metric for each province.

**Note:** GroupBy can contain endless levels for nesting. However, to ensure the performance and facilitate GroupBy operations, you are allowed only to configure a small number of levels for nesting. For more information, see [Limits](/intl.en-US/Developer Guide/Search Index/Limits.md).

Examples

```
/**
 * Perform nesting-based aggregation.
 * Two aggregations and one GroupByField are added to the outermost level. Two sub-aggregations and one GroupByRange are added to GroupByField.
 */
public void subGroupBy(SyncClient client) {
    // Create a query request.
    SearchRequest searchRequest = SearchRequest.newBuilder()
        .indexName("index_name")
        .tableName("table_name")
        .returnAllColumns(true)
        .searchQuery(
            SearchQuery.newBuilder()
                .query(QueryBuilders.match("textField", "hello"))
                .limit(10)
                .addAggregation(AggregationBuilders.min("name1", "fieldName1"))
                .addAggregation(AggregationBuilders.max("name2", "fieldName2"))
                .addGroupBy(GroupByBuilders
                    .groupByField("name3", "fieldName3")
                    .addSubAggregation(AggregationBuilders.max("subName1", "fieldName4"))
                    .addSubAggregation(AggregationBuilders.sum("subName2", "fieldName5"))
                    .addSubGroupBy(GroupByBuilders
                        .groupByRange("subName3", "fieldName6")
                        .addRange(12, 90)
                        .addRange(100, 900)
                    ))
                .build())
        .build();
    // Execute the query request.
    SearchResponse resp = client.search(searchRequest);
    // Obtain the maximum and minimum values for the first level.
    AggregationResults aggResults = resp.getAggregationResults();
    System.out.println(aggResults.getAsMinAggregationResult("name1").getValue());
    System.out.println(aggResults.getAsMaxAggregationResult("name2").getValue());

    // Obtain the GroupByField results of the first level and the results of the aggregations nested in the GroupByField.
    GroupByFieldResult results = resp.getGroupByResults().getAsGroupByFieldResult("someName1");
    for (GroupByFieldResultItem item : results.getGroupByFieldResultItems()) {
        System.out.println("Count" + item.getRowCount());
        System.out.println("key:" + item.getKey());

        // Obtain the sub-aggregate results.
        // Display the maximum value obtained from the sub-aggregation operation.
        System.out.println(item.getSubAggregationResults().getAsMaxAggregationResult("subName1"));
        // Display the sum obtained from the sub-aggregation operation.
        System.out.println(item.getSubAggregationResults().getAsSumAggregationResult("subName2"));
        // Display the GroupByRange values obtained from the sub-aggregation operation.
        GroupByRangeResult subResults = resp.getGroupByResults().getAsGroupByRangeResult("subName3");
        for (GroupByRangeResultItem subItem : subResults.getGroupByRangeResultItems()) {
            System.out.println("Count" + subItem.getRowCount());
            System.out.println("key" + subItem.getKey());
        }
    }
}
```

## Multiple aggregations

You can perform multiple aggregation operations.

**Note:** Implementing multiple complex aggregation operations at a time may increase response time.

-   Example 1

    ```
    public void multipleAggregation(SyncClient client) {
        // Create a query request.
        SearchRequest searchRequest = SearchRequest.newBuilder()
            .tableName("tableName")
            .indexName("indexName")
            .searchQuery(
                SearchQuery.newBuilder()
                    .query(QueryBuilders.matchAll())
                    .limit(0)
                    .addAggregation(AggregationBuilders.min("name1", "long"))
                    .addAggregation(AggregationBuilders.sum("name2", "long"))
                    .addAggregation(AggregationBuilders.distinctCount("name3", "long"))
                    .build())
            .build();
        // Execute the query request.
        SearchResponse resp = client.search(searchRequest);
        // Obtain the minimum value obtained from the aggregation operation.
        System.out.println(resp.getAggregationResults().getAsMinAggregationResult("name1").getValue());
        // Obtain the sum obtained from the aggregation operation.
        System.out.println(resp.getAggregationResults().getAsSumAggregationResult("name2").getValue());
        // Obtain the distinct count obtained from the aggregation operation.
        System.out.println(resp.getAggregationResults().getAsDistinctCountAggregationResult("name3").getValue());
    }
    ```

-   Example 2

    ```
    public void multipleGroupBy(SyncClient client) {
        // Create a query request.
        SearchRequest searchRequest = SearchRequest.newBuilder()
            .tableName("tableName")
            .indexName("indexName")
            .searchQuery(
                SearchQuery.newBuilder()
                    .query(QueryBuilders.matchAll())
                    .limit(0)
                    .addAggregation(AggregationBuilders.min("name1", "long"))
                    .addAggregation(AggregationBuilders.sum("name2", "long"))
                    .addAggregation(AggregationBuilders.distinctCount("name3", "long"))
                    .addGroupBy(GroupByBuilders.groupByField("name4", "type"))
                    .addGroupBy(GroupByBuilders.groupByRange("name5", "long").addRange(1, 15))
                    .build())
            .build();
        // Execute the query request.
        SearchResponse resp = client.search(searchRequest);
        // Obtain the minimum value obtained from the aggregation operation.
        System.out.println(resp.getAggregationResults().getAsMinAggregationResult("name1").getValue());
        // Obtain the sum obtained from the aggregation operation.
        System.out.println(resp.getAggregationResults().getAsSumAggregationResult("name2").getValue());
        // Obtain the distinct count obtained from the aggregation operation.
        System.out.println(resp.getAggregationResults().getAsDistinctCountAggregationResult("name3").getValue());
        // Obtain the GroupByField values obtained from the aggregation operation.
        for (GroupByFieldResultItem item : resp.getGroupByResults().getAsGroupByFieldResult("name4").getGroupByFieldResultItems()) {
            // Display the keys.
            System.out.println(item.getKey());
            // Display the number of rows.
            System.out.println(item.getRowCount());
        }
        // Obtain the GroupByRange values from the aggregation operation.
        for (GroupByRangeResultItem item : resp.getGroupByResults().getAsGroupByRangeResult("name5").getGroupByRangeResultItems()) {
            // Display the number of rows.
            System.out.println(item.getRowCount());
        }
    }
    ```


