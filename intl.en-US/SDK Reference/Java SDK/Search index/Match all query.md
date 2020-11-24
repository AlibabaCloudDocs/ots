# Match all query

You can use match all query to query the total number of rows in a table or obtain multiple rows of data.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialize SDK for Java.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Java SDK/Search index/Create search indexes.md).

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

