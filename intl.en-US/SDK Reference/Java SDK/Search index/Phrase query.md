# Phrase query

Match phrase query is similar to match query, but evaluates the position between multiple tokens. A row meets the query condition only when the order and position of the tokens in the row match those of the tokens contained in the tokenized keyword.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialize SDK for Java](/intl.en-US/SDK Reference/Java SDK/Initialize SDK for Java.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Java SDK/Search index/CreateSearchIndex.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|fieldName|The name of the column to query.Match phrase query applies to TEXT columns. |
|text|The keyword used to match the column values when you perform a match phrase query.If the column to query is a TEXT column, the keyword is tokenized into multiple tokens based on the analyzer you set when you create the search index. By default, single-word tokenization is performed if you do not set the analyzer when you create the search index.

For example, if you search for the phrase "this is", you can obtain query results such as "..., this is tablestore" and "this is a table". You cannot obtain query results such as "this table is ..." and "is this a table". |
|query|The query type, which is set to matchPhraseQuery.|
|offset|The position from which the current query starts.|
|limit|The maximum number of rows that the current query returns.To query only the number of matched rows without returning specific data, you can set limit to 0. Then, Tablestore returns the number of matched rows without data from the table. |
|tableName|The name of the data table.|
|indexName|The name of the search index.|
|columnsToGet|Specify whether to return all columns. By default, returnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If returnAll is set to true, all columns are returned. |
|getTotalCount|Specify whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when the total number of rows that match the query conditions is returned. |

## Examples

```
/**
 * Search the table for rows where the values in Col_Text match "hangzhou shanghai". Set the query type to matchPhraseQuery. Tablestore returns the total number of rows that match the phrase and matched rows in this query.
 * @param client
 */
private static void matchPhraseQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    MatchPhraseQuery matchPhraseQuery = new MatchPhraseQuery(); // Set the query type to MatchPhraseQuery.
    matchPhraseQuery.setFieldName("Col_Text"); // Set the name of the column to query.
    matchPhraseQuery.setText("hangzhou shanghai"); // Set the keyword that you want to match.
    searchQuery.setQuery(matchPhraseQuery);
    searchQuery.setOffset(0); // Set offset to 0.
    searchQuery.setLimit(20); // Set limit to 20 to return up to 20 rows.
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);
    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount());
    System.out.println("Row: " + resp.getRows()); // By default, primary key columns of the matched rows are returned.

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set ReturnAll to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

