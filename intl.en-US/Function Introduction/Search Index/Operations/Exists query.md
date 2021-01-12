# Exists query

This query is also called a NULL query or NULL-value query. This query is used for sparse data to determine whether a column in a row exists. For example, you can query the rows in which the value of the address column is not empty.

**Note:**

-   If you want to query whether a column contains empty values, you must use ExistsQuery in combination with MustNotQueries in BoolQuery.
-   Assume that a column does not exist. The city column is used in the example.
    -   The city column does not exist in the row.
    -   The city column is an empty array \("city" = "\[\]"\).

## API operations

You can call the Search or ParallelScan operation and set the query type to ExistsQuery to perform exists query.

## Use Tablestore SDKs

You can use the following Tablestore SDKs to implement exists query:

-   Tablestore SDK for Java: [Exists query](/intl.en-US/SDK Reference/Java SDK/Search Index/Exists query.md)
-   Tablestore SDK for Go: [Exists query](/intl.en-US/SDK Reference/Go SDK/Search Index/Exists query.md)
-   Tablestore SDK for PHP: [Exists query](/intl.en-US/SDK Reference/PHP SDK/Search index/Exists query.md)

## Parameters

|Parameter|Description|
|---------|-----------|
|fieldName|The name of the column.|
|query|The query type. Set the value to ExistsQuery.|
|getTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when the total number of rows that match the query conditions is returned. |
|tableName|The name of the table.|
|indexName|The name of the search index.|
|columnsToGet|Specifies whether to return all columns. By default, returnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If returnAll is set to true, all columns are returned. |

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
    //searchQuery.setGetTotalCount(true);//Set the total number of matched rows to return.

    SearchRequest searchRequest = new SearchRequest("sampleTable", "sampleSearchIndex", searchQuery);
    // You can set the columnsToGet parameter to specify the columns to return or specify to return all columns. If you do not set this parameter, only the primary key columns are returned.
    //SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    //columnsToGet.setReturnAll(true); // Set returnAll to true to return all columns.
    //columnsToGet.setColumns(Arrays.asList("ColName1","ColName2")); // Set columns to return specified columns.
    //searchRequest.setColumnsToGet(columnsToGet);
  
    SearchResponse resp = client.search(searchRequest);
    //System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows.
    System.out.println("Row: " + resp.getRows());
}
```

