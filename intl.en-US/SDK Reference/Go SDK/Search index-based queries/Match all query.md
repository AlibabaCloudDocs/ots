# Match all query

You can use match all query to query the total number of rows in a table or obtain multiple rows of data.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|Query|The query type. To use match all query, set this parameter to MatchAllQuery.|
|Limit|The maximum number of rows that the current query returns.To query only the number of matched rows but not detailed data, you can set Limit to 0. In this case, Tablestore returns the number of matched rows without data from the table. |
|GetTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned.Query performance is affected when this parameter is set to True. |
|ColumnsToGet|Specifies whether to return all columns of each matched row. By default, ReturnAll is set to false, which indicates that not all columns are returned. If ReturnAll is set to false, you can configure Columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If ReturnAll is set to true, all columns are returned. |

## Examples

```
/**
 * Use match all query to query the total number of rows in a table.
 */
func MatchAllQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
    searchRequest := &tablestore.SearchRequest{}
    searchRequest.SetTableName(tableName)
    searchRequest.SetIndexName(indexName)
    query := &search.MatchAllQuery{} // Set the query type to MatchAllQuery.
    searchQuery := search.NewSearchQuery()
    searchQuery.SetQuery(query) 
    searchQuery.SetGetTotalCount(true) 
    searchQuery.SetLimit(0) // Set Limit to 0, which indicates that no data is returned.
    searchRequest.SetSearchQuery(searchQuery)
    searchResponse, err := client.Search(searchRequest)
    if err ! = nil { // Check for exceptions.
        fmt.Printf("%#v", err) 
        return
    }
    fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess)
    fmt.Println("TotalCount: ", searchResponse.TotalCount) // Display the total number of rows in the table.
}
```

