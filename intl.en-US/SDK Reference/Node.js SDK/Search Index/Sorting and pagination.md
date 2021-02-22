# Sorting and pagination

You can specify IndexSort when you create a search index and specify a sorting method when you query data. You can use limit and offset or tokens for pagination.

## Index sorting

By default, a search index is sorted based on the specified IndexSort field. When you use a search index to query data, IndexSort determines the default order in which the matched data is returned.

When you create a search index, you can customize the IndexSort value. By default, if you do not specify IndexSort, the primary key order is used.

**Notice:** Search indexes of the NEST type do not support IndexSort.

## Specify a sorting method

Sorting can be enabled only for fields for which enableSortAndAgg is set to true.

You can specify a sorting method for each query. Search index-based queries support the following sorting methods: You can also specify multiple sorting methods based on different priorities.

-   ScoreSort

    You can use ScoreSort to sort the query result based on the BM25-based keyword relevance score. ScoreSort is applicable to scenarios such as full-text search.

    **Note:** You must set ScoreSort to sort the query result by keyword relevance score. Otherwise, the query result is sorted based on the value of the IndexSort field.

    ```
    sort: {
        sorters: [
            {
                scoreSort: {
                    order: TableStore.SortOrder.SORT_ORDER_ASC
                }
            }
        ]
    }
    ```

-   PrimaryKeySort

    You can use PrimaryKeySort to sort the query result based on the order of primary key columns.

    ```
    sort: {
        sorters: [
            {
                primaryKeySort: {
                    order: TableStore.SortOrder.SORT_ORDER_DESC // Sort the query result in the descending order.
                    //order: TableStore.SortOrder.SORT_ORDER_ASC // Sort the query result in the ascending order.
                }
            }
        ]
    }
    ```

-   FieldSort

    You can use FieldSort to sort the query result based on a specified column.

    ```
    sort: {
        sorters: [
            {
                fieldSort: {
                    fieldName: "Col_Keyword",
                    order: TableStore.SortOrder.SORT_ORDER_DESC
                }
            }
        ]
    }
    ```

    You can sort the query result based on the value of multiple columns.

    ```
    sort: {
        sorters: [
            {
                fieldSort: {
                    fieldName: "Col_Keyword",
                    order: TableStore.SortOrder.SORT_ORDER_DESC
                }
            },
            {
                fieldSort: {
                    fieldName: "Col_Long",
                    order: TableStore.SortOrder.SORT_ORDER_DESC
                }
            }
        ]
    }
    ```

-   GeoDistanceSort

    You can use GeoDistanceSort to sort the query result by geographical location.

    ```
    sort: {
        sorters: [
            {
                geoDistanceSort: {
                    fieldName: "Col_Geo_Point",
                    points: ["0,0"],// Set the central point.
                    order: TableStore.SortOrder.SORT_ORDER_ASC // The query result is returned in ascending order of the distances between geographical locations and the central point.
                }
            }
        ]
    }
    ```


For the detailed sample code, see [Search](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/search.js).

## Specify a pagination method

You can use limit and offset or use tokens to split returned query results into pages.

-   Use the limit and offset parameters

    When the total number of rows to obtain is smaller than 10,000, you can specify the limit and offset parameters for pagination. The sum of the limit and offset parameter values cannot exceed 10,000. The maximum value of limit is 100.

    **Note:** To set limit to a value greater than 100, see [How do I increase the limit on the rows of data returned by calling the Search operation to 1,000?](/intl.en-US/FAQ/General FAQ/How do I increase the limit on the rows of data returned by calling the Search operation
         to 1,000?.md).

    If you use limit and offset for pagination but do not configure the limit and offset values, the default values are used. The default value of limit is 10. The default value of offset is 0.

    ```
    
    /**
     * Set offset to 90 and limit to 10 to scan data from line 90 to line 99.
     */
    client.search({
        tableName: TABLE_NAME,
        indexName: INDEX_NAME,
        searchQuery: {
            offset: 90,
            limit: 10, 
            query: {
                queryType: TableStore.QueryType.MATCH_ALL_QUERY
            }
            getTotalCount: true // If TotalCount is set to true, the total number of rows that meet the query condition is returned. If TotalCount is set to false, the total number of rows that meet the query condition is not returned.
        },
        columnToGet: { // Set RETURN_SPECIFIED to return the specified columns, RETURN_ALL to return all columns, or RETURN_NONE to return no data.
            returnType: TableStore.ColumnReturnType.RETURN_ALL
        }
    }, function (err, data) {
        if (err) {
            console.log('error:', err);
            return;
        }
        console.log('success:', JSON.stringify(data, null, 2));
    });
    ```

