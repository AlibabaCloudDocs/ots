# Exists query

This query is also called a NULL query or NULL-value query. This query is used for sparse data to determine whether a column in a row exists. For example, you can query the rows in which the value of the address column is not empty.

**Note:**

-   If you want to query whether a column contains empty values, you must use the mustNotQueries clause of BoolQuery.
-   Assume that a column does not exist. The city column is used in the example.
    -   The city column does not exist in the row.
    -   The city column is an empty array \("city" = "\[\]"\).

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Java SDK/Search index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|fieldName|The name of the column.|
|query|The query type. Set the value to ExistsQuery.|
|tableName|The name of the table.|
|indexName|The name of the search index.|

## Examples

```
/**
 * Use ExistsQuery to query the rows where the address column is not empty.
 * @param client
 */
private static void existQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    ExistsQuery existQuery = new ExistsQuery(); // Set the query type to ExistsQuery.
    existQuery.setFieldName("address");
    searchQuery.setQuery(existQuery);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set ReturnAll to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);

    System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows.
    System.out.println("Row: " + resp.getRows());
}
```

