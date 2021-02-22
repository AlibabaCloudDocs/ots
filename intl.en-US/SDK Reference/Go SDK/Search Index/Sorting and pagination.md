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
        searchQuery := search.NewSearchQuery()
        searchQuery.SetSort(&search.Sort{
            []search.Sorter{
                &search.ScoreSort{
                    Order: search.SortOrder_DESC.Enum(), // Sort the query result in descending order of scores.
                },
            },
        })
    ```

-   PrimaryKeySort

    You can use PrimaryKeySort to sort the query result based on the order of primary key columns.

    ```
    searchQuery := search.NewSearchQuery()
        searchQuery.SetSort(&search.Sort{
            []search.Sorter{
                &search.PrimaryKeySort{
                    Order: search.SortOrder_ASC.Enum(),
                },
            },
        })
    ```

-   FieldSort

    You can use FieldSort to sort the query result based on a specified column.

    ```
        // Sort the query results based on the values of Col_Long in descending order.
        searchQuery.SetSort(&search.Sort{
            []search.Sorter{
                &search.FieldSort{
                    FieldName: "Col_Long",
                    Order:     search.SortOrder_DESC.Enum(),
                },
            },
        })
    ```

    You can sort the query result based on the value of multiple columns.

    ```
    searchQuery.SetSort(&search.Sort{
            []search.Sorter{
                &search.FieldSort{
                    FieldName: "col1",
                    Order:     search.SortOrder_ASC.Enum(),
                },
                &search.FieldSort{
                    FieldName: "col2",
                    Order:     search.SortOrder_DESC.Enum(),
                },
            },
        })
    ```

-   GeoDistanceSort

    You can use GeoDistanceSort to sort the query result by geographical location.

    ```
        searchQuery.SetSort(&search.Sort{
            []search.Sorter{
                &search.GeoDistanceSort{
                    FieldName: "location",   // Set the name of the geographical location field.
                    Points:    []string{"40,-70"}, // Set the coordinate pairs of a central point.
                },
            },
        })
    ```


## Specify a pagination method

You can use Limit and Offset or Token for pagination when you obtain returned results.

-   Use the Limit and Offset parameters

    When the total number of rows to obtain is smaller than 10,000, you can specify the Limit and Offset parameters for pagination. The sum of the Limit and Offset parameter values cannot exceed 10,000. The maximum value of Limit is 100.

    **Note:** To set Limit to a value greater than 100, see [How do I increase the limit on the rows of data returned by calling the Search operation to 1,000?](/intl.en-US/FAQ/General FAQ/How do I increase the limit on the rows of data returned by calling the Search operation
         to 1,000?.md).

    If you use Limit and Offset for pagination but do not configure the Limit and Offset values, the default values are used. The default value of Limit is 10. The default value of Offset is 0.

    ```
    searchQuery := search.NewSearchQuery()
    searchQuery.SetLimit(10)
    searchQuery.SetOffset(10) 
    ```

-   Use a token

    We recommend that you use a token for deep pagination because this method has no limits on the pagination depth.

    If Tablestore cannot complete reading data that meets the query condition, the server returns NextToken. You can use NextToken to continue reading the subsequent data.

    By default, you can only page backward when you use a token. However, you can cache and use the previous token to implement forward pagination because a token is valid during the query.

    When you use the token, the sorting method is the same as that used in the previous request. Tablestore sorts data based on the IndexSort field by default or based on the method that you have specified. Therefore, you cannot set the sorting method if you use a token. You cannot set Offset when a token is used. Data is returned page by page, which results in a slow query.

    **Note:** Search indexes of the NEST type do not support IndexSort. If you use a search index of the NEST type to query data and require pagination, you must specify the sorting method to return data in the query conditions. Otherwise, Tablestore does not return NextToken when all data that meets the query conditions is not completely read.

    ```
    /**
     * Use a token to read data by page.
     * If SearchResponse returns NextToken, you can use this token to initiate the next query.
     * All data that matches the query condition is returned if the NextToken field value is nil.
     */
    func QueryRowsWithToken(client *tablestore.TableStoreClient, tableName string, indexName string) {
        querys := []search.Query{
            &search.MatchAllQuery{},
            &search.TermQuery{
                FieldName: "Col_Keyword",
                Term:      "tablestore",
            },
        }
        for _, query := range querys {
            fmt.Printf("Test query: %#v\n", query)
            searchRequest := &tablestore.SearchRequest{}
            searchRequest.SetTableName(tableName)
            searchRequest.SetIndexName(indexName)
            searchQuery := search.NewSearchQuery()
            searchQuery.SetQuery(query)
            searchQuery.SetLimit(10)
            searchQuery.SetGetTotalCount(true)
            searchRequest.SetSearchQuery(searchQuery)
            searchResponse, err := client.Search(searchRequest)
            if err ! = nil {
                fmt.Printf("%#v", err)
                return
            }
            rows := searchResponse.Rows
            requestCount := 1
            for searchResponse.NextToken ! = nil {
                searchQuery.SetToken(searchResponse.NextToken)
                searchResponse, err = client.Search(searchRequest)
                if err ! = nil {
                    fmt.Printf("%#v", err)
                    return
                }
                requestCount++
                for _, r := range searchResponse.Rows {
                    rows = append(rows, r)
                }
            }
            fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess)
            fmt.Println("TotalCount: ", searchResponse.TotalCount)
            fmt.Println("RowsSize: ", len(rows))
            fmt.Println("RequestCount: ", requestCount)
        }
    }
    ```


