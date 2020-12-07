# Wildcard query

You can use asterisks \(\*\) and question marks \(?\) to search for data that contains wildcards. An asterisk \(\*\) represents any number of characters. A question mark \(?\) represents a single character. The matched value can start with asterisks \(\*\) or question marks \(?\). For example, if you search for string "table\*e", you can retrieve query results such as "tablestore".

## Limits

A string that contains wildcards cannot exceed 10 bytes in length.

## Operations

You can call the Search or ParallelScan operation and set the query type to WildcardQuery to implement wildcard query.

## Use Tablestore SDKs

You can use the following Tablestore SDKs to implement wildcard query:

-   Tablestore SDK for Java: [Wildcard query](/intl.en-US/SDK Reference/Java SDK/Search index/Wildcard query.md)
-   Tablestore SDK for Go: [Wildcard query](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Wildcard query.md)
-   Tablestore SDK for Python: [Wildcard query](/intl.en-US/SDK Reference/Python SDK/Search index-based queries/Wildcard query.md)
-   Tablestore SDK for Node.js: [Wildcard query](/intl.en-US/SDK Reference/NodeJS SDK/Search index-based queries/Wildcard query.md)
-   Tablestore SDK for .NET: [Wildcard query](/intl.en-US/SDK Reference/.NET SDK/Search index operations/Wildcard query.md)
-   Tablestore SDK for PHP: [Wildcard query](/intl.en-US/SDK Reference/PHP SDK/Search index/Wildcard query.md)

## Parameters

|Parameter|Description|
|---------|-----------|
|fieldName|The name of the column.|
|value|The string that contains wildcards. The string cannot exceed 10 bytes in length.|
|query|The type of the query. Set the query type to WildcardQuery.|
|getTotalCount|Specifies whether to return the total number of matched rows. Query performance is affected when the total number of rows that match the query conditions is returned. |
|tableName|The name of the table.|
|indexName|The name of the search index.|
|columnsToGet|Specifies whether to return all columns. By default, returnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If returnAll is set to true, all columns are returned. |

## Examples

```
/**
 * Search the table for rows where the value of Col_Keyword matches "hang*u".
 * @param client
 */
private static void wildcardQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    WildcardQuery wildcardQuery = new WildcardQuery(); // Set the query type to WildcardQuery.
    wildcardQuery.setFieldName("Col_Keyword");
    wildcardQuery.setValue("hang*u"); // Specify a string that contains one or more wildcard characters in wildcardQuery.
    searchQuery.setQuery(wildcardQuery);
    //searchQuery.setGetTotalCount(true);//Set the total number of matched rows to return.

    SearchRequest searchRequest = new SearchRequest("sampleTable", "sampleSearchIndex", searchQuery);
    // You can set the columnsToGet parameter to specify the columns to return or specify to return all columns. If you do not set this parameter, only the primary key columns are returned.
    //SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    //columnsToGet.setReturnAll(true); // Set returnAll to true to return all columns.
    //columnsToGet.setColumns(Arrays.asList("ColName1","ColName2")); // Set columns to return specified columns.
    //searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    //System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

