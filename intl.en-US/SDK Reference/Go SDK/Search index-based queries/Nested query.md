# Nested query

This topic describes how to use NestedQuery to query the data of nested columns. You cannot directly query NESTED fields. You must specify the NestedQuery object. In NestedQuery, you must specify the path of a NESTED column and a subquery. The subquery can be of any type.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|Path|The path of the NESTED column. The path is similar to the tree structure. For example, news.title indicates the title subcolumn in the news column of the NESTED type.|
|Query|The query on the subcolumn in the NESTED column. The query can be of any query type.|
|ScoreMode|The value on which a score is calculated when multiple values exist for a column.|

## Examples

The NESTED column contains nested\_1 and nested\_2. You must search the col\_nested.nested\_1 column for data that matches "tablestore".

```
func NestedQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
    searchRequest := &tablestore.SearchRequest{}
    searchRequest.SetTableName(tableName)
    searchRequest.SetIndexName(indexName)
    query := &search.NestedQuery{ // Set the query type to NestedQuery.
        Path: "col_nested", // Set the path of the NESTED field.
        Query: &search.TermQuery{ // Construct the subquery of NestedQuery.
            FieldName: "col_nested.nested_1", // Set the field that is prefixed with col_nested.
            Term:      "tablestore",          // Set the value you want to match.
        },
        ScoreMode: search.ScoreMode_Avg,
    }
    searchQuery := search.NewSearchQuery()
    searchQuery.SetQuery(query)
    searchRequest.SetSearchQuery(searchQuery)
    // Return all columns.
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

