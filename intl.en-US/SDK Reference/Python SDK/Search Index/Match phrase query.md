# Match phrase query

Match phrase query is similar to match query, but evaluates the position between multiple tokens. A row meets the query condition only when the order and position of the tokens in the row match those of the tokens contained in the tokenized keyword.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Python SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|field\_name|The name of the column you want to query. Match phrase query applies to TEXT columns. |
|text|The keyword used to match the column values when you perform a phase query. If the column to query is a TEXT column, the keyword is tokenized into multiple tokens based on the analyzer you set when you create the search index. By default, single-word tokenization is performed if you do not set the analyzer when you create the search index.

For example, if you query the phrase "this is", results such as "..., this is tablestore" and "this is a table" are returned. Query results such as "this table is ..."or "is this a table" are not returned. |
|query|The query type. To use match phrase query, set this parameter to MatchPhraseQuery.|
|table\_name|The name of the base table.|
|index\_name|The name of the search index.|
|limit|The maximum number of rows that the current query returns. To query just the number of matched rows without any detailed data, you can set limit to 0. In this case, Tablestore returns the number of matched rows without data from the table. |
|get\_total\_count|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to False, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to True. |
|columns\_to\_get|Specifies whether to return all columns of each matched row. -   If you set return\_type to ColumnReturnType.SPECIFIED, you can use column\_names to specify the columns to return.
-   If you set return\_type to ColumnReturnType.ALL, all columns are returned.
-   If you set return\_type to ColumnReturnType.NONE, only the primary key columns are returned. |

## Examples

Use match phrase query to query the rows whose t column values match the 'this is' phrase in sequence.

-   Perform match phrase query by using Tablestore SDK for Python V5.2.1 or later

    By default, if you use Tablestore SDK for Python V5.2.1 or later to perform a match phrase query, SearchResponse objects are returned. The following code provides a request sample:

    ```
    query = MatchPhraseQuery('t', 'this is')
    search_response = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```

    You can use the following sample request to return results of the Tuple type:

    ```
    query = MatchPhraseQuery('t', 'this is')
    rows, next_token, total_count, is_all_succeed, agg_results, group_by_results = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    ).v1_response()
    ```

-   Perform match phrase query by using Tablestore SDK for Python of a version earlier than 5.2.1

    By default, if you use Tablestore SDK for Python of a version earlier than 5.2.1 to perform a match phrase query, results of the Tuple type are returned. The following code provides a request sample:

    ```
    query = MatchPhraseQuery('t', 'this is')
    rows, next_token, total_count, is_all_succeed = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```


