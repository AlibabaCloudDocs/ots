# Match all query

You can use match all query to query the total number of rows in a table or obtain multiple rows of data.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A data table is created, and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Python SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|query|The query type. To use match all query, set this parameter to MatchAllQuery.|
|table\_name|The name of the table.|
|index\_name|The name of the search index.|
|limit|The maximum number of rows that the current query returns. To query the number of matched rows without detailed data, you can set limit to 0. In this case, Tablestore returns the number of matched rows without data from the table. |
|get\_total\_count|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to true. |
|columns\_to\_get|Specifies whether to return all columns of each matched row. -   If you set return\_type to ColumnReturnType.SPECIFIED, you can use column\_names to specify the columns to return.
-   If you set return\_type to ColumnReturnType.ALL, all columns are returned.
-   If you set return\_type to ColumnReturnType.NONE, only the primary key columns are returned. |

## Examples

Use match all query to query the total number of rows in the table.

-   Perform match all query by using Tablestore SDK for Python V5.2.1 or later

    By default, if you use Tablestore SDK for Python V5.2.1 or later to perform a match all query, SearchResponse objects are returned. The following code provides a request sample:

    ```
    query = MatchAllQuery()
    all_rows = []
    next_token = None
    
    while not all_rows or next_token:
        search_response = client.search(table_name, index_name,
            SearchQuery(query, next_token=next_token, limit=100, get_total_count=True),
            columns_to_get=ColumnsToGet(['k', 't', 'g', 'ka', 'la'], ColumnReturnType.SPECIFIED))
        all_rows.extend(search_response.rows)
    
    for row in all_rows:
        print row
    
    print 'Total rows:', len(all_rows)
    ```

    You can use the following sample request to return results of the Tuple type:

    ```
    query = MatchAllQuery()
    all_rows = []
    next_token = None
    
    while not all_rows or next_token:
        rows, next_token, total_count, is_all_succeed, agg_results, group_by_results = client.search(table_name, index_name,
            SearchQuery(query, next_token=next_token, limit=100, get_total_count=True),
            columns_to_get=ColumnsToGet(['k', 't', 'g', 'ka', 'la'], ColumnReturnType.SPECIFIED)).v1_response()
        all_rows.extend(rows)
    
    for row in all_rows:
        print row
    
    print 'Total rows:', len(all_rows)
    ```

-   Perform match all query by using Tablestore SDK for Python of a version earlier than 5.2.1

    By default, if you use Tablestore SDK for Python of a version earlier than 5.2.1 to perform a match all query, results of the Tuple type are returned. The following code provides a request sample:

    ```
    query = MatchAllQuery()
    all_rows = []
    next_token = None
    
    while not all_rows or next_token:
        rows, next_token, total_count, is_all_succeed = client.search(table_name, index_name,
            SearchQuery(query, next_token=next_token, limit=100, get_total_count=True),
            columns_to_get=ColumnsToGet(['k', 't', 'g', 'ka', 'la'], ColumnReturnType.SPECIFIED))
        all_rows.extend(rows)
    
    for row in all_rows:
        print row
    
    print 'Total rows:', len(all_rows)
    ```


