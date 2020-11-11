# Wildcard query

You can use asterisks \(\*\) and question marks \(?\) as wildcards to search for data. An asterisk \(\*\) can represent any number of characters. A question mark \(?\) can represent any single character. The value to query can start with an asterisk \(\*\) or a question mark \(?\). For example, you can match query results such as "tablestore" by searching for "table\*e".

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialize SDK for Java](/intl.en-US/SDK Reference/Java SDK/Initialize SDK for Java.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Java SDK/Search index/CreateSearchIndex.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|FieldName|The name of the column.|
|Value|The string that contains wildcards. The string can be up to 10 bytes in length.|
|Query|The query type. Set the query type to WildcardQuery.|
|TABLE\_NAME|The name of the table.|
|INDEX\_NAME|The name of the search index.|
|ColumnsToGet|Specifies whether to return all columns. The default value of ReturnAll is false, which indicates that not all columns are returned. You can specify the columns to return by using ColumnsToGet. Otherwise, only the primary key columns are returned.

If ReturnAll is set to true, all columns are returned. |
|getTotalCount|Specifies whether to return the total number of matched rows. Query performance is affected when the total number of rows that match the query conditions is returned. |

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
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set ReturnAll to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);

    System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

