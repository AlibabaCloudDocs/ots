# Create search indexes

You can call the CreateSearchIndex operation to create one or more search indexes for a table.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/.NET SDK/Initialization.md).
-   A table is created for which TimeToLive is set to -1 and MaxVersions is set to 1.

## Parameters

The following table describes the parameters that you can specify when you create a search index, including TableName, IndexName, and IndexSchema. You can configure FieldSchemas, IndexSetting, and IndexSort for IndexSchema. The following table provides detailed information about the parameters.

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table.|
|IndexName|The name of the search index.|
|FieldSchemas|The list of field schemas. You can configure the following parameters for each field schema:-   FieldName: required. This parameter specifies the name of the field in the search index, which is also called the column name. Type: String.

A field in a search index can be a primary key column or an attribute column.

-   FieldType: required. This parameter specifies the type of the field. Use FieldType.XXX to set the type. For more information, see [Data types of column values](/intl.en-US/Function Introduction/Search Index/Operations/Overview.md).
-   Array: optional. This parameter specifies whether the value is an array. Type: Boolean.

A value of true indicates that the column is an array. Data written to the column must be a JSON array. Example: \["a","b","c"\].

Nested values are an array. If you set FieldType to Nested, ignore the Array parameter.

-   Index: optional. This parameter specifies whether to create an index for the column. Type: Boolean.

Default value: true. A value of true indicates that Tablestore creates an inverted index or spatial index for the column. A value of false indicates that Tablestore does not create indexes for the column.

-   IndexOptions: optional. This parameter specifies whether to store terms such as position and offset in an inverted list. Use the default value in most cases.
-   Analyzer: optional. This parameter specifies the type of the tokenizer. If the field type is set to Text, you can set this parameter. By default, the type of the tokenizer is set to single-word tokenization. For more information, see [Tokenization](/intl.en-US/Function Introduction/Search Index/Operations/Tokenization.md).
-   EnableSortAndAgg: optional. This parameter specifies whether to enable the sorting and aggregation features. Type: Boolean.

Only fields with the EnableSortAndAgg attribute set to true can be sorted. For more information, see [Sorting and pagination](/intl.en-US/SDK Reference/Go SDK/Search Index/Sorting and pagination.md).

-   Store: optional. This parameter specifies whether to store the value of the field in the search index. Type: Boolean.

A value of true indicates that the value of the field is stored in the search index. You can read the value of the field directly from the search index without the need to query the table. This optimizes query performance. |
|IndexSetting|The settings of the search index, including RoutingFields.RoutingFields: optional. You use this parameter to customize routing fields. You can specify that part of primary key columns are used as routing fields. Tablestore distributes data that is written to a search index to different partitions based on the specified routing fields. The data with the same routing field values is distributed to the same data partition. |
|IndexSort|The presorting settings of the search index, including Sorters. If IndexSort is not set, data is sorted by primary key.**Note:** Search indexes of the NEST type do not support IndexSort.

Sorters: required. This parameter specifies the presorting method for the search index. PrimaryKeySort and FieldSort are supported. For more information, see [Sorting and pagination](/intl.en-US/SDK Reference/Go SDK/Search Index/Sorting and pagination.md).

-   PrimaryKeySort: Data is sorted by primary key. You can configure the following parameter for PrimaryKeySort:

Order: the order for sorting. You can set this parameter to the ascending or descending order. Default value: DataModel.Search.Sort.SortOrder.ASC.

-   FieldSort: Data is sorted based on the field value. You can configure the following parameters for FieldSort:

Only fields for which indexes are created and the sorting and aggregation features are enabled can be presorted.

    -   FieldName: the name of the field for sorting.
    -   Order: the order for sorting. You can set this parameter to the ascending or descending order. Default value: DataModel.Search.Sort.SortOrder.ASC.
    -   Mode: the sorting method used when the field has multiple values. |

## Examples

-   Example 1:

    Creates a search index.

    ```
    /// <summary>
    /// Create a search index that contains the Keyword_type_col, Long_type_col, and Text_type_col attribute columns. Set the type of data in Keyword_type_col to Keyword, in Long_type_col to Long, and in Text_type_col to Text.
    /// </summary>
    /// <param name="otsClient"></param>
    public static void CreateSearchIndex(OTSClient otsClient)
    {
        // Set the names of the table and search index.
        CreateSearchIndexRequest request = new CreateSearchIndexRequest(TableName, IndexName);
        List<FieldSchema> FieldSchemas = new List<FieldSchema>() {
            new FieldSchema(Keyword_type_col,FieldType.KEYWORD){ // Set the field name and field type.
                index =true, // Set index to true.
                EnableSortAndAgg =true // Set EnableSortAndAgg to true to enable the sorting and aggregation features.
            },
            new FieldSchema(Long_type_col,FieldType.LONG){ index=true,EnableSortAndAgg=true},
            new FieldSchema(Text_type_col,FieldType.TEXT){ index=true}
        };
        request.IndexSchame = new IndexSchema()
        {
            FieldSchemas = FieldSchemas
        };
        // Call a client to create the search index.
        CreateSearchIndexResponse response = otsClient.CreateSearchIndex(request);
        Console.WriteLine("Searchindex is created: " + IndexName);
    }
    ```

-   Example 2

    Specify IndexSort when you create a search index.

    ```
    /// <summary>
    /// Create a search index that contains the Keyword_type_col, Long_type_col, and Text_type_col attribute columns. Set the type of data in Keyword_type_col to Keyword, in Long_type_col to Long, and in Text_type_col to Text.
    /// </summary>
    /// <param name="otsClient"></param>
    public static void CreateSearchIndexWithIndexSort(OTSClient otsClient)
    {
        // Set the names of the table and search index.
        CreateSearchIndexRequest request = new CreateSearchIndexRequest(TableName, IndexName);
        List<FieldSchema> FieldSchemas = new List<FieldSchema>() {
            new FieldSchema(Keyword_type_col,FieldType.KEYWORD){ // Set the field name and field type.
                index =true, // Set index to true.
                EnableSortAndAgg =true // Set EnableSortAndAgg to true to enable the sorting and aggregation features.
            },
            new FieldSchema(Long_type_col,FieldType.LONG){ index=true,EnableSortAndAgg=true},
            new FieldSchema(Text_type_col,FieldType.TEXT){ index=true}
        };
        request.IndexSchame = new IndexSchema()
        {
            FieldSchemas = FieldSchemas,
            // Presort data by the Long_type_col column. You must index the Long_type_col column and enable the sorting and aggregation features.
            IndexSort = new DataModel.Search.Sort.Sort()
            {
                Sorters = new List<DataModel.Search.Sort.ISorter>
                {
                    new DataModel.Search.Sort.FieldSort(Long_type_col, DataModel.Search.Sort.SortOrder.ASC)
                }
            }
        };
    
        CreateSearchIndexResponse response = otsClient.CreateSearchIndex(request);
        Console.WriteLine("Searchindex is created: " + IndexName);
    }
    ```


