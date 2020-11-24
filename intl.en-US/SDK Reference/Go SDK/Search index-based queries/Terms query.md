# Terms query

This query is similar to term query. However, terms query supports multiple terms. This query is also similar to the SQL IN operator. A row of data is returned if one of the keywords matches the field value. Terms query is equivalent to the IN operator in SQL statements.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|Query|The query type. To use terms query, set this parameter to TermsQuery.|
|FieldName|The name of the field used to match the query conditions.|
|Terms|The keywords used in the query to match the column values.A row meets the query conditions when the value of the specified column exactly matches at least one keyword. |
|Limit|The maximum number of rows that the current query returns.To query only the number of matched rows but not detailed data, you can set Limit to 0. In this case, Tablestore returns the number of matched rows without data from the table. |
|ColumnsToGet|Specifies whether to return all columns of each matched row. You can configure ReturnAll and Columns for this parameter.By default, ReturnAll is set to false, which indicates that not all columns are returned. If ReturnAll is set to false, you can configure Columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If ReturnAll is set to true, all columns are returned. |

## Examples

```
/**
 * Search the table for rows where the value of Col_Keyword exactly matches "hangzhou" or "tablestore".
 */
func TermsQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
    searchRequest := &tablestore.SearchRequest{}
    searchRequest.SetTableName(tableName)
    searchRequest.SetIndexName(indexName)
    query := &search.TermsQuery{}   // Set the query type to TermsQuery.
    query.FieldName = "Col_Keyword" // Set the field used to match the query conditions.
    terms := make([]interface{}, 0)
    terms = append(terms, "hangzhou")
    terms = append(terms, "tablestore")
    query.Terms = terms // Set the keywords used in the query to match the column values.
    searchQuery := search.NewSearchQuery()
    searchQuery.SetQuery(query)
    searchQuery.SetLimit(100)
    searchRequest.SetSearchQuery(searchQuery)
    // Set ReturnAll to true to return all columns.
    searchRequest.SetColumnsToGet(&tablestore.ColumnsToGet{
        ReturnAll: true,
    })
    searchResponse, err := client.Search(searchRequest)
    if err != nil {
        fmt.Printf("%#v", err)
        return
    }
    fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
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

