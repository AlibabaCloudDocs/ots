# Term query

You can use term query to query data that exactly matches the specified value of a field. Term query is similar to queries based on string match conditions. When a table contains a TEXT string, Tablestore tokenizes the string and exactly matches tokens.

## Operations

You can call the Search or ParallelScan operation and set the query type to TermQuery to implement term query.

## Use Tablestore SDKs

You can use the following Tablestore SDKs to implement term query:

-   Tablestore SDK for Java: [Term query](/intl.en-US/SDK Reference/Java SDK/Search index/Term query.md)
-   Tablestore SDK for Go: [Term query](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Term query.md)
-   Tablestore SDK for Python: [Term query](/intl.en-US/SDK Reference/Python SDK/Search index-based queries/Term query.md)
-   Tablestore SDK for Node.js: [Term query](/intl.en-US/SDK Reference/NodeJS SDK/Search index-based queries/Term query.md)
-   Tablestore SDK for .NET: [Term query](/intl.en-US/SDK Reference/.NET SDK/Search index operations/Term query.md)
-   Tablestore SDK for PHP: [Term query](/intl.en-US/SDK Reference/PHP SDK/Search index/Term query.md)

## Parameters

|Parameter|Description|
|---------|-----------|
|query|The type of the query. Set the query type to TermQuery.|
|fieldName|The name of the field you want to match.|
|term|The keyword used to match the column values when you perform a term query.This word is not tokenized. Instead, the whole word is used to match the field values.

When a table contains a TEXT string, Tablestore tokenizes the string and exactly matches tokens. For example, TEXT string "tablestore is cool" is tokenized into "tablestore", "is", and "cool". When you specify one of these tokens as a search string, you can retrieve query results that contain "tablestore is cool". |
|tableName|The name of the table.|
|indexName|The name of the search index.|
|columnsToGet|Specifies whether to return all columns of each matched row. You can configure returnAll and columns for this parameter. By default, returnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use columns to specify the columns you want to return. If you do not specify the columns to return, only the primary key columns are returned.

If returnAll is set to true, all columns are returned. |

## Examples

```
/**
 * Search the table for rows where the value of Col_Keyword exactly matches "hangzhou".
 * @param client
 */
private static void termQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    TermQuery termQuery = new TermQuery(); // Set the query type to TermQuery.
    termQuery.setFieldName("Col_Keyword"); // Set the name of the field that you want to match.
    termQuery.setTerm(ColumnValue.fromString("hangzhou")); // Set the value that you want to match.
    searchQuery.setQuery(termQuery);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set ReturnAll to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
            
```

