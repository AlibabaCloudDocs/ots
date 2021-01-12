# Prefix query

You can use prefix query to query data that matches a specified prefix. If the type of a field is TEXT, Tablestore tokenizes the string and matches tokens by using the specified prefix.

## Operations

You can call the Search or ParallelScan operation and set the query type to PrefixQuery to implement prefix query.

## Use Tablestore SDKs

You can use the following Tablestore SDKs to implement prefix query:

-   Tablestore SDK for Java: [Prefix query](/intl.en-US/SDK Reference/Java SDK/Search Index/Prefix query.md)
-   Tablestore SDK for Go: [Prefix query](/intl.en-US/SDK Reference/Go SDK/Search Index/Prefix query.md)
-   Tablestore SDK for Python: [Prefix query](/intl.en-US/SDK Reference/Python SDK/Search Index/Prefix query.md)
-   Tablestore SDK for Node.js: [Prefix query](/intl.en-US/SDK Reference/Node.js SDK/Search Index/Prefix query.md)
-   Tablestore SDK for .NET: [Prefix query](/intl.en-US/SDK Reference/.NET SDK/Search Index/Prefix query.md)
-   Tablestore SDK for PHP: [Prefix query](/intl.en-US/SDK Reference/PHP SDK/Search index/Prefix query.md)

## Parameters

|Parameter|Description|
|---------|-----------|
|query|The type of the query. Set the query type to PrefixQuery.|
|fieldName|The name of the field that you want to match.|
|prefix|The prefix.If the field used to match the query conditions is a TEXT field, the field values are tokenized. A row meets the query conditions when the tokenized value of the specified field contains at least one term that contains the specified prefix. |
|getTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to true. |
|tableName|The name of the table.|
|indexName|The name of the search index.|
|columnsToGet|Specifies whether to return all columns of each matched row. You can configure returnAll and columns for this parameter. By default, returnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If returnAll is set to true, all columns are returned. |

## Examples

```
/**
 * Search the table for rows where the value of Col_Keyword contains the prefix that exactly matches "hangzhou".
 * @param client
 */
private static void prefixQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    PrefixQuery prefixQuery = new PrefixQuery(); // Set the query type to PrefixQuery.
    searchQuery.setGetTotalCount(true);
    prefixQuery.setFieldName("Col_Keyword");
    prefixQuery.setPrefix("hangzhou");
    searchQuery.setQuery(prefixQuery);
    //searchQuery.setGetTotalCount(true); // Specify that the total number of matched rows is returned.

    SearchRequest searchRequest = new SearchRequest("sampleTable", "sampleSearchIndex", searchQuery);
    // You can set the columnsToGet parameter to specify the columns to return or specify that all columns are returned. If you do not set this parameter, only the primary key columns are returned.
    //SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    //columnsToGet.setReturnAll(true); // Set ReturnAll to true to return all columns.
    //columnsToGet.setColumns(Arrays.asList("ColName1","ColName2")); // Set Columns to return specified columns.
    //searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    //System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
```

