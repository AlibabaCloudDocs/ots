# Range query

You can use range query to query data that falls within a specified range. If the type of a field is TEXT, Tablestore tokenizes the string and matches any of the tokens that fall within the specified range.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Python SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|field\_name|The name of the field used to match the query conditions.|
|range\_from|The value from which the query starts.|
|range\_to|The value with which the query ends.|
|include\_lower|Specifies whether to include the value of range\_from in the response. The value type is Boolean.|
|include\_upper|Specifies whether to include the value of range\_to in the response. The value type is Boolean.|
|table\_name|The name of the table.|
|index\_name|The name of the search index.|
|query|The query type. To use range query, set this parameter to RangeQuery.|
|limit|The maximum number of rows that the current query returns.To query only the number of matched rows but not detailed data, you can set limit to 0. In this case, Tablestore returns the number of matched rows without data from the table. |
|get\_total\_count|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to False, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to True. |
|columns\_to\_get|Specifies whether to return all columns of each matched row. You can configure return\_type and column\_names for this parameter.-   If you set return\_type to ColumnReturnType.SPECIFIED, you can configure column\_names to specify the columns to return.
-   If you set return\_type to ColumnReturnType.ALL, all columns are returned.
-   If you set return\_type to ColumnReturnType.NONE, only the primary key columns are returned. |

## Examples

```
query = RangeQuery('k', 'key100', 'key200', include_lower=False, include_upper=False)
rows, next_token, total_count, is_all_succeed = client.search(
    table_name, index_name, 
    SearchQuery(query, limit=100, get_total_count=True), 
    ColumnsToGet(return_type=ColumnReturnType.ALL)
)
```

