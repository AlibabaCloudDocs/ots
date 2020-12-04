# Range query

You can use RangeQuery to query data that falls within a specified range. When a table contains a TEXT string, Tablestore tokenizes the string and matches tokens by using the specified prefix.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Java SDK/Search index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|fieldName|The name of the field you want to match.|
|from|The value from which the query starts.When you set range conditions, you can use greaterThan to indicate the greater than \(\>\) sign and greaterThanOrEqual to indicate the greater-than-or-equal-to \(≥\) sign. |
|to|The value with which the query ends.When you set range conditions, you can use lessThan to indicate the less-than \(<\) sign and lessThanOrEqual to indicate the less-than-or-equal-to \(≤\) sign. |
|includeLower|Specifies whether to include the value of the from parameter in the response. Type: Boolean.|
|includeUpper|Specifies whether to include the value of the to parameter in the response. Type: Boolean.|
|query|The query type, which is set to RangeQuery.|
|sort|The sorting method. For more information, see [Sort](/intl.en-US/Function Introduction/Search Index/API operations/Sort.md).|
|getTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when the total number of rows that match the query conditions is returned. |
|tableName|The name of the table.|
|indexName|The name of the search index.|
|columnsToGet|Specifies whether to return all columns. By default, returnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If returnAll is set to true, all columns are returned. |

## Examples

```
/**
 * Search the table for rows where the value in Col_Long is greater than 3. Tablestore sorts these rows by Col_Long in descending order.
 * @param client
 */
private static void rangeQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    RangeQuery rangeQuery = new RangeQuery(); // Set the query type to RangeQuery.
    rangeQuery.setFieldName("Col_Long");  // Set the name of the field that you want to match.
    rangeQuery.greaterThan(ColumnValue.fromLong(3));  // Specify the range of the values of the field. The matched values are greater than 3.
    searchQuery.setGetTotalCount(true);
    searchQuery.setQuery(rangeQuery);
    // Sort the query results by Col_Long in descending order.
    FieldSort fieldSort = new FieldSort("Col_Long");
    fieldSort.setOrder(SortOrder.DESC);
    searchQuery.setSort(new Sort(Arrays.asList((Sort.Sorter)fieldSort)));
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

