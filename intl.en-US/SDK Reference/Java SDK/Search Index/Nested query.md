# Nested query

This topic describes how to use nested query to query the child row data of nested columns. You cannot directly query nested fields. You must specify the NestedQuery object. In NestedQuery, you must specify the path of a nested column and a subquery. The subquery can be of any type.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Java SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|path|The path of the nested column. The path is similar to the tree structure. For example, news.title indicates the title subcolumn in the news column of the nested type.|
|query|The query on the subcolumn in the nested column. The query can be of any query type.|
|scoreMode|Specifies which value is used to calculate the score when a column contains multiple values.|
|getTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to true. |
|tableName|The name of the table.|
|indexName|The name of the search index.|
|columnsToGet|Specifies whether to return all columns of each matched row. You can configure returnAll and columns for this parameter. By default, returnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If returnAll is set to true, all columns are returned. |

## Examples

The nested column contains nested\_1 and nested\_2. You need to search the col\_nested.nested\_1 column for data that matches "tablestore".

```
private static void nestedQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    NestedQuery nestedQuery = new NestedQuery(); // Set the query type to NestedQuery.
    nestedQuery.setPath("col_nested"); // Set the path of the nested field.
    TermQuery termQuery = new TermQuery(); // Specify a subquery for NestedQuery.
    termQuery.setFieldName("col_nested.nested_1"); // Set the name of the field that you want to match. The field name must contain the prefix of the nested column.
    termQuery.setTerm(ColumnValue.fromString("tablestore")); // Set the value that you want to match.
    nestedQuery.setQuery(termQuery);
    nestedQuery.setScoreMode(ScoreMode.None);
    searchQuery.setQuery(nestedQuery);
    //searchQuery.setGetTotalCount(true);// Specify that the total number of matched rows is returned.

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

