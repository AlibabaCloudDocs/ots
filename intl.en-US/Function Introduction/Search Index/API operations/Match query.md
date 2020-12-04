# Match query

You can use match query to query a table based on approximate matches. Tablestore tokenizes the values in TEXT columns and the keywords you use to perform match queries based on the analyzer you set. Therefore, Tablestore can perform match queries based on the tokenized text.

For example, the value in the title column of a row is "this table is". Single-word tokenization is used. If you set the keyword to "this is" for the match query, Tablestore returns this row in the query result.

## Scenarios

You can use match query to query data in TEXT columns in full-text search scenarios.

## Operations

You can call the Search or ParallelScan operation and set the query type to MatchQuery to implement match query.

## Use Tablestore SDKs

You can use the following Tablestore SDKs to implement match query:

-   Tablestore SDK for Java: [Match query](/intl.en-US/SDK Reference/Java SDK/Search index/Match query.md)
-   Tablestore SDK for Go: [Match query](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Match query.md)
-   Tablestore SDK for Python: [Match query](/intl.en-US/SDK Reference/Python SDK/Search index-based queries/Match query.md)
-   Tablestore SDK for Node.js: [Match query](/intl.en-US/SDK Reference/NodeJS SDK/Search index-based queries/Match query.md)
-   Tablestore SDK for .NET: [Match query](/intl.en-US/SDK Reference/.NET SDK/Search index operations/Match query.md)
-   Tablestore SDK for PHP: [Match query](/intl.en-US/SDK Reference/PHP SDK/Search index/Match query.md)

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
|getTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when the total number of rows that match the query conditions is returned. |
|tableName|The name of the table.|
|indexName|The name of the search index.|
|columnsToGet|Specifies whether to return all columns. By default, returnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If returnAll is set to true, all columns are returned. |

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

