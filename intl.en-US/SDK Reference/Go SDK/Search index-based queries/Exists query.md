# Exists query

This query is also called a NULL query or NULL-value query. This query is used for sparse data to determine whether a column in a row exists. For example, you can query the rows in which the value of the address column is not empty.

**Note:**

-   If you want to query whether a column is empty, you must use ExistsQuery in combination with MustNotQueries in BoolQuery.
-   Assume that a column does not exist. The city column is used in the example.
    -   The city column does not exist in the row.
    -   The city column is an empty array. \("city" = "\[\]"\).

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|Query|Sets the query type to ExistsQuery.|
|FieldName|The name of the column.|

## Examples

The following code provides an example on how to query the rows where the city column contains values:

```
func ExitsQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
    searchRequest := &tablestore.SearchRequest{}
    searchRequest.SetTableName(tableName)
    searchRequest.SetIndexName(indexName)
    query := &search.TermQuery{} // Set the query type to ExitsQuery.
    query.FieldName = "city" // Set the column name.
    searchQuery := search.NewSearchQuery()
    searchQuery.SetQuery(query)
    searchQuery.SetGetTotalCount(true)
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

