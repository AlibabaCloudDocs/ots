# Aggregation

You can use aggregation to obtain the minimum value, maximum value, sum, and average of columns and the count and distinct count of rows. You can also use aggregation to group the obtained results by field value, range, geographical location, or filter. You can also use multiple aggregations to perform complex queries.

## Minimum value

The aggregation method used to return the minimum value of a field. This method is similar to the SQL MIN function.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on its name.|
    |field|The name of the field used to perform the aggregation operation. Only the LONG and DOUBLE data types are supported.|
    |missing|The default value for the field used for the aggregation operation on a row when the field value is empty.

    -   If the missing parameter is not specified, the row is ignored.
    -   If the missing parameter is set, the value of this parameter is used as the field value of the row. |

-   Examples

    The following code provides an example on how to use aggregation to obtain the lowest score from the scores of people aged 18:

    ```
    query = TermQuery('age', 18)
    agg = Min('score', name = 'min')
    
    search_response = client.search(table_name, index_name,
                                    SearchQuery(query, next_token = None, limit=0, aggs=[agg]),
                                    columns_to_get = ColumnsToGet(return_type = ColumnReturnType.ALL_FROM_INDEX))
    
    for agg_result in search_response.agg_results:
        print('{\n"name":"%s",\n"value":%s\n}\n' % (agg_result.name, str(agg_result.value)))
    ```


## Maximum value

The aggregation method used to return the maximum value of a field. This method is similar to the SQL MAX function.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on its name.|
    |field|The name of the field used to perform the aggregation operation. Only the LONG and DOUBLE data types are supported.|
    |missing|The default value for the field used for the aggregation operation on a row when the field value is empty.

    -   If the missing parameter is not specified, the row is ignored.
    -   If the missing parameter is set, the value of this parameter is used as the field value of the row. |

-   Examples

    The following code provides an example on how to use aggregation to obtain the highest score from the scores of people aged 18. By default, if a person has no score, a value of 0 is used as the score for aggregation.

    ```
    query = TermQuery('age', 18)
    agg = Max('score', missing_value = 0, name = 'max')
    
    search_response = client.search(table_name, index_name,
                                    SearchQuery(query, next_token = None, limit=0, aggs=[agg]),
                                    columns_to_get = ColumnsToGet(return_type = ColumnReturnType.ALL_FROM_INDEX))
    
    for agg_result in search_response.agg_results:
        print('{\n"name":"%s",\n"value":%s\n}\n' % (agg_result.name, str(agg_result.value)))
    ```


## Sum

The aggregation method used to return the sum of all rows for a numeric field. This method is similar to the SQL SUM function.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on its name.|
    |field|The name of the field used to perform the aggregation operation. Only the LONG and DOUBLE data types are supported.|
    |missing|The default value for the field used for the aggregation operation on a row when the field value is empty.

    -   If the missing parameter is not specified, the row is ignored.
    -   If the missing parameter is set, the value of this parameter is used as the field value of the row. |

-   Examples

    The following code provides an example on how to use aggregation to obtain a sum of the scores of people aged 18:

    ```
    query = TermQuery('age', 18)
    agg = Sum('score', name = 'sum')
    
    search_response = client.search(table_name, index_name,
                                    SearchQuery(query, next_token = None, limit=2, aggs=[agg]),
                                    columns_to_get = ColumnsToGet(return_type = ColumnReturnType.ALL_FROM_INDEX))
    
    for agg_result in search_response.agg_results:
        print('{\n"name":"%s",\n"value":%s\n}\n' % (agg_result.name, str(agg_result.value)))
    ```


## Average

The aggregation method used to return the average value of all rows for a numeric field. This method is similar to the SQL AVG function.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on its name.|
    |field|The name of the field used to perform the aggregation operation. Only the LONG and DOUBLE data types are supported.|
    |missing|The default value for the field used for the aggregation operation on a row when the field value is empty.

    -   If the missing parameter is not specified, the row is ignored.
    -   If the missing parameter is set, the value of this parameter is used as the field value of the row. |

