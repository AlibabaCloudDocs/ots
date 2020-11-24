# Terms query

This query is similar to term query. However, terms query supports multiple terms. This query is also similar to the SQL IN operator. A row of data is returned if one of the keywords matches the field value. Terms query is equivalent to the IN operator in SQL statements.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Python SDK/Search index-based queries/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|query\_type|The query type. To use terms query, set this parameter to TermsQuery.|
|field\_name|The name of the field used to match the query conditions.|
|terms|The keywords used in the query to match the column values.A row meets the query conditions when the value of the specified column exactly matches at least one keyword. |
|table\_name|The name of the table.|
|index\_name|The name of the search index.|
|limit|The maximum number of rows that the current query returns.To query only the number of matched rows but not detailed data, you can set limit to 0. In this case, Tablestore returns the number of matched rows without data from the table. |
|get\_total\_count|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to False, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to True. |
|columns\_To\_get|Specifies whether to return all columns of each matched row. You can configure return\_type and column\_names for this parameter. -   If you set return\_type to ColumnReturnType.SPECIFIED, you can configure column\_names to specify the columns to return.
-   If you set return\_type to ColumnReturnType.ALL, all columns are returned.
-   If you set return\_type to ColumnReturnType.NONE, only the primary key columns are returned. |

## Examples

```
query = TermsQuery('k', ['key000', 'key100', 'key888', 'key999', 'key908', 'key1000'])
rows, next_token, total_count, is_all_succeed = client.search(
    table_name, index_name, 
    SearchQuery(query, limit=100, get_total_count=True), 
    ColumnsToGet(return_type=ColumnReturnType.ALL)
)
```

