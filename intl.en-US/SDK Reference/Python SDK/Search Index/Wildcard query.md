# Wildcard query

You can use asterisks \(\*\) and question marks \(?\) as wildcards to search for data. An asterisk \(\*\) can represent any number of characters. A question mark \(?\) can represent any single character. The value to query can start with an asterisk \(\*\) or a question mark \(?\). For example, you can match query results such as "tablestore" by searching for "table\*e".

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A data table is created, and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Python SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|query|The query type. Set this parameter to WildcardQuery.|
|field\_name|The name of the column.|
|value|The string that contains wildcards. The string cannot exceed 10 bytes in length.|
|table\_name|The name of the table.|
|index\_name|The name of the search index.|
|limit|The maximum number of rows that the current query returns.|
|get\_total\_count|Specifies whether to return the total number of matched rows. Default value: False. Query performance is affected when this parameter is set to true. |
|ColumnsToGet|Specifies whether to return all columns of each matched row. -   If you set return\_type to ColumnReturnType.SPECIFIED, you must specify the columns to return.
-   If you set return\_type to ColumnReturnType.ALL, all columns are returned.
-   If you set return\_type to ColumnReturnType.NONE, only the primary key columns are returned. |

## Examples

Use wildcard query to query the rows in which the values of the k columns match 'key00\*'.

-   Perform wildcard query by using Tablestore SDK for Python V5.2.1 or later

    By default, if you use Tablestore SDK for Python V5.2.1 or later to perform a wildcard query, SearchResponse objects are returned. The following code provides a request sample:

    ```
    query = WildcardQuery('k', 'key00*')
    search_response = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```

    You can use the following sample request to return results of the Tuple type:

    ```
    query = WildcardQuery('k', 'key00*')
    rows, next_token, total_count, is_all_succeed, agg_results, group_by_results = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    ).v1_response()
    ```

-   Perform wildcard query by using Tablestore SDK for Python of a version earlier than 5.2.1

    By default, if you use Tablestore SDK for Python of a version earlier than 5.2.1 to perform a wildcard query, results of the Tuple type are returned. The following code provides a request sample:

    ```
    query = WildcardQuery('k', 'key00*')
    rows, next_token, total_count, is_all_succeed = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```


