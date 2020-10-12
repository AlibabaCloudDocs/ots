# Wildcard query

You can use asterisks \(\*\) and question marks \(?\) to search for data that matches wildcards. An asterisk \(\*\) represents any number of characters. A question mark \(?\) represents any single character. The matched value can start with asterisks \(\*\) or question marks \(?\). For example, if you search for string "table\*e", you can retrieve query results such as "tablestore".

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/. .NET SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [CreateSearchIndex](/intl.en-US/SDK Reference/. .NET SDK/Search index operations/CreateSearchIndex.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|Query|The query type. Set the query type to WildcardQuery.|
|FieldName|The column name.|
|Value|The string that contains wildcards. The string cannot exceed 10 bytes in length.|
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|GetTotalCount|Specifies whether to return the total number of matched rows. The default value is false, which indicates that the total number of rows in the table is not returned. If GetTotalCount is set to true, the query performance is affected. |
|ColumnsToGet|Specifies whether to return all columns. The default value of ReturnAll is false, which indicates that not all columns are returned. You can specify the columns to be returned by using ColumnsToGet. If you do not use ColumnsToGet to specify the columns to be returned , only the primary key columns are returned.

If ReturnAll is set to true, all columns are returned. |

## Examples

```
/// <summary>
/// Wildcard query. An asterisk (*) that represents any length of characters and a question mark (?) that represents any single character are supported.
/// </summary>
/// <param name="otsClient"></param>
public static void WildcardQuery(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    // Set the query type to WildcardQuery. The value based on which to match column values can contain wildcards.
    searchQuery.Query = new WildcardQuery(Keyword_type_col, "*Search*");
    searchQuery.GetTotalCount = true;
    var request = new SearchRequest(TableName, IndexName, searchQuery);
    request.ColumnsToGet = new ColumnsToGet()
    {
        ReturnAll = true
    };

    var response = otsClient.Search(request);

    Console.WriteLine("Total Count:" + response.TotalCount);
}
```

