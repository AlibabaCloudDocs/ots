# Sorting and pagination

You can specify IndexSort when you create a search index and specify a sorting method when you query data. You can use limit and offset or use tokens for pagination.

## Index sorting

The matched data is sorted based on the IndexSort field value when search indexes are used in a query. Search indexes of the NEST type do not support IndexSort. Tablestore returns the query result based on the default IndexSort field value, which is the order of primary key columns. You can specify the IndexSort field when you create a search index. If you do not specify the IndexSort field, Tablestore returns the query result based on the order of primary key columns.

**Note:** Search indexes of the NEST type do not support IndexSort.

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
                    order: TableStore.SortOrder.SORT_ORDER_DESC // Descending order.
                    //order: TableStore.SortOrder.SORT_ORDER_ASC // Ascending order.
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
                    points: ["0,0"],// Set the center point.
                    order: TableStore.SortOrder.SORT_ORDER_ASC // The query result is returned in ascending order of the distances between geographical locations and the center point.
                }
            }
        ]
    }
    ```


For code details, see [Search](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/search.js).

## Specify a pagination method

You can use limit and offset or use tokens to split returned query results into pages.

-   Use the limit and offset values

    When the total number of rows to obtain is smaller than 10,000, you can specify the limit and offset parameters for pagination. The sum of the limit and offset parameter values cannot exceed 10,000.

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
            getTotalCount: true // If TotalCount is set to true, the total number of rows that meet the filter condition is returned. If TotalCount is set to false, the total number of rows that meet the filter condition is not returned.
        },
        columnToGet: { // Set RETURN_SPECIFIED to return a specified number of columns, RETURN_ALL to return all columns, or RETURN_NONE to return no data.
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

    If Tablestore is unable to complete reading data that meets the filter condition, the server returns nextToken. You can use nextToken to continue reading the subsequent data.

    When you use the token, the sorting method is the same as that used in the previous request. The system sorts data based on the IndexSort field by default or based on the method that you have specified. Therefore, you cannot set the sorting method if you use a token. You cannot set offset when a token is used. Data is returned page by page, which results in a slow query.

    ```
    /**
     * The following code provides an example on how to use tokens to paginate results in synchronous and asynchronous modes:
     */
    var params = {
        tableName: TABLE_NAME,
        indexName: INDEX_NAME,
        searchQuery: {
            offset: 0,
            limit: 10,
            token: null,// Obtain a next token as the start point to scan the next page. The data type is byte stream.
            query: {
                queryType: TableStore.QueryType.MATCH_ALL_QUERY
            },
            getTotalCount: true
        },
        columnToGet: {
            returnType: TableStore.ColumnReturnType.RETURN_NONE,
            returnNames: ["pic_tag", "pic_description", "time_stemp", "pos"]
        }
    };
    
    /**
     * The following code provides an example on how to use tokens to paginate results in synchronous mode:
     */
    (async () => { // Code in synchronous mode.
      try {
        var data = await client.search(params);
        console.log(data);
    
        while (data.nextToken) { // If a next token is returned, not all data is returned.
          params.searchQuery.token = data.nextToken;// Update the token value for more pages to be returned.
          data = await client.search(params);
          console.log(data);
        }
      } catch (error) {
          console.log(error);
      }
    })()
    
    /**
     * The following code provides an example on how to use tokens to paginate results in asynchronous mode:
     */
    client.search(params, function (err, data) { // Code in asynchronous mode. Return only one page.
        console.log('success:', JSON.stringify(data, null, 2));
    
        if (data.nextToken) {
            params.searchQuery.token = data.nextToken;// Update the token value for more pages to be returned.
            client.search(params, function (err, data) {
                console.log('token success:', JSON.stringify(data, null, 2));
            });
        }
    });
    
    
    
    /**
     * Implement persistent storage for tokens.
     * 1) Convert a token (nextToken) as binary data in the Buffer object to a Base64 string and implement persistent storage for the string.
     * 2) Convert the persisted Base64 string to binary data by creating a Buffer object to use nextToken as a parameter.
     */
    var stringToken = data.nextToken.toString("base64", data.nextToken.offset, data.nextToken.limit);
    var bufferToken = new Buffer(stringToken, "base64");
    ```


