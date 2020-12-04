# Match phrase query

Match phrase query is similar to match query, but evaluates the position between multiple tokens. A row meets the query condition only when the order and position of the tokens in the row match those of the tokens contained in the tokenized keyword.

For example, the value in the column is "Hangzhou West Lake Scenic Area" and the keyword you specify in Query is "Hangzhou Scenic Area". Tablestore returns the row when you use match query. However, when you use match phrase query, Tablestore does not return the row. The distance between "Hangzhou" and "Scenic Area" in Query is 0. But the distance in the column to query of this row is 2 because the two words "West" and "Lake" exist between "Hangzhou" and "Scenic Area".

## API operations

You can call the Search or ParallelScan operation and set the query type to MatchPhraseQuery to perform match phrase query.

## Usage

You can use the following Tablestore SDKs to implement match phrase query:

-   Tablestore SDK for Java: [Match phrase query](/intl.en-US/SDK Reference/Java SDK/Search index/Phrase query.md)
-   Tablestore SDK for Go: [Match phrase query](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Phrase query.md)
-   Tablestore SDK for Python: [Match phrase query](/intl.en-US/SDK Reference/Python SDK/Search index-based queries/Phrase query.md)
-   Tablestore SDK for Node.js: [Match phrase query](/intl.en-US/SDK Reference/NodeJS SDK/Search index-based queries/Phrase query.md)
-   Tablestore SDK for .NET: [Match phrase query](/intl.en-US/SDK Reference/.NET SDK/Search index operations/Phrase query.md)
-   Tablestore SDK for PHP: [Match phrase query](/intl.en-US/SDK Reference/PHP SDK/Search index/Phrase query.md)

## Parameters

|Parameter|Description|
|---------|-----------|
|fieldName|The name of the column to query.Match phrase query applies to TEXT columns. |
|text|The keyword used to match the column values when you perform a match phrase query.If the column to query is a TEXT column, the keyword is tokenized into multiple tokens based on the analyzer you set when you create the search index. By default, single-word tokenization is performed if you do not set the analyzer when you create the search index.

For example, if you search for the phrase "this is", you can obtain query results such as "..., this is tablestore" and "this is a table". You cannot obtain query results such as "this table is ..." and "is this a table". |
|query|The query type, which is set to matchPhraseQuery.|
|offset|The position from which the current query starts.|
|limit|The maximum number of rows that the current query returns.To query only the number of matched rows without returning specific data, you can set limit to 0. Then, Tablestore returns the number of matched rows without data from the table. |
|getTotalCount|Specify whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when the total number of rows that match the query conditions is returned. |
|tableName|The name of the data table.|
|indexName|The name of the search index.|
|columnsToGet|Specify whether to return all columns. By default, returnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If returnAll is set to true, all columns are returned. |

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

