# Term query

You can use term query to query data that exactly matches the specified value of a field. Term query is similar to queries based on string match conditions. When a table contains a TEXT string, Tablestore tokenizes the string and exactly matches tokens.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Python SDK/Search index-based queries/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|query\_type|The query type. To use term query, set this parameter to TermQuery.|
|field\_name|The name of the field used to match the query conditions.|
|term|The keyword used for the query to match the column values.The keyword is not tokenized but used as an exact word to match the column values.

If the field used to match the query conditions is a TEXT field, the field values are tokenized. A row meets the query conditions when the tokenized value of the specified field contains at least one term that can match the keyword. For example, if the value of a TEXT field in a row is "tablestore is cool", the value can be tokenized into "tablestore", "is", and "cool". The row can meet query conditions when you set "tablestore", "is", or "cool" to the keyword to match the values of the TEXT field. |
|table\_name|The name of the table.|
|index\_name|The name of the search index.|
|limit|The maximum number of rows that the current query returns.To query only the number of matched rows but not detailed data, you can set limit to 0. In this case, Tablestore returns the number of matched rows without data from the table. |
|get\_total\_count|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to False, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to True. |
|columns\_to\_get|Specifies whether to return all columns of each matched row. You can configure return\_type and column\_names for this parameter. -   If you set return\_type to ColumnReturnType.SPECIFIED, you can use column\_names to specify the columns to return.
-   If you set return\_type to ColumnReturnType.ALL, all columns are returned.
-   If you set return\_type to ColumnReturnType.NONE, only the primary key columns are returned. |

## Examples

```
query = TermQuery('k', 'key000')
rows, next_token, total_count, is_all_succeed = client.search(
    table_name, index_name, 
    SearchQuery(query, limit=100, get_total_count=True), 
    ColumnsToGet(return_type=ColumnReturnType.ALL)
)
```

