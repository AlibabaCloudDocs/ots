# Term query

You can use term query to query data that exactly matches the specified value of a field. Term query is similar to queries based on string match conditions. If the type of a field is TEXT, Tablestore tokenizes the string and exactly matches tokens.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/.NET SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/.NET SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|FieldName|The name of the field used to match the query conditions.|
|Term|The keyword used for the query to match the column values.The keyword is not tokenized but used as an exact word to match the column values.

If the field used to match the query conditions is a TEXT field, the field values are tokenized. A row meets the query conditions when the tokenized value of the specified field contains at least one term that can match the keyword. For example, if the value of a TEXT field in a row is "tablestore is cool", the value can be tokenized into "tablestore", "is", and "cool". The row can meet query conditions when you set "tablestore", "is", or "cool" to the keyword to match the values of the TEXT field. |
|GetTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to True. |
|Query|The query type. To use term query, set this parameter to TermQuery.|
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|ColumnsToGet|Specifies whether to return all columns of each matched row. You can configure ReturnAll and Columns for this parameter. By default, ReturnAll is set to false, which indicates that not all columns are returned. If ReturnAll is set to false, you can configure Columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If ReturnAll is set to true, all columns are returned. |

## Examples

```
/// <summary>
/// Search the table for rows where the value of Keyword_type_col exactly matches "SearchIndex".
/// </summary>
/// <param name="otsClient"></param>
public static void TermQuery(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    // Specify that the total number of matched rows is returned.
    searchQuery.GetTotalCount = true;
    // Set the query type to TermQuery, the field used to match the query conditions to Keyword_type_col, and the keyword used for the query to match the field values to "SearchIndex".
    searchQuery.Query = new TermQuery(Keyword_type_col, new ColumnValue("SearchIndex"));

    var request = new SearchRequest(TableName, IndexName, searchQuery);
    request.ColumnsToGet = new ColumnsToGet()
    {
        ReturnAll = true // Specify that all columns of the matched rows are returned.
    };
    var response = otsClient.Search(request);
    // Check the value of NextToken.
```

