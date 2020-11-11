# Collapse \(distinct\)

You can use the collapse feature to collapse the result set based on a specified column when the results of a query contain large amounts of data of a specific type. Data of the corresponding type is displayed only once in the query results to ensure diversity in the result types.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Create search indexes.md).

## Usage notes

-   If you use the collapse feature, you must implement pagination by specifying offset and limit instead of Token.
-   If you aggregate and collapse a result set at the same time, the result set is aggregated before it is collapsed.
-   If you collapse the query results, the total number of groups of the returned results is determined by the sum of the offset and size values. A maximum of 10,000 groups can be returned.
-   The total number of rows returned in the results indicates the number of returned rows before you use the collapse feature. After the result set is collapsed, you cannot query the total number of the returned groups.

## Parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|Query|The query type.|
|Collapse|The collapse parameter, including FieldName.FieldName: the name of the column based on which the result set is collapsed. Only columns whose values are of the INTEGER, FLOATING-POINT and KEYWORD data types are supported. |
|Offset|The position from which the current query starts.|
|Limit|The maximum number of rows that the current query returns.To query only the number of matched rows without the data, you can set Limit to 0. In this case, Tablestore returns the number of matched rows without table data. |

## Examples

```
func QueryWithCollapse(client *tablestore.TableStoreClient, tableName string, indexName string) {
    searchRequest := &tablestore.SearchRequest{}
    searchRequest.SetTableName(tableName)       // Specify the name of the table.
    searchRequest.SetIndexName(indexName)       // Specify the name of the search index.

    searchQuery := search.NewSearchQuery()
    searchQuery.SetQuery(&search.MatchQuery{    // Specify the query conditions.
        FieldName:          "user_id",
        Text:               "00002",
    })
    searchQuery.SetCollapse(&search.Collapse{
        FieldName: "product_name",              // Collapse the result set based on the product_name value.

    })
    searchQuery.SetOffset(0)
    searchQuery.SetLimit(100)
    searchRequest.SetColumnsToGet(&tablestore.ColumnsToGet{ReturnAll:true}) // Specify to return all columns.
    searchRequest.SetSearchQuery(searchQuery)

    searchResponse, err := client.Search(searchRequest)   // Start the query.
    if err != nil {
        fmt.Println("Failed to search with error:", err)
        return
    }
    for _, row := range searchResponse.Rows {           // Display the rows returned.
        jsonBody, err := json.Marshal(row)
        if err != nil {
            panic(err)
        }
        fmt.Println("Row: ", string(jsonBody))
    }
}
```

