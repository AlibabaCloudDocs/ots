# Nested query

You can use NestedQuery to query the data in the child rows of a nested field. You cannot directly query NESTED columns. To query a NESTED column, you must encapsulate NestedQuery by specifing the path of the NESTED field and a subquery. The subquery can be a query of any type.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/.NET SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/.NET SDK/Search index operations/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|Path|The path of the NESTED column. The path is similar to the tree structure. For example, news.title indicates the title subcolumn in the news column of the NESTED type.|
|Query|The query on the subcolumn in the NESTED column. The query can be of any query type.|
|ScoreMode|The value on which a score is calculated when multiple values exist for a column.|
|TableName|The name of the table.|
|IndexName|The name of the search index.|

## Examples

```
/// <summary>
/// The col1_nested column contains nested_1 and nested_2. The type is NESTED. You need to search the col1_nested.nested_1 column for data that matches "tablestore".
/// </summary>
/// <param name="otsClient"></param>
public static void NestedQuery(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    searchQuery.GetTotalCount = true;
    var nestedQuery = new NestedQuery();
    nestedQuery.Path = "col1_nested"; // Set the path of the NESTED field.
    TermQuery termQuery = new TermQuery("col1 _nested.nested_1",new ColumnValue("tablestore"));// Construct a subquery of NestedQuery.
    nestedQuery.Query = termQuery;
    nestedQuery.ScoreMode = ScoreMode.None;

    var request = new SearchRequest(TableName, IndexName, searchQuery);
    request.ColumnsToGet = new ColumnsToGet()
    {
        ReturnAll = true
    };

    var response = otsClient.Search(request);

    Console.WriteLine("Total Count:" + response.TotalCount);
}

                
```

