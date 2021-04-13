# Nested query

This topic describes how to use nested query to query the data in the child rows of nested fields. Nested fields cannot be directly queried. To query a nested field, you must specify the path of the nested field and a subquery in a NestedQuery object. The subquery can be a query of any type.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Java SDK/Search Index/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|path|The path of the nested field. The path is similar to the tree structure. For example, news.title indicates the title subcolumn in the nested field named news.|
|query|The query implemented on the subcolumn in the nested field. The query can be of any query type.|
|scoreMode|Specifies which value is used to calculate the score when the field contains multiple values.|
|getTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to true. |
|tableName|The name of the table.|
|indexName|The name of the search index.|
|columnsToGet|Specifies whether to return all columns of each matched row. You can configure returnAll and columns for this parameter. By default, returnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If returnAll is set to true, all columns are returned. |

## Examples

The following examples show how to use nested queries.

-   Example 1

    The following code provides an example on how to query data that matches the "tablestore" condition in the col\_nested.nested\_1 column. In this example, the nested field named col\_nested includes two subcolumns: nested\_1 and nested\_2.

    ```
    private static void nestedQuery(SyncClient client) {
        SearchQuery searchQuery = new SearchQuery();
        NestedQuery nestedQuery = new NestedQuery(); // Set the query type to NestedQuery.
        nestedQuery.setPath("col_nested"); // Set the path of the nested column.
        TermQuery termQuery = new TermQuery(); // Create the child query of the NestedQuery object.
        termQuery.setFieldName("col_nested.nested_1"); // Set the column name. Note the path that includes nested columns. 
        termQuery.setTerm(ColumnValue.fromString("tablestore")); // Specify the value that you want to query.
        nestedQuery.setQuery(termQuery);
        nestedQuery.setScoreMode(ScoreMode.None);
        searchQuery.setQuery(nestedQuery);
        //searchQuery.setGetTotalCount(true);// Specify the setGetTotalCount parameter to true to return the total number of rows that match the query condition.
    
        SearchRequest searchRequest = new SearchRequest("sampleTable", "sampleSearchIndex", searchQuery);
        // You can set the columnsToGet parameter to specify whether to return all columns or only specified columns. By default, if this parameter is not set, only the primary columns are returned.
        //SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
        //columnsToGet.setReturnAll(true); // Set the setReturnAll parameter to return all columns.
        //columnsToGet.setColumns(Arrays.asList("ColName1","ColName2")); // Set the setColumns parameter to return specified columns.
        //searchRequest.setColumnsToGet(columnsToGet);
    
        SearchResponse resp = client.search(searchRequest);
        //System.out.println("TotalCount: "+ resp.getTotalCount()); // Display the total number of matched columns but not returned columns.
    System.out.println("Row:  " + resp.getRows());
    } 
        TermQuery termQuery = new TermQuery(); // Create the child query of the NestedQuery object. 
        termQuery.setFieldName("col_nested.nested_1"); // Set the column name. Note the path that includes nested columns.  
        termQuery.setTerm(ColumnValue.fromString("tablestore")); // Specify the value that you want to query.
        nestedQuery.setQuery(termQuery);
        nestedQuery.setScoreMode(ScoreMode.None);
        searchQuery.setQuery(nestedQuery);
        //searchQuery.setGetTotalCount(true);// Specify the setGetTotalCount parameter to true to return the total number of rows that match the query condition. 
    
        SearchRequest searchRequest = new SearchRequest("sampleTable", "sampleSearchIndex", searchQuery);
        // You can set the columnsToGet parameter to specify whether to return all columns or only specified columns. By default, if this parameter is not set, only the primary columns are returned. 
        //SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
        //columnsToGet.setReturnAll(true); // Set the setReturnAll parameter to return all columns. 
        //columnsToGet.setColumns(Arrays.asList("ColName1","ColName2")); // Set the setColumns parameter to return specified columns. 
        //searchRequest.setColumnsToGet(columnsToGet);
    
        SearchResponse resp = client.search(searchRequest);
        //System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched columns but not returned columns. 
        System.out.println("Row: " + resp.getRows());
    }
    ```

-   Example 2

    The following code provides an example on how to query data that matches the "tablestore" condition in the col\_nested.nested\_2.nested\_2\_2 column. In this example, the nested field named col\_nested includes two subcolumns: nested\_1 and nested\_2. The nested\_2 subcolumn includes two columns: nested\_2\_1 and nested\_2\_2.

    ```
    private static void nestedQuery(SyncClient client) {
        SearchQuery searchQuery = new SearchQuery();
        NestedQuery nestedQuery = new NestedQuery(); // Set the query type to NestedQuery. 
        nestedQuery.setPath("col_nested.nested_2"); // Set the path of the nested column, which is the parent path of the field to query. 
        TermQuery termQuery = new TermQuery(); // Create the child query of the NestedQuery object. 
        termQuery.setFieldName("col_nested.nested_2.nested_2_2"); // Set the path of the column, which is the full path of the field to query. 
        termQuery.setTerm(ColumnValue.fromString("tablestore")); // Specify the value that you want to query. 
        nestedQuery.setQuery(termQuery);
        nestedQuery.setScoreMode(ScoreMode.None);
        searchQuery.setQuery(nestedQuery);
        //searchQuery.setGetTotalCount(true);// Set the setGetTotalCount parameter to true to return the total number of rows that match the query condition. 
    
        SearchRequest searchRequest = new SearchRequest("sampleTable", "sampleSearchIndex", searchQuery);
        // You can set the columnsToGet parameter to specify whether to return all columns or only specified columns. By default, if this parameter is not set, only the primary columns are returned. 
        //SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
        //columnsToGet.setReturnAll(true); // Set the setReturnAll parameter to return all columns. 
        //columnsToGet.setColumns(Arrays.asList("ColName1","ColName2")); // Set the setColumns parameter to return specified columns. 
        //searchRequest.setColumnsToGet(columnsToGet);
    
        SearchResponse resp = client.search(searchRequest);
        //System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched columns but not returned columns. 
        System.out.println("Row: " + resp.getRows());
    }
    ```


