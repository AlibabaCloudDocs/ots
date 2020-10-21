# Phrase query

Match phrase query is similar to match query, but evaluates the position between multiple tokens. A row meets the query condition only when the order and position of the tokens in the row match those of the tokens contained in the tokenized keyword.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/. .NET SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [CreateSearchIndex](/intl.en-US/SDK Reference/. .NET SDK/Search index operations/CreateSearchIndex.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|FieldName|The name of the column used to match the query conditions.Match query applies to TEXT columns. |
|Text|The keyword used for the query to match the column values.If the column used for the match query is a TEXT column, the keyword is tokenized into multiple keywords based on the analyzer you set when you create the search index. By default, single-word tokenization is performed if you do not set the analyzer when you create the search index.

For example, if you use the phrase "this is" as the keyword for a query, you can obtain query results such as "..., this is tablestore" and "this is a table". Query results such as "this table is ..." or "is this a table" cannot be obtained. |
|Query|The query type. To use phrase query, set this parameter to MatchPhraseQuery.|
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|GetTotalCount|Specify whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to true. |
|ColumnsToGet|Specifies whether to return all columns of each matched rows. By default, ReturnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use Columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If ReturnAll is set to true, all columns are returned. |

## Examples

```
/// <summary>
/// Phrase query is similar to match query. However, a keyword is matched in a match query, whereas a phase is matched in a phase query.
/// </summary>
/// <param name="otsClient"></param>
public static void MatchPhraseQuery(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    // Set the query type to MatchPhraseQuery.
    searchQuery.Query = new MatchPhraseQuery(Text_type_col, "TableStore SearchIndex");
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