-   Examples

    The following code provides an example on how to use aggregation to obtain an average of the scores of people aged 18:

    ```
    query = TermQuery('age', 18)
    agg = Avg('score', name = 'avg')
    
    search_response = client.search(table_name, index_name,
                                    SearchQuery(query, next_token = None, limit=2, aggs=[agg]),
                                    columns_to_get = ColumnsToGet(return_type = ColumnReturnType.ALL_FROM_INDEX))
    
    for agg_result in search_response.agg_results:
        print('{\n"name":"%s",\n"value":%s\n}\n' % (agg_result.name, str(agg_result.value)))
    ```


## Count

The aggregation method used to return the total number of values of a specified field or the total number of rows in the table. This method is similar to the SQL COUNT function.

**Note:** You can use the following methods to query the total number of rows in a table or the number of rows that match the query conditions:

-   Use the count feature of aggregation. Set the value of count to \* in the request.
-   Use the query feature to obtain the number of rows that match the query conditions. Set the setGetTotalCount parameter to true in the query. Use match all query to obtain the total number of rows in a table.

You can set the value of count to the name of a column to query the number of rows that contain the column in a table. This method applies to sparse columns.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |field|The name of the field used to perform the aggregation operation. Only the LONG, DOUBLE, BOOLEAN, KEYWORD, and GEOPOINT data types are supported.|

-   Examples

    The following code provides an example on how to use aggregation to obtain the number of people aged 18 that have taken the exam and have scores.

    ```
    
    query = TermQuery('age', 18)
    agg = Count('score', name = 'count')
    
    search_response = client.search(table_name, index_name,
                                    SearchQuery(query, next_token = None, limit=2, aggs=[agg]),
                                    columns_to_get = ColumnsToGet(return_type = ColumnReturnType.ALL_FROM_INDEX))
    
    for agg_result in search_response.agg_results:
        print('{\n"name":"%s",\n"value":%s\n}\n' % (agg_result.name, str(agg_result.value)))
    ```


## Distinct count

The aggregation method used to return the number of distinct values for a field. This method is similar to the SQL COUNT\(distinct\) function.

**Note:** The number of distinct values is an approximate number.

-   When the number of rows is smaller than 10,000, the calculated result is an exact value.
-   When the number of rows is equal to or greater than 100 million, the error rate is 2%.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on its name.|
    |field|The name of the field used to perform the aggregation operation. Only the LONG, DOUBLE, BOOLEAN, KEYWORD, and GEOPOINT data types are supported.|
    |missing|The default value for the field used for the aggregation operation on a row when the field value is empty.

    -   If the Missing parameter is not specified, the row is ignored.
    -   If the Missing parameter is set, the value of this parameter is used as the field value of the row. |

-   Examples

    The following code provides an example on how to use aggregation to obtain the number of distinct names used by people aged 18:

    ```
    query = TermQuery('age', 18)
    agg = DistinctCount('name', name = 'distinct_name')
    
    search_response = client.search(table_name, index_name,
                                    SearchQuery(query, next_token = None, limit=2, aggs=[agg]),
                                    columns_to_get = ColumnsToGet(return_type = ColumnReturnType.ALL_FROM_INDEX))
    
    for agg_result in search_response.agg_results:
        print('{\n"name":"%s",\n"value":%s\n}\n' % (agg_result.name, str(agg_result.value)))
    ```


## Group by field value

The method used to group query results based on field values. The same values are grouped together. The value of each group and the number of corresponding values are returned.

**Note:** The calculated number may be slightly different from the actual number when the number of values in a group is too large.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |field|The name of the field used to perform the aggregation operation. Only the LONG, DOUBLE, BOOLEAN, and KEYWORD data types are supported.|
    |size|The number of returned groups.|
    |group\_by\_sort|The sorting rules for items in a group. By default, group items are sorted in descending order. When you set multiple sorting rules, data is sorted based on the order in which the rules are added. Supported parameters:     -   Sort by value in ascending alphabetical order
    -   Sort by value in descending alphabetical order
    -   Sort by row count in ascending order
    -   Sort by row count in descending order
    -   Sort based on values obtained from the sub-aggregation in ascending order
    -   Sort based on values obtained from the sub-aggregation in descending order |
    |Ssub\_aggs and sub\_group\_bys|The sub-aggregation. The sub-aggregation operation is performed based on the grouping results.     -   Scenarios

Query the number of products in each category and the maximum and minimum product prices in each category.

    -   Method

