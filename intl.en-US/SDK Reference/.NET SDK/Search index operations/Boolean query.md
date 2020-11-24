# Boolean query

This topic describes how to use Boolean query to query data based on a combination of filter conditions. This type of query contains one or more subqueries as filter conditions. Tablestore returns the rows that match the subqueries. Each subquery can be of any type, including BoolQuery.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/.NET SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/.NET SDK/Search index operations/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|MustQueries|Only row data that matches all subqueries is returned. This parameter is equivalent to the AND operator.|
|MustNotQueries|Only row data that must not match all subqueries is returned. This parameter is equivalent to the NOT operator.|
|FilterQueries|Only row data that matches all subfilters is returned. Filter is similar to Query. The difference is that Filter does not calculate the relevance score based on the number of subfilters that the row meet.|
|ShouldQueries|The list of subqueries the query result can or cannot match. This parameter is equivalent to the OR operator.Only rows that meet the minimum number of subquery conditions specified in ShouldQueries can match the result.

The overall relevance score is higher if more subqueries of ShouldQueries are met. |
|MinimumShouldMatch|The minimum number that row data must match for subquery conditions in ShouldQueries. Default value: 1.|
|TableName|The name of the table.|
|IndexName|The name of the search index.|

## Examples

```
/// <summary>
/// BoolQuery contains one or more subqueries. Each subquery has its own type.
///MustQueries: Row data must match the condition.
///ShouldQueries: More than one condition is included in ShouldQueries. At least one condition is met, and the data in this row meets the query condition.
///MustNotQueries: row data must not match the condition.
///MinimumShouldMatch: the minimum number of conditions that must be met.
/// </summary>
/// <param name="otsClient"></param>
public static void BoolQuery(OTSClient otsClient)
{
    Console.WriteLine("\n Start bool query...");

    var searchQuery = new SearchQuery();
    searchQuery.GetTotalCount = true;
    var boolQuery = new BoolQuery();
    var shouldQuerys = new List<IQuery>();
    shouldQuerys.Add(new TermQuery(Keyword_type_col, new ColumnValue("SearchIndex")));
    shouldQuerys.Add(new TermQuery(Keyword_type_col, new ColumnValue("TableStore")));
    boolQuery.ShouldQueries = shouldQuerys;
    boolQuery.MinimumShouldMatch = 1;

    searchQuery.Query = boolQuery;

    var request = new SearchRequest(TableName, IndexName, searchQuery);
    request.ColumnsToGet = new ColumnsToGet()
    {
        ReturnAll = true
    };

    var response = otsClient.Search(request);

    Console.WriteLine("Total Count:" + response.TotalCount);
    foreach (var row in response.Rows)
    {
        PrintRow(row);
    }
}
```

