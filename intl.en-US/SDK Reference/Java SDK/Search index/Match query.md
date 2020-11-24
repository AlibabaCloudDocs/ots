# Match query

You can use match query to query a table based on approximate matches. Tablestore tokenizes the values in TEXT columns and the keywords you use to perform match queries based on the analyzer you set. Therefore, Tablestore can perform match queries based on the tokenized text.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialize SDK for Java.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Java SDK/Search index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|fieldName|The name of the column you want to query.Match query applies to TEXT columns. |
|text|The keyword used to match the column values when you perform a match query.If the column to query is a TEXT column, the keyword is tokenized into multiple tokens based on the analyzer you set when you create the search index. By default, single-word tokenization is performed if you do not set the analyzer when you create the search index.

For example, if you set the analyzer type to single-word tokenization and use "this is" as a search string, you can obtain query results such as "..., this is tablestore", "is this tablestore", "tablestore is cool", "this", and "is". |
|query|The query type, which is set to matchQuery.|
|offset|The position from which the current query starts.|
|limit|The maximum number of rows that the current query returns.To query only the number of matched rows without returning specific data, you can set limit to 0. Then, Tablestore returns the number of matched rows without data from the table. |
|minimumShouldMatch|The minimum number of matched tokens contained in a column value.A row is returned only when the value of the queried column in the row contains at least the minimum number of matched tokens.

**Note:** minimumShouldMatch must be used together with the OR logical operator. |
|operator|The logical operator. By default, OR is used as the logical operator, which indicates that a row matches the query conditions when one of the tokens is matched.If you set the operator to AND, the row data meets the query condition only when all tokens are in the column values. |
|tableName|The name of the table.|
|indexName|The name of the search index.|
|columnsToGet|Specifies whether to return all columns. By default, returnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If returnAll is set to true, all columns are returned. |
|getTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when the total number of rows that match the query conditions is returned. |

## Examples

```
/**
 * Search the table for rows where the value in Col_Keyword matches "hangzhou". Tablestore returns matched rows and the total number of matched rows.
 * @param client
 */
private static void matchQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    MatchQuery matchQuery = new MatchQuery(); // Set the query type to MatchQuery.
    matchQuery.setFieldName("Col_Keyword"); // Set the name of the column you want to query.
    matchQuery.setText("hangzhou"); // Set the keyword that you want to match.
    searchQuery.setQuery(matchQuery);
    searchQuery.setOffset(0); // Set offset to 0.
    searchQuery.setLimit(20); // Set limit to 20 to return up to 20 rows.
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);
    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount());
    System.out.println("Row: " + resp.getRows()); // By default, Tablestore returns only primary key columns if you do not set columnsToGet.

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set ReturnAll to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
            
```

