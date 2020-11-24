# Match query

You can use match query to query a table based on approximate matches. Tablestore tokenizes the values in TEXT columns and the keywords you use to perform match queries based on the analyzer you set. Therefore, Tablestore can perform match queries based on the tokenized text.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/.NET SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/.NET SDK/Search index operations/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|FieldName|The name of the column used to match the query conditions.Match query applies to TEXT columns. |
|Text|The keyword used for the query to match the column values.If the column used for the match query is a TEXT column, the keyword is tokenized into multiple keywords based on the analyzer you set when you create the search index. By default, single-word tokenization is performed if you do not set the analyzer when you create the search index.

For example, you can specify a TEXT column for a query, and set the analyzer to perform single-word tokenization. If you use the phrase "this is" as the keyword for a query, you can obtain query results such as "..., this is tablestore", "is this tablestore", "tablestore is cool", "this", and "is". |
|Query|The query type. To use match query, set this parameter to MatchQuery.|
|Operator|The logical operator. By default, OR is used as the logical operator, which indicates that a row matches the query conditions when one of the analyzed keywords is matched.If you set operator to AND, a row meets the query condition only when all tokenized keywords are matched. |
|MinimumShouldMatch|The minimum number of matched tokenized keywords contained in a column value.A row is returned only when the value of the queried column in the row contains at least the minimum number of matched tokenized keywords.

**Note:** minimumShouldMatch must be used together with the OR logical operator. |
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|GetTotalCount|Specify whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to true. |
|ColumnsToGet|Specifies whether to return all columns of each matched rows. By default, ReturnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use Columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If ReturnAll is set to true, all columns are returned. |

## Examples

```
/// <summary>
/// Use fuzzy matching and phase or approximate query to return the specified columns.
/// </summary>
/// <param name="otsClient"></param>
public static void MatchQuery(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    // Set the query type to MatchQuery, the column used to match the query conditions to Text_type_col, and the keyword used for the query to match the column values to "SearchIndex".
    searchQuery.Query = new MatchQuery(Text_type_col, "SearchIndex");
    searchQuery.GetTotalCount = true;
    var request = new SearchRequest(TableName, IndexName, searchQuery);
    request.ColumnsToGet = new ColumnsToGet()
    {
        Columns = new List<string>() { Long_type_col, Text_type_col, Keyword_type_col }
    };

    var response = otsClient.Search(request);

    Console.WriteLine("Total Count:" + response.TotalCount);
}
```

