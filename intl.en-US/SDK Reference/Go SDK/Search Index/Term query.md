# Term query

You can use term query to query data that exactly matches the specified value of a field. Term query is similar to queries based on string match conditions. If the type of a field is TEXT, Tablestore tokenizes the string and exactly matches tokens.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Go SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|Query|The query type. To use term query, set this parameter to TermQuery.|
|FieldName|The name of the field used to match the query conditions.|
|Term|The keyword used for the query to match the column values.The keyword is not tokenized but used as an exact word to match the column values.

If the field used to match the query conditions is a TEXT field, the field values are tokenized. A row meets the query conditions when the tokenized value of the specified field contains at least one term that can match the keyword. For example, if the value of a TEXT field in a row is "tablestore is cool", the value can be tokenized into "tablestore", "is", and "cool". The row can meet query conditions when you set "tablestore", "is", or "cool" to the keyword to match the values of the TEXT field. |
|ColumnsToGet|Specifies whether to return all columns of each matched row. You can configure ReturnAll and Columns for this parameter.By default, ReturnAll is set to false, which indicates that not all columns are returned. If ReturnAll is set to false, you can configure Columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If ReturnAll is set to true, all columns are returned. |
|GetTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned.Query performance is affected when this parameter is set to true. |

## Examples

```
/**
 * Search the table for rows where the value of Col_Keyword exactly matches "hangzhou".
 */
func TermQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
    searchRequest := &tablestore.SearchRequest{}
    searchRequest.SetTableName(tableName)
    searchRequest.SetIndexName(indexName)
    query := &search.TermQuery{} // Set the query type to TermQuery.
    query.FieldName = "Col_Keyword" // Set the field used to match the query conditions.
    query.Text = "hangzhou" // Set the keyword used for the query to match the column values.
    searchQuery := search.NewSearchQuery()
    searchQuery.SetQuery(query)
    searchQuery.SetGetTotalCount(true)
    searchRequest.SetSearchQuery(searchQuery)
    // Set ReturnAll to true to return all columns.
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

