# Terms query

This query is similar to term query. However, terms query supports multiple terms. This query is also similar to the SQL IN operator. A row of data is returned if one of the keywords matches the field value. Terms query is equivalent to the IN operator in SQL statements.

## Operations

You can call the Search or ParallelScan operation and set the query type to TermsQuery to implement terms query.

## Use Tablestore SDKs

You can use the following Tablestore SDKs to implement terms query:

-   Tablestore SDK for Java: [Terms query](/intl.en-US/SDK Reference/Java SDK/Search index/Terms query.md)
-   Tablestore SDK for Go: [Terms query](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Terms query.md)
-   Tablestore SDK for Python: [Terms query](/intl.en-US/SDK Reference/Python SDK/Search index-based queries/Terms query.md)
-   Tablestore SDK for Node.js: [Terms query](/intl.en-US/SDK Reference/NodeJS SDK/Search index-based queries/Terms query.md)
-   Tablestore SDK for PHP: [Terms query](/intl.en-US/SDK Reference/PHP SDK/Search index/Terms query.md)

## Parameters

|Parameter|Description|
|---------|-----------|
|query|The type of the query. Set the query type to TermsQuery.|
|fieldName|The name of the field you want to match.|
|terms|The keywords used to match the column values when you perform a terms query.A row of data is returned if one of the keywords matches the field value. |
|tableName|The name of the table.|
|indexName|The name of the search index.|
|columnsToGet|Specifies whether to return all columns of each matched row. You can configure returnAll and columns for this parameter. By default, returnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use columns to specify the columns you want to return. If you do not specify the columns you want to return, only the primary key columns are returned.

If returnAll is set to true, all columns are returned. |

## Examples

```
/**
 * Search the table for rows where the value of Col_Keyword exactly matches "hangzhou" or "xi'an".
 * @param client
 */
private static void termQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    TermsQuery termsQuery = new TermsQuery(); // Set the query type to TermsQuery.
    termsQuery.setFieldName("Col_Keyword"); // Set the name of the field that you want to match.
    termsQuery.addTerm(ColumnValue.fromString("hangzhou")); // Set the value that you want to match.
    termsQuery.addTerm(ColumnValue.fromString("xi'an")); // Set the value that you want to match.
    searchQuery.setQuery(termsQuery);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set ReturnAll to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

