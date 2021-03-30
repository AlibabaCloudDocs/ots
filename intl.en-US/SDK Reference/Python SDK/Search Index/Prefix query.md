# Prefix query

You can use prefix query to query data that matches a specified prefix. If the type of a field is TEXT, Tablestore tokenizes the string and matches tokens by using the specified prefix.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A data table is created, and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Python SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|field\_name|The name of the column used to match the query conditions.|
|prefix|The prefix. If the field used to match the query conditions is a TEXT field, the field values are tokenized. A row meets the query conditions when the tokenized value of the specified field contains at least one term that contains the specified prefix. |
|query|The type of the query. Set the query type to PrefixQuery.|
|table\_name|The name of the table.|
|index\_name|The name of the search index.|
|limit|The maximum number of rows that the current query returns. To query the number of matched rows without detailed data, you can set limit to 0. In this case, Tablestore returns the number of matched rows without data from the table. |
|get\_total\_count|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to False, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to True. |
|columns\_to\_get|Specifies whether to return all columns of each matched row. You can configure return\_type and column\_names for this parameter. -   If you set return\_type to ColumnReturnType.SPECIFIED, you can use column\_names to specify the columns to return.
-   If you set return\_type to ColumnReturnType.ALL, all columns are returned.
-   If you set return\_type to ColumnReturnType.NONE, only the primary key columns are returned. |

## Examples

Use prefix query to query the rows in which the values of the k columns are prefixed with 'key00'.

-   Perform prefix query by using Tablestore SDK for Python V5.2.1 or later

    By default, if you use Tablestore SDK for Python V5.2.1 or later to perform a prefix query, SearchResponse objects are returned. The following code provides a request sample:

    ```
    query = PrefixQuery('k', 'key00')
    search_response = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```

    You can use the following sample request to return results of the Tuple type:

    ```
    query = PrefixQuery('k', 'key00')
    rows, next_token, total_count, is_all_succeed, agg_results, group_by_results = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    ).v1_response()
    ```

-   Perform prefix query by using Tablestore SDK for Python of a version earlier than 5.2.1

    By default, if you use Tablestore SDK for Python of a version earlier than 5.2.1 to perform a prefix query, results of the Tuple type are returned. The following code provides a request sample:

    ```
    query = PrefixQuery('k', 'key00')
    rows, next_token, total_count, is_all_succeed = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```