First group query results by product category, and then add two sub-aggregations to obtain the maximum and minimum product prices in each category.

    -   Result
        -   Fruits: The number is 5. The maximum price is CNY 15. The minimum price is CNY 3.
        -   Toiletries: The number is 10. The maximum price is CNY 98. The minimum price is CNY 1.
        -   Electronic devices: The number is 3. The maximum price is CNY 8,699. The minimum price is CNY 2,300.
        -   Other products: The number is 15. The maximum price is CNY 1,000. The minimum price is CNY 80. |

-   Example 1

    The following code provides an example on how to group people aged 18 by score and obtain the top 10 common scores and the number of people in each group.

    ```
    query = TermQuery('age', 18)
    group_by = GroupByField('score', size = 10)
    
    search_response = client.search(table_name, index_name,
                                    SearchQuery(query, next_token = None, limit=20, group_bys = [group_by]),
                                    columns_to_get = ColumnsToGet(return_type = ColumnReturnType.ALL_FROM_INDEX))
    
    for group_by in search_response.group_by_results:
        print("name:%s" % group_by.name)
        print("groups:")
        for item in group_by.items:
            print("key:%s, count:%d" % (item.key, item.row_count))
    ```

-   Example 2

    The following code provides an example on how to group people aged 18 by score and obtain the two least common scores and the number of people with each score.

    ```
    group_by = GroupByField('score', size = 2, group_by_sort = [RowCountSort(sort_order=SortOrder.ASC)])
    search_response = client.search(table_name, index_name,
                                    SearchQuery(TermQuery('age', 18), limit=100, get_total_count=True, group_bys = [group_by]),
                                    ColumnsToGet(return_type=ColumnReturnType.ALL_FROM_INDEX))
    
    for group_by in search_response.group_by_results:
        print("name:%s" % group_by.name)
        print("groups:")
        for item in group_by.items:
            print("key:%s, count:%d" % (item.key, item.row_count))
    ```

-   Example 3

    The following code provides an example on how to group people aged 18 by score and obtain the top two common scores and the number of people in each group. Then, sort each group of people by primary key and obtain the information of the top three people in each group.

    ```
    sort = RowCountSort(sort_order = SortOrder.DESC)
    sub_agg = [TopRows(limit=3,sort=Sort([PrimaryKeySort(sort_order=SortOrder.DESC)]), name = 't1')]
    
    group_by = GroupByField('l', size = 2, group_by_sort = [sort], sub_aggs = sub_agg)
    search_response = client.search(table_name, index_name,
                                    SearchQuery(TermQuery('age', 18), limit=100, get_total_count=True, group_bys = [group_by]),
                                    ColumnsToGet(return_type=ColumnReturnType.ALL_FROM_INDEX))
    
    for group_by in search_response.group_by_results:
        print("name:%s" % group_by.name)
        print("groups:")
        for item in group_by.items:
            print("\tkey:%s, count:%d" % (item.key, item.row_count))
            for sub_agg in item.sub_aggs:
                print("\t\tname:%s:" % sub_agg.name)
                for entry in sub_agg.value:
                    print("\t\t\tvalue:%s" % str(entry))
    ```

-   Example 4

    The following code provides an example on how to group people aged 18 by score or gender:

    ```
    sort = RowCountSort(sort_order = SortOrder.ASC)
    sub_group = GroupByField('sex', size = 10, group_by_sort = [sort])
    
    group_by = GroupByField('score', size = 10, group_by_sort = [sort], sub_group_bys = [sub_group])
    search_response = client.search(table_name, index_name,
                                    SearchQuery(TermQuery('age', 18), limit=100, get_total_count=True, group_bys = [group_by]),
                                    ColumnsToGet(return_type=ColumnReturnType.ALL_FROM_INDEX))
    
    for group_by in search_response.group_by_results:
        print("name:%s" % group_by.name)
        print("groups:")
        for item in group_by.items:
            print("\tkey:%s, count:%d" % (item.key, item.row_count))
            for sub_group in item.sub_group_bys:
                print("\t\tname:%s:" % sub_group.name)
                for sub_item in sub_group.items:
                    print("\t\t\tkey:%s, count:%s" % (str(sub_item.key), str(sub_item.row_count)))
    ```


## Group by range

