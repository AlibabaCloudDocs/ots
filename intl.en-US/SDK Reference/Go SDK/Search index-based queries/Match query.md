# Match query

You can use match query to query a table based on approximate matches. Tablestore tokenizes the values in TEXT columns and the keywords you use to perform match queries based on the analyzer you set. Therefore, Tablestore can perform match queries based on the tokenized text.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|Query|The query type. To use match query, set this parameter to MatchQuery.|
|FieldName|The name of the column used to match the query conditions.Match query applies to TEXT columns. |
|Text|The keyword used for the query to match the column values.If the column used for the match query is a TEXT column, the keyword is tokenized into multiple keywords based on the analyzer you set when you create the search index. By default, single-word tokenization is performed if you do not set the analyzer when you create the search index.

For example, you can specify a TEXT column for a query, and set the analyzer to perform single-word tokenization. If you use the phrase "this is" as the keyword for a query, you can obtain query results such as "..., this is tablestore", "is this tablestore", "tablestore is cool", "this", and "is". |
|Operator|The logical operator. By default, OR is used as the logical operator, which indicates that a row matches the query conditions when one of the tokenized keywords is matched.If you set operator to AND, a row meets the query condition only when all tokenized keywords are matched. |
|MinimumShouldMatch|The minimum number of matched tokenized keywords contained in a column value.A row is returned only when the value of the queried column in the row contains at least the minimum number of matched tokenized keywords.

**Note:** MinimumShouldMatch must be used together with the OR logical operator. |
|Offset|The position from which the current query starts.|
|Limit|The maximum number of rows that the current query returns.To query only the number of matched rows but not detailed data, you can set Limit to 0. In this case, Tablestore returns the number of matched rows without data from the table. |
|GetTotalCount|Specify whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned.Query performance is affected when this parameter is set to true. |
|ColumnsToGet|Specifies whether to return all columns of each matched rows. You can configure ReturnAll and Columns for this parameter.By default, ReturnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use Columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If ReturnAll is set to true, all columns are returned. |

## Examples

```
/**
 * Search the table for rows where the value of Col_Keyword matches "hangzhou". Tablestore returns the number of rows that match the phrase and parts of the matched rows in this query.
 */
func MatchQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
    searchRequest := &tablestore.SearchRequest{}
    searchRequest.SetTableName(tableName)
    searchRequest.SetIndexName(indexName)
    query := &search.MatchQuery{} // Set the query type to MatchQuery.
    settingquery.FieldName = "Col_Keyword" // Set the column used to match the query conditions.
    query.Text = "hangzhou" // Set the keyword used for the query to match the column values.
    searchQuery := search.NewSearchQuery()
    searchQuery.SetQuery(query)
    searchQuery.SetGetTotalCount(true) 
    searchQuery.SetOffset(0) // Set Offset to 0.
    searchQuery.SetLimit(20) // Set Limit to 20 to return up to 20 rows.
    searchRequest.SetSearchQuery(searchQuery)
    searchResponse, err := client.Search(searchRequest)
    if err != nil { // Check for exceptions.
        fmt.Printf("%#v", err)
        return
    }
    fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
    fmt.Println("TotalCount: ", searchResponse.TotalCount) // Check the total number of matched rows.
    fmt.Println("RowCount: ", len(searchResponse.Rows)) // Check the total number of returned rows.
    for _, row := range searchResponse.Rows {
        jsonBody, err := json.Marshal(row)
        if err != nil {
            panic(err)
        }
        fmt.Println("Row: ", string(jsonBody)) // If ColumnsToGet is not specified, only primary key columns that match the query conditions are returned.
    }
    // Set columnsToGet to true to return all columns.
    searchRequest.SetColumnsToGet(&tablestore.ColumnsToGet{
        ReturnAll:true,
    })
    searchResponse, err = client.Search(searchRequest)
    if err != nil {
        fmt.Printf("%#v", err)
        return
    }
    fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
    fmt.Println("TotalCount: ", searchResponse.TotalCount) // Check the total number of matched rows.
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

