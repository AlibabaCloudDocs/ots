# Match all query

You can use match all query to query the total number of rows in a table or obtain multiple rows of data.

## API operations

You can call the Search or ParallelScan operation and set the query type to MatchAllQuery to perform match all query.

## Usage

You can use the following Tablestore SDKs to implement match all query:

-   Tablestore SDK for Java: [Match all query](/intl.en-US/SDK Reference/Java SDK/Search index/Match all query.md)
-   Tablestore SDK for Go: [Match all query](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Match all query.md)
-   Tablestore SDK for Python: [Match all query](/intl.en-US/SDK Reference/Python SDK/Search index-based queries/Match all query.md)
-   Tablestore SDK for Node.js: [Match all query](/intl.en-US/SDK Reference/NodeJS SDK/Search index-based queries/Match all query.md)
-   Tablestore SDK for .NET: [Match all query](/intl.en-US/SDK Reference/.NET SDK/Search index operations/Match all query.md)
-   Tablestore SDK for PHP: [Match all query](/intl.en-US/SDK Reference/PHP SDK/Search index/Query by match.md)

## Parameters

|Parameter|Description|
|---------|-----------|
|query|The query type, which is set to MatchAllQuery.|
|tableName|The name of the data table.|
|indexName|The name of the search index.|
|limit|The maximum number of rows that the current query returns.To query only the number of matched rows without returning specific data, you can set limit to 0. Then, Tablestore returns the number of matched rows without data from the table. |
|columnsToGet|Specifies whether to return all columns. By default, returnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If returnAll is set to true, all columns are returned. |
|getTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when the total number of rows that match the query conditions is returned. |

## Examples

```
/**
 * Use match all query to query the total number of rows in a table.
 * @param client
 */
private static void matchAllQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();

    /**
    * Set the query type to MatchAllQuery.
    */
    searchQuery.setQuery(new MatchAllQuery()); 

    /**
     * In the MatchAllQuery-based query result, the value of TotalCount is the total number of rows in the table.
     * To query only the number of matched rows without returning specific data, you can set limit to 0. Then, Tablestore returns the number of rows without data from the table.
     */
    searchQuery.setLimit(0);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    /**
    * Set the total number of matched rows to return.
    */ 
    searchQuery.setGetTotalCount(true);
    SearchResponse resp = client.search(searchRequest);
    /**
     * Check whether Tablestroe returns all the matched data. When the value of isAllSuccess is false, Tablestore may fail to query a part of partitions and return a part of data.
     */
    if (!resp.isAllSuccess()) {
        System.out.println("NotAllSuccess!") ;
    }
    System.out.println("IsAllSuccess: " + resp.isAllSuccess());
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of rows is displayed.
    System.out.println(resp.getRequestId());
}
            
```

