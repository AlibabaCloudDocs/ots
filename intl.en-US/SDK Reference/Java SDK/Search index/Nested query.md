# Nested query

This topic describes how to use NestedQuery to query the data of nested columns. You cannot directly query NESTED fields. You must specify the NestedQuery object. In NestedQuery, you must specify the path of a NESTED column and a subquery. The subquery can be of any type.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialize SDK for Java.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Java SDK/Search index/Create search indexes.md).

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

