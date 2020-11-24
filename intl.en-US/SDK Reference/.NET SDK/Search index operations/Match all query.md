# Match all query

You can use match all query to query the total number of rows in a table or obtain multiple rows of data.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/.NET SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/.NET SDK/Search index operations/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|Query|The query type. To use match all query, set this parameter to MatchAllQuery.|
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|Limit|The maximum number of rows that the current query returns.To query only the number of matched rows but not detailed data, you can set Limit to 0. In this case, Tablestore returns the number of matched rows without data from the table. |
|GetTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to True. |
|ColumnsToGet|Specifies whether to return all columns of each matched row. By default, ReturnAll is set to false, which indicates that not all columns are returned. If ReturnAll is set to false, you can configure Columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If ReturnAll is set to true, all columns are returned. |

## Examples

```
/// <summary>
/// Query all the rows in the table and return the total number of the rows.
/// </summary>
/// <param name="otsClient"></param>
public static void MatchAllQuery(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    searchQuery.Query = new MatchAllQuery();
    searchQuery.GetTotalCount = true; // Set GetTotalCount to true to return the total number of rows that meet the query conditions.
    /*
    * In the result of a match all query, the value of TotalCount indicates the total number of rows in the table.
    * To query only the number of matched rows but not detailed data, you can set limit to 0. In this case, Tablestore returns the number of matched rows without data from the table.
    */
    searchQuery.Limit = 0;
    var request = new SearchRequest(TableName, IndexName, searchQuery);

    var response = otsClient.Search(request);
    // Check whether Tablestore returns all the matched data. When the value of isAllSuccess is false, Tablestore may fail to query a part of partitions and return only a part of data.
    Console.WriteLine("IsAllSuccess:" + response.IsAllSuccess);
    Console.WriteLine("Total Count:" + response.TotalCount);
}
```

