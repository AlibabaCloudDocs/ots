# Collapse \(distinct\)

You can use the collapse feature to collapse the result set based on a specified column when the results of a query contain large amounts of data of a specific type. Data of the corresponding type is displayed only once in the query results to ensure diversity in the result types.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Java SDK/Search index/Create search indexes.md).

## Usage notes

-   If you use the collapse feature, you must implement pagination by specifying offset and limit instead of Token.
-   If you aggregate and collapse a result set at the same time, the result set is aggregated before it is collapsed.
-   If you collapse the query results, the total number of groups of the returned results is determined by the sum of the offset and limit values. A maximum of 10,000 groups can be returned.
-   The total number of rows returned in the results indicates the number of returned rows before you use the collapse feature. After the result set is collapsed, the total number of the returned groups cannot be queried.

## Parameters

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table.|
|indexName|The name of the search index.|
|query|The query type.|
|collapse|The collapse parameter, including the fieldName field.fieldName: the name of the column based on which the result set is collapsed. Only columns whose values are of the INTEGER, FLOATING-POINT or KEYWORD type are supported. |
|limit|The position from which the current query starts.|
|offset|The maximum number of rows that the current query returns.To query only the number of matched rows without the data, you can set limit to 0. In this case, Tablestore returns the number of matched rows without table data. |

## Examples

```
private static void UseCollapse(SyncClient client){
        SearchQuery searchQuery = new SearchQuery(); // Specify the query conditions.
        MatchQuery matchQuery = new MatchQuery();
        matchQuery.setFieldName("user_id");
        matchQuery.setText("00002");

        searchQuery.setQuery(matchQuery);
        Collapse collapse = new Collapse("product_name"); // Collapse the result set based on the product_name values.

        searchQuery.setGetTotalCount(true);
        searchQuery.setCollapse(collapse);
        searchQuery.setOffset(1000);
        searchQuery.setLimit(20);
        SearchRequest searchRequest = new SearchRequest("order","order_index",searchQuery);// Specify the names of the table and the search index.

        SearchResponse response = client.search(searchRequest);  
        System.out.println(response.getTotalCount());    
        System.out.println(response.getRows().size()); // Display the product names based on the product_name values.
    }
```
