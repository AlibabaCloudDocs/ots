# Wildcard query

You can use asterisks \(\*\) and question marks \(?\) to search for data that contains wildcards. An asterisk \(\*\) represents any number of characters. A question mark \(?\) represents a single character. The matched value can start with an asterisk \(\*\) or a question mark \(?\). For example, if you search for string "table\*e", you can retrieve query results such as "tablestore".

## Limits

A string that contains wildcards cannot exceed 20 characters in length.

## Operations

You can call the Search or ParallelScan operation and set the query type to WildcardQuery to implement wildcard query.

## Use Tablestore SDKs

You can use the following Tablestore SDKs to implement wildcard query:

-   Tablestore SDK for Java: [Wildcard query](/intl.en-US/SDK Reference/Java SDK/Search Index/Wildcard query.md)
-   Tablestore SDK for Go: [Wildcard query](/intl.en-US/SDK Reference/Go SDK/Search Index/Wildcard query.md)
-   Tablestore SDK for Python: [Wildcard query](/intl.en-US/SDK Reference/Python SDK/Search Index/Wildcard query.md)
-   Tablestore SDK for Node.js: [Wildcard query](/intl.en-US/SDK Reference/Node.js SDK/Search Index/Wildcard query.md)
-   Tablestore SDK for .NET: [Wildcard query](/intl.en-US/SDK Reference/.NET SDK/Search Index/Wildcard query.md)
-   Tablestore SDK for PHP: [Wildcard query](/intl.en-US/SDK Reference/PHP SDK/Search index/Wildcard query.md)

## Parameters

|Parameter|Description|
|---------|-----------|
|fieldName|The name of the column.|
|value|The string that contains wildcards. The string cannot exceed 20 characters in length.|
|query|The type of the query. Set the query type to WildcardQuery.|
|getTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to true. |
|tableName|The name of the table.|
|indexName|The name of the search index.|
|columnsToGet|Specifies whether to return all columns of each matched row. You can configure returnAll and columns for this parameter. By default, returnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

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
}
```