-   Use a token

    We recommend that you use a token for deep pagination because this method has no limits on the pagination depth.

    If Tablestore cannot complete reading all data that meets the query conditions, Tablestore returns nextToken. You can use nextToken to continue reading the subsequent data.

    By default, you can only page backward when you use a token. However, you can cache and use the previous token to implement forward pagination because a token is valid during the query.

    When you use the token, the sorting method is the same as that used in the previous request. Tablestore sorts data based on the IndexSort field by default or based on the method that you have specified. Therefore, you cannot set the sorting method if you use a token. You cannot set offset when a token is used. Data is returned page by page, which results in a slow query.

    **Note:** Search indexes of the NEST type do not support IndexSort. If you use a search index of the NEST type to query data and require pagination, you must specify the sorting method to return data in the query conditions. Otherwise, Tablestore does not return nextToken when only part of data that meets the query conditions is returned.

    ```
    /**
     * The following code provides an example on how to use tokens to paginate results in synchronous and asynchronous modes.
     */
    var params = {
        tableName: TABLE_NAME,
        indexName: INDEX_NAME,
        searchQuery: {
            offset: 0,
            limit: 10,
            token: null,// Obtain the nextToken as the start point to scan the next page. The data type is byte stream.
            query: {
                queryType: TableStore.QueryType.MATCH_ALL_QUERY
            },
            getTotalCount: true
        },
        columnToGet: {
            returnType: TableStore.ColumnReturnType.RETURN_SPECIFIED,
            returnNames: ["pic_tag", "pic_description", "time_stemp", "pos"]
        }
    };
    
    /**
     * The following code provides an example on how to use tokens to paginate results in synchronous mode.
     */
    (async () => { // Code in synchronous mode.
      try {
        var data = await client.search(params);
        console.log('success:', JSON.stringify(data, null, 2));
    
        while (data.nextToken && data.nextToken.length) { // If nextToken is returned, Tablestore continues to read data.
    
          // Store the tokens persistently.
          // Convert a token (nextToken) as binary data in the Buffer object to a Base64 string and store the string persistently.
          // You can convert the persisted Base64 string to binary data by creating a Buffer object to use nextToken as a parameter.
          var nextToken = data.nextToken.toString("base64");
          var token =  Buffer.from(nextToken, "base64");
    
          params.searchQuery.token = token;// Update the token value to return more results.
          data = await client.search(params);
          console.log('token success:', JSON.stringify(data, null, 2));
        }
      } catch (error) {
          console.log(error);
      }
    })()
    
    /**
     * The following code provides an example on how to use tokens to paginate results in asynchronous mode.
     */
    client.search(params, function (err, data) { 
        console.log('success:', JSON.stringify(data, null, 2));
    
        if (data.nextToken && data.nextToken.length) {
    
            // Store the tokens persistently.
            // Convert a token (nextToken) as binary data in the Buffer object to a Base64 string and store the string persistently.
            // You can convert the persisted Base64 string to binary data by creating a Buffer object to use nextToken as a parameter.
            var nextToken = data.nextToken.toString("base64");
            var token =  Buffer.from(nextToken, "base64");
    
            params.searchQuery.token = token;// Update the token value to return more results.
            client.search(params, function (err, data) {
                console.log('token success:', JSON.stringify(data, null, 2));
            });
        }
    });
    ```


