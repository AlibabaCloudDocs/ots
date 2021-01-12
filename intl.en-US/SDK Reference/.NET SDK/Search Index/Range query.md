# Range query

You can use range query to query data that falls within a specified range. If the type of a field is TEXT, Tablestore tokenizes the string and matches tokens by using the specified prefix.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/.NET SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/.NET SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|GetTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to True. |
|Query|The query type. To use range query, set this parameter to RangeQuery.|
|FieldName|The name of the field used to match the query conditions.|
|From|The value from which the query starts.|
|To|The value with which the query ends.|
|IncludeLower|Specifies whether to include the value of the From parameter in the response. The value type is Boolean.|
|IncludeUpper|Specifies whether to include the value of the To parameter in the response. The value type is Boolean.|
|TableName|The name of the table.|
|IndexName|The name of the search index.|

## Examples

```
/// <summary>
/// Perform a range query. Specify a range that starts from the From value and ends at the To value. Search for data within the range.
/// </summary>
/// <param name="otsClient"></param>
public static void RangeQuery(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    searchQuery.GetTotalCount = true;
    var rangeQuery = new RangeQuery(Long_type_col, new ColumnValue(0), new ColumnValue(6));
    // Specify that the value of the From parameter is included in the response. The rows where the values of the specified query column are greater than or equal to 0 is returned.
    rangeQuery.IncludeLower = true;
    searchQuery.Query = rangeQuery;
    var request = new SearchRequest(TableName, IndexName, searchQuery);
    var response = otsClient.Search(request);

    Console.WriteLine("Total Count:" + response.TotalCount);
}
```

