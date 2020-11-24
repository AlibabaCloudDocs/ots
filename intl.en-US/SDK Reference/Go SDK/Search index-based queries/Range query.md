# Range query

You can use RangeQuery to query data that falls within a specified range. When a table contains a TEXT string, Tablestore tokenizes the string and matches tokens by using the specified prefix.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|Query|The query type. To use range query, set this parameter to RangeQuery.|
|FieldName|The name of the field used to match the query conditions.|
|From|The value from which the query starts.You can use GT or GTE to specify a range. GT indicates that the values within the range are greater than the specified value. GTE indicates that the values within the range are greater than or equal to the specified value. |
|To|The value with which the query ends.You can use LT or LTE to specify a range. LT indicates that the values within the range are smaller than the specified value. LTE indicates that the values within the range are smaller than or equal to the specified value. |
|IncludeLower|Specifies whether to include the value of the From parameter in the response. The value type is Boolean.|
|IncludeUpper|Specifies whether to include the value of the To parameter in the response. The value type is Boolean.|
|Sort|The sorting method. The returned rows are sorted based on the specified method. For more information, see [Sorting and paging](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Sorting and paging.md).|
|ColumnsToGet|Specifies whether to return all columns of each matched row. You can configure ReturnAll and Columns for this parameter.By default, ReturnAll is set to false, which indicates that not all columns are returned. If ReturnAll is set to false, you can configure Columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If ReturnAll is set to true, all columns are returned. |

## Examples

```
/**
 * Search the table for rows where the value in Col_Long is greater than 3. Tablestore sorts the matched rows based on the values of Col_Long in descending order.
 */
func RangeQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
    searchRequest := &tablestore.SearchRequest{}
    searchRequest.SetTableName(tableName)
    searchRequest.SetIndexName(indexName)
    searchQuery := search.NewSearchQuery()
    rangeQuery := &search.RangeQuery{} // Set the query type to RangeQuery.
    rangeQuery.FieldName = "Col_Long" // Set the field used to match the query conditions.
    rangeQuery.GT(3) // Specify the range of the field value, which is greater than 3.
    searchQuery.SetQuery(rangeQuery)
    // Sort the query results based on the values of Col_Long in descending order.
    searchQuery.SetSort(&search.Sort{
        []search.Sorter{
            &search.FieldSort{
                FieldName: "Col_Long",
                Order:     search.SortOrder_DESC.Enum(),
            },
        },
    })
    searchRequest.SetSearchQuery(searchQuery)
    searchRequest.SetColumnsToGet(&tablestore.ColumnsToGet{
        ReturnAll:true,
    })
    searchResponse, err := client.Search(searchRequest)
    if err ! = nil {
        fmt.Printf("%#v", err)
        return
    }
    fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
    fmt.Println("RowCount: ", len(searchResponse.Rows))
    for _, row := range searchResponse.Rows {
        jsonBody, err := json.Marshal(row)
        if err ! = nil {
            panic(err)
        }
        fmt.Println("Row: ", string(jsonBody))
    }
}
```

