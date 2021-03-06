# Match query

You can use match query to query a table based on approximate matches. Tablestore tokenizes the values in TEXT columns and the keywords you use to perform match queries based on the analyzer you set. Therefore, Tablestore can perform match queries based on the tokenized text.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Python SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|field\_name|The name of the column you want to query. Match query applies to TEXT columns. |
|text|The keyword used to match the column values when you perform a match query. If the column to query is a TEXT column, the keyword is tokenized into multiple tokens based on the analyzer you set when you create the search index. By default, single-word tokenization is performed if you do not set the analyzer when you create the search index.

For example, if you set the analyzer type to single-word tokenization and use "this is" as a search string, you can obtain query results such as "..., this is tablestore", "is this tablestore", "tablestore is cool", "this", and "is". |
|query|The query type. To use match query, set this parameter to MatchQuery.|
|table\_name|The name of the table.|
|index\_name|The name of the search index.|
|limit|The maximum number of rows that the current query returns. To query the number of matched rows without detailed data, you can set limit to 0. In this case, Tablestore returns the number of matched rows without data from the table. |
|operator|The logical operator. By default, OR is used as the logical operator, which indicates that a row matches the query conditions when one of the tokens is matched. If you set the operator to AND, the row data meets the query condition only when all tokens are in the column values. |
|minimum\_should\_match|The minimum number of matched tokens contained in a column value. A row is returned only when the value of the queried column in the row contains at least the minimum number of matched tokenized keywords.

**Note:** minimum\_should\_match must be used together with the OR logical operator. |
|get\_total\_count|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to False, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to True. |
|columns\_to\_get|Specifies whether to return all columns of each matched row. -   If you set return\_type to ColumnReturnType.SPECIFIED, you can use column\_names to specify the columns to return.
-   If you set return\_type to ColumnReturnType.ALL, all columns are returned.
-   If you set return\_type to ColumnReturnType.NONE, only the primary key columns are returned. |

## Examples

Use match query to query the rows in which the values of the t columns approximately match 'this is 0'.

-   Perform match query by using Tablestore SDK for Python V5.2.1 or later

    By default, if you use Tablestore SDK for Python V5.2.1 or later to perform a match query, SearchResponse objects are returned. The following code provides a request sample:

    ```
    query = MatchQuery('t', 'this is 0')
    search_response = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```

    You can use the following sample request to return results of the Tuple type:

    ```
    query = MatchQuery('t', 'this is 0')
    rows, next_token, total_count, is_all_succeed, agg_results, group_by_results = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    ).v1_response()
    ```

-   Perform match query by using Tablestore SDK for Python of a version earlier than 5.2.1

    By default, if you use Tablestore SDK for Python of a version earlier than 5.2.1 to perform a match query, results of the Tuple type are returned. The following code provides a request sample:

    ```
    query = MatchQuery('t', 'this is 0')
    rows, next_token, total_count, is_all_succeed = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```


