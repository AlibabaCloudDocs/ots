# Nested query

This topic describes how to use NestedQuery to query the data of nested columns. You cannot directly query NESTED fields. You must specify the NestedQuery object. In NestedQuery, you must specify the path of a NESTED column and a subquery. The subquery can be of any type.

**Note:**

-   Only NESTED columns can be queried when you use nested query.
-   However, a query request can include nested queries and queries of other types. For more information about the NESTED type, see [Nested type](/intl.en-US/Function Introduction/Search Index/API operations/Array and Nested field types.md).

## Operations

You can call the Search or ParallelScan operation and set the query type to NestedQuery to implement nested query.

## Use Tablestore SDKs

You can use the following Tablestore SDKs to implement nested query:

-   Tablestore SDK for Java: [Nested query](/intl.en-US/SDK Reference/Java SDK/Search index/NestedQuery.md)
-   Tablestore SDK for Go: [Nested query](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Query by nested field.md)
-   Tablestore SDK for Python: [Nested query](/intl.en-US/SDK Reference/Python SDK/Search index-based queries/Query by nested field.md)
-   Tablestore SDK for Node.js: [Nested query](/intl.en-US/SDK Reference/NodeJS SDK/Search index-based queries/Query by nested field.md)
-   Tablestore SDK for .NET: [Nested query](/intl.en-US/SDK Reference/.NET SDK/Search index operations/NestedQuery.md)
-   Tablestore SDK for PHP: [Nested query](/intl.en-US/SDK Reference/PHP SDK/Search index/Query by nested field.md)

## Parameters

|Parameter|Description|
|---------|-----------|
|path|The path of the NESTED column. The path is similar to the tree structure. For example, news.title indicates the title subcolumn in the news column of the NESTED type.|
|query|The query on the subcolumn in the NESTED column. The query can be of any query type.|
|scoreMode|Specifies which value is used to calculate the score when a column contains multiple values.|
|tableName|The name of the table.|
|indexName|The name of the search index.|

## Examples

The NESTED column contains nested\_1 and nested\_2. You need to search the col\_nested.nested\_1 column for data that matches "tablestore".

```
private static void nestedQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    NestedQuery nestedQuery = new NestedQuery(); // Set the query type to NestedQuery.
    nestedQuery.setPath("col_nested"); // Set the path of the NESTED field.
    TermQuery termQuery = new TermQuery(); // Specify a subquery for NestedQuery.
    termQuery.setFieldName("col_nested.nested_1"); // Set the name of the field that you want to match. The field name must contain the prefix of the NESTED column.
    termQuery.setTerm(ColumnValue.fromString("tablestore")); // Set the value that you want to match.
    nestedQuery.setQuery(termQuery);
    nestedQuery.setScoreMode(ScoreMode.None);
    searchQuery.setQuery(nestedQuery);
    searchQuery.setGetTotalCount(true);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set ReturnAll to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

