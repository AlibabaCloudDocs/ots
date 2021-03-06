# Prefix query

You can use prefix query to query data that matches a specified prefix. If the type of a field is TEXT, Tablestore tokenizes the string and matches tokens by using the specified prefix.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/.NET SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/.NET SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|FieldName|The name of the field used to match the query conditions.|
|Prefix|The value of the prefix.If the field used to match the query conditions is a TEXT field, the field values are tokenized. A row meets the query conditions when the tokenized value of the specified field contains at least one term that contains the specified prefix. |
|GetTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to true. |
|Query|The query type. To use prefix query, set this parameter to PrefixQuery.|
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|ColumnsToGet|Specifies whether to return all columns of each matched row. You can configure ReturnAll and Columns for this parameter. By default, ReturnAll is set to false, which indicates that not all columns are returned. If ReturnAll is set to false, you can configure Columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If ReturnAll is set to true, all columns are returned. |

## Examples

```
/// <summary>
/// Perform prefix query.
/// </summary>
/// <param name="otsClient"></param>
public static void PrefixQuery(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    // Set the query type to PrefixQuery, the field used to match the query conditions to Keyword_type_col, and the prefix to "Search".
    searchQuery.Query = new PrefixQuery(Keyword_type_col, "Search");
    searchQuery.GetTotalCount = true;
    var request = new SearchRequest(TableName, IndexName, searchQuery);
    request.ColumnsToGet = new ColumnsToGet()
    {
        ReturnAll = true // Specify that all columns of the matched rows are returned.
    };

    var response = otsClient.Search(request);

    Console.WriteLine("Total Count:" + response.TotalCount);
}
```

