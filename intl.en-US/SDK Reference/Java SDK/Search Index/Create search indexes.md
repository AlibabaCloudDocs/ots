# Create search indexes

You can call the CreateSearchIndex operation to create one or more search indexes for a table.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialization.md).
-   A table is created for which timeToLive is set to -1 and maxVersions is set to 1.

## Parameters

The following table describes the parameters that you can specify when you create a search index, including tableName, indexName, and indexSchema. You can configure fieldSchemas, indexSetting, and indexSort for indexSchema.

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table.|
|indexName|The name of the search index.|
|fieldSchemas|The list of field schemas. You can configure the following parameters for each field schema:-   fieldName: required. This parameter specifies the name of the field in the search index, which is also called the column name. Type: String.

A field in a search index can be a primary key column or an attribute column.

-   fieldType: required. This parameter specifies the type of the field. Use FieldType.XXX to set the type. For more information, see [Data types of column values](/intl.en-US/Function Introduction/Search Index/Operations/Overview.md).
-   array: optional. This parameter specifies whether the value is an array. Type: Boolean.

A value of true indicates that the column is an array. Data written to the column must be a JSON array. Example: \["a","b","c"\].

Nested values are an array. If you set fieldType to Nested, ignore the array parameter.

-   index: optional. This parameter specifies whether to create an index for the column. Type: Boolean.

A value of true indicates that Tablestore creates an inverted index or spatial index for the column. A value of false indicates that Tablestore does not create indexes for the column.

-   analyzer: optional. This parameter specifies the type of the analyzer. If the field type is set to Text, you can set this parameter. If the field type is not set, the type of the analyzer is set to the single-word tokenization. For more information, see [Tokenization](/intl.en-US/Function Introduction/Search Index/Operations/Tokenization.md).
-   enableSortAndAgg: optional. This parameter specifies whether to enable the sorting and aggregation features. Type: Boolean.

Only fields with the enableSortAndAgg parameter set to true can be sorted. For more information, see [Sorting and pagination](/intl.en-US/SDK Reference/Java SDK/Search Index/Sorting and pagination.md).

-   store: optional. This parameter specifies whether to store the value of the field in the search index. Type: Boolean.

A value of true indicates that the value of the field is stored in the search index. You can read the value of the field directly from the search index without the need to query the table. This optimizes query performance. |
|indexSetting|The settings of the search index, including routingFields.routingFields: optional. You use this parameter to customize routing fields. You can specify that part of primary key columns are used as routing fields. Tablestore distributes data that is written to a search index to different partitions based on the specified routing fields. The data with the same routing field values is distributed to the same data partition. |
|indexSort|The presorting settings of the search index, including sorters. If indexSort is not set, data is sorted by primary key.**Note:** Search indexes that contain Nested fields do not support IndexSort.

sorters: required. This parameter specifies the presorting method for the search index. PrimaryKeySort and FieldSort are supported. For more information, see [Sorting and pagination](/intl.en-US/SDK Reference/Java SDK/Search Index/Sorting and pagination.md).

-   PrimaryKeySort: Data is sorted by primary key. You can configure the following parameter for PrimaryKeySort:

order: the order for sorting. You can set this parameter to the ascending or descending order. Default value: SortOrder.ASC.

-   FieldSort: Data is sorted based on the field value. You can configure the following parameters for FieldSort:

Only fields for which indexes are created and the sorting and aggregation features are enabled can be presorted.

    -   fieldName: the name of the field for sorting.
    -   order: the order for sorting. You can set this parameter to the ascending or descending order. Default value: SortOrder.ASC.
    -   mode: the sorting method used when the field has multiple values. |

## Examples

-   Example 1

    Create a search index that consists of the Col\_Keyword and Col\_Long columns. Set the type of data in Col\_Keyword to String and Col\_Long to Long.

    ```
    
    private static void createSearchIndex(SyncClient client) {
        CreateSearchIndexRequest request = new CreateSearchIndexRequest();
        request.setTableName(TABLE_NAME); // Set the name of the data table. 
        request.setIndexName(INDEX_NAME); // Set the name of the search index. 
        IndexSchema indexSchema = new IndexSchema();
        indexSchema.setFieldSchemas(Arrays.asList(
                new FieldSchema("Col_Keyword", FieldType.KEYWORD) // Set the field name and field type. 
                        .setIndex(true) // Set the parameter to true to enable the index. 
                        .setEnableSortAndAgg(true), // Set the parameter to true to enable sorting and aggregation. 
                new FieldSchema("Col_Long", FieldType.LONG)
                        .setIndex(true)
                        .setEnableSortAndAgg(true)));
        request.setIndexSchema(indexSchema);
        client.createSearchIndex(request); // Call client to create the search index. 
    }
    ```

-   Example 2

    Specify IndexSort when you create a search index.

    ```
    private static void createSearchIndexWithIndexSort(SyncClient client) {
        CreateSearchIndexRequest request = new CreateSearchIndexRequest();
        request.setTableName(TABLE_NAME);
        request.setIndexName(INDEX_NAME);
        IndexSchema indexSchema = new IndexSchema();
        indexSchema.setFieldSchemas(Arrays.asList(
                new FieldSchema("Col_Keyword", FieldType.KEYWORD).setIndex(true).setEnableSortAndAgg(true),
                new FieldSchema("Col_Long", FieldType.LONG).setIndex(true).setEnableSortAndAgg(true),
                new FieldSchema("Col_Text", FieldType.TEXT).setIndex(true),
                new FieldSchema("Timestamp", FieldType.LONG).setIndex(true).setEnableSortAndAgg(true)));
        // Presort data by the Timestamp column. You must create a search index and enable sorting and aggregation for the Timestamp column. 
        indexSchema.setIndexSort(new Sort(
                Arrays.<Sort.Sorter>asList(new FieldSort("Timestamp", SortOrder.ASC))));
        request.setIndexSchema(indexSchema);
        client.createSearchIndex(request);
    }
    ```