The method used to group query results based on value ranges of a field. Field values that fall within a specified range are grouped together. The number of values in each range is returned.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |field|The name of the field used to perform the aggregation operation. Only the LONG and DOUBLE data types are supported.|
    |range\[double\_from, double\_to\)|The value ranges for grouping. The value range can start from Double.MIN\_VALUE and end at Double.MAX\_VALUE. |
    |sub\_aggs and sub\_group\_bys|The sub-aggregation. The sub-aggregation operation is performed based on the grouping results. For example, after you group query results by sales volume and then by province, you can obtain the result of which province has the largest proportion in a specified range of sales volume. You must specify GroupByField in GroupByRange to run this query. |

-   Examples

    The following code provides an example on how to group people aged 18 by score range and obtain the numbers of people with scores in the \[80, 90\) and \[90, 100\) intervals:

    ```
    query = TermQuery('age', 18)
    group_by = GroupByRange(field_name = 'score', ranges = [(80, 90),(90, 100)])
    
    search_response = client.search(table_name, index_name,
                                    SearchQuery(query, next_token = None, limit=0, group_bys = [group_by]),
                                    columns_to_get = ColumnsToGet(return_type = ColumnReturnType.ALL_FROM_INDEX))
    
    for group_by in search_response.group_by_results:
        print("name:%s" % group_by.name)
        print("groups:")
        for item in group_by.items:
            print("range:%.1f~%.1f, count:%d" % (item.range_from, item.range_to, item.row_count))
    ```


## Group by geographical distance

The method used to group query results based on their geographical distances to a central point. Query results in distances that fall within a specified range are grouped together. The number of items in each range is returned.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |field|The name of the field used to perform the aggregation operation. Only the GEOPOINT data type is supported.|
    |origin\(double lat, double lon\)|The longitude and latitude of the central point. lat indicates the latitude of the central point. lon indicates the longitude of the central point. |
    |range\[double\_from, double\_to\)|The ranges for grouping. Unit: meter. The value range can start from Double.MIN\_VALUE and end at Double.MAX\_VALUE. |
    |sub\_aggs and sub\_group\_bys|The sub-aggregation. The sub-aggregation operation is performed based on the grouping results.|

-   Examples

    The following code provides an example on how to group people aged 18 by the geographical distance from their school to their home locations and obtain the numbers of people who live within one kilometer from the school and people who live one to two kilometers away from school: The latitude and longitude of the school is \(31,116\).

    ```
    query = TermQuery('age', 18)
    group_by = GroupByGeoDistance(field_name = 'address', origin=GeoPoint(31, 116), ranges = [(0, 1000), (1000,2000)])
    
    search_response = client.search(table_name, index_name,
                                    SearchQuery(query, next_token = None, limit=2, group_bys = [group_by]),
                                    columns_to_get = ColumnsToGet(return_type = ColumnReturnType.ALL_FROM_INDEX))
    
    for group_by in search_response.group_by_results:
        print("name:%s" % group_by.name)
        print("groups:")
        for item in group_by.items:
            print("range:%.1f~%.1f, count:%d" % (item.range_from, item.range_to, item.row_count))
    ```


## Group by filter

The method used to filter the query results and group them together to obtain the result of the number of items that match each filter. Results are returned in the order in which the filters are added.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |filter|The filters for the query. Results are returned in the order in which the filters are added.|
    |sub\_aggs and sub\_group\_bys|The sub-aggregation. The sub-aggregation operation is performed based on the grouping results.|

-   Examples

    The following code provides an example on how to group people aged 18 by filter and obtain the numbers of people who scored 100 in the math exam and who scored 100 in the Chinese exam.

    ```
    query = TermQuery('age', 18)
    filter1 = TermQuery('math', 100)
    filter2 = TermQuery('chinese', 100)
    filters = [filter1, filter2]
    group_by = GroupByFilter(filters)
    
    search_response = client.search(
        table_name, index_name,
        SearchQuery(query, next_token = None, limit=2, group_bys = [group_by]),
        columns_to_get = ColumnsToGet(return_type = ColumnReturnType.ALL_FROM_INDEX))
    
    for group_by in search_response.group_by_results:
        print("name:%s" % group_by.name)
        print("groups:")
        i = 0
        for item in group_by.items:
            print("filter:%s=%s, count:%d" % (str(filters[i].field_name), str(filters[i].column_value), item.row_count))
            i=i+1
    ```


