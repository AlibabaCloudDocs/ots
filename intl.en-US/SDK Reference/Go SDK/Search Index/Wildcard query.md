# Wildcard query

You can use asterisks \(\*\) and question marks \(?\) as wildcards to search for data. An asterisk \(\*\) can represent any number of characters. A question mark \(?\) can represent any single character. The value to query can start with an asterisk \(\*\) or a question mark \(?\). For example, you can match query results such as "tablestore" by searching for "table\*e".

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialize a Tablestore client](/intl.en-US/SDK Reference/Go SDK/Initialize a Tablestore client.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Go SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|query|The query type. Set the query type to WildcardQuery.|
|FieldName|The column name.|
|Value|The string that contains wildcards. The string cannot exceed 20 characters in length.|
|ColumnsToGet|Specifies whether to return all columns. The default value of ReturnAll is false, which indicates that not all columns are returned. You can specify the columns to be returned by using ColumnsToGet. If you do not use ColumnsToGet to specify the columns to be returned , only the primary key columns are returned.

If ReturnAll is set to true, all columns are returned. |

## Examples

```
/**
 * Search the table for rows where the value of Col_Keyword matches "hang*u".
 */
func WildcardQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
    searchRequest := &tablestore.SearchRequest{}
    searchRequest.SetTableName(tableName)
    searchRequest.SetIndexName(indexName)
    query := &search.WildcardQuery{} // Set the query type to WildcardQuery.
    query.FieldName = "Col_Keyword"
    query.Value = "hang*u"
    searchQuery := search.NewSearchQuery()
    searchQuery.SetQuery(query)
    searchRequest.SetSearchQuery(searchQuery)
    // Return all columns.
    searchRequest.SetColumnsToGet(&tablestore.ColumnsToGet{
        ReturnAll:true,
    })
    searchResponse, err := client.Search(searchRequest)
    if err != nil {
        fmt.Printf("%#v", err)
        return
    }
    fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
    fmt.Println("TotalCount: ", searchResponse.TotalCount) // Display the total number of matched rows.
    fmt.Println("RowCount: ", len(searchResponse.Rows))
    for _, row := range searchResponse.Rows {
        jsonBody, err := json.Marshal(row)
        if err != nil {
            panic(err)
        }
        fmt.Println("Row: ", string(jsonBody))
    }
}
```

