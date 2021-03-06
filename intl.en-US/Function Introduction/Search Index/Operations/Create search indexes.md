# Create search indexes

After you create a search index for a data table, you can query fields only in the search index when you use the search index. You can create multiple search indexes for a data table.

## Optimal method to create search indexes

The actual query requirements determine the number of search indexes created for a table.

If you have a data table that contains the following fields: id, name, age, city, and sex. To query data by name, age, or city, you can use one of the following methods to create a search index:

-   Method 1: Create a search index for each field

    If you use this method, you must create the following search indexes: name\_index, age\_index, and city\_index.

    -   To query students by city, use city\_index. To query students by age, use age\_index.
    -   However, this method does not work if you want to query students who are younger than 12 years old and live in Chengdu.
    The implementation of the method is similar to that of the global secondary index feature. However, this method is not cost-effective. To create a search index, we recommend that you do not use this method.

-   Method 2: Create one search index for multiple fields

    If you use this method, a search index named student\_index contains the following fields: name, age, and city.

    -   To query students by city, query the city field in student\_index. To query students by age, query the age field in student\_index.
    -   To query students who are younger than 12 years old and live in Chengdu, query the age and city fields in student\_index.
    This method provides more features at a lower cost. To create a search index, we recommend that you use this method.


## Limits

-   Timeliness of index creation

    It takes a few seconds to use a search index after the search index is created. In the creation process, you can write data to the data table. However, metadata queries of the search index and data queries by using the search index are affected.

-   Limits on the number of resources

    For more information, see [Search index limits](/intl.en-US/Function Introduction/Limits/Search index limits.md).


## Operations

You can call the CreateSearchIndex operation to create a search index.

## Use Tablestore SDKs

You can use the following SDKs to create a search index:

-   Tablestore SDK for Java: [Create search indexes](/intl.en-US/SDK Reference/Java SDK/Search Index/Create search indexes.md)
-   Tablestore SDK for Go: [Create search indexes](/intl.en-US/SDK Reference/Go SDK/Search Index/Create search indexes.md)
-   Tablestore SDK for Python: [Create search indexes](/intl.en-US/SDK Reference/Python SDK/Search Index/Create search indexes.md)
-   Tablestore SDK for Node.js: [Create search indexes](/intl.en-US/SDK Reference/Node.js SDK/Search Index/Create search indexes.md)
-   Tablestore SDK for .NET: [Create search indexes](/intl.en-US/SDK Reference/.NET SDK/Search Index/Create search indexes.md)
-   Tablestore SDK for PHP SDK: [Create search indexes](/intl.en-US/SDK Reference/PHP SDK/Search index/Create search indexes.md)

## Parameters

When you create a search index, you must specify tableName, indexName, and indexSchema. You can configure fieldSchemas, indexSetting, and indexSort for indexSchema. The following table describes the parameters.

|Parameter|Description|
|---------|-----------|
|tableName|The name of the data table.|
|indexName|The name of the search index.|
|fieldSchemas|The list of field schemas. You can configure the following parameters for each field schema:-   fieldName: required. This parameter specifies the name of the field in the search index, which is also called the column name. Type: String.

A field in a search index can be a primary key column or attribute column.

-   fieldType: required. This parameter specifies the type of the field. Use FieldType.XXX to set the type. For more information, see [Data types of column values](/intl.en-US/Function Introduction/Search Index/Operations/Overview.md).
-   array: optional. This parameter specifies whether the value is an array. Type: Boolean.

A value of true indicates that the column is an array. Data written to the column must be a JSON array. Example: \["a","b","c"\].

Nested values are an array. If you set fieldType to Nested, you do not need to set the array parameter.

-   index: optional. This parameter specifies whether to enable the index. Type: Boolean.

A value of true indicates that Tablestore creates an inverted index or spatial index for the column. A value of false indicates that Tablestore does not create indexes for the column.

-   analyzer: optional. This parameter specifies the type of the analyzer. If the field type is set to Text, you can set this parameter. By default, the type of the analyzer is set to the single-word tokenization. For more information about analyzers, see [Tokenization](/intl.en-US/Function Introduction/Search Index/Operations/Tokenization.md).
-   enableSortAndAgg: optional. This parameter specifies whether to enable the sorting and aggregation features. Type: Boolean.

Only fields with the enableSortAndAgg attribute set to true can be sorted. For more information about sorting, see [Sorting and pagination](/intl.en-US/Function Introduction/Search Index/Operations/Sorting and pagination.md).

-   store: optional. This parameter specifies whether to store the value of the field in the search index. Type: Boolean.

A value of true indicates that you can read the value of the field directly from the search index without the need to query the data table. This optimizes query performance. |
|indexSetting|The settings of the search index, including routingFields. routingFields: optional. You can use this parameter to customize routing fields. You can specify that part of primary key columns are used as routing fields. Tablestore distributes data that is written to a search index to different partitions based on the specified routing fields. The data with the same routing field values is distributed to the same data partition. |
|indexSort|The presorting settings of the search index, including sorters. By default, data is sorted by primary key if indexSort is not set. **Note:** Search indexes that contain nested fields do not support indexSort.

sorters: required. This parameter specifies the presorting method for the search index. PrimaryKeySort and FieldSort are supported. For more information, see [Sorting and pagination](/intl.en-US/Function Introduction/Search Index/Operations/Sorting and pagination.md).

-   PrimaryKeySort: Data is sorted by primary key. You can configure the following parameter for PrimaryKeySort:

order: the order for sorting. You can set this parameter to the ascending or descending order. Default setting: SortOrder.ASC.

-   FieldSort: Data is sorted based on the field value. You can configure the following parameters for FieldSort:

Only fields for which indexes are created and sorting and aggregation are enabled can be presorted.

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


