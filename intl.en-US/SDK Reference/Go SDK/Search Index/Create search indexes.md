# Create search indexes

You can call the CreateSearchIndex operation to create one or more search indexes for a table.

## Prerequisites

-   The Client instance is initialized. For more information, see [Initialize a Tablestore client](/intl.en-US/SDK Reference/Go SDK/Initialize a Tablestore client.md).
-   A table is created for which TimeToLive is set to -1 and MaxVersions is set to 1.

## Parameters

The following table describes the parameters that you can specify when you create a search index, including TableName, IndexName, and IndexSchema. You can configure FieldSchemas, IndexSetting, and IndexSort for IndexSchema.

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

A value of true indicates that Tablestore creates an inverted index or spatial index for the column. A value of false indicates that Tablestore does not create indexes for the column.

-   IndexOptions: optional. This parameter specifies whether to store terms such as position and offset in an inverted list. Use the default value in most cases.
-   Analyzer: optional. This parameter specifies the type of the analyzer. If the field type is set to Text, you can set this parameter. If the field type is not set, the type of the analyzer is set to the single-word tokenization. For more information, see [Tokenization](/intl.en-US/Function Introduction/Search Index/Operations/Tokenization.md).
-   EnableSortAndAgg: optional. This parameter specifies whether to enable the sorting and aggregation features. Type: Boolean.

Only fields with the EnableSortAndAgg parameter set to true can be sorted. For more information, see [Sorting and pagination](/intl.en-US/SDK Reference/Go SDK/Search Index/Sorting and pagination.md).

-   Store: optional. This parameter specifies whether to store the value of the field in the search index. Type: Boolean.

A value of true indicates that the value of the field is stored in the search index. You can read the value of the field directly from the search index without the need to query the table. This optimizes query performance. |
|IndexSetting|The settings of the search index, including RoutingFields.RoutingFields: optional. You use this parameter to customize routing fields. You can specify that part of primary key columns are used as routing fields. Tablestore distributes data that is written to a search index to different partitions based on the specified routing fields. The data with the same routing field values is distributed to the same data partition. |
|IndexSort|The presorting settings of the search index, including Sorters. If IndexSort is not set, data is sorted by primary key.**Note:** Search indexes that contain Nested fields do not support IndexSort.

Sorters: required. This parameter specifies the presorting method for the search index. PrimaryKeySort and FieldSort are supported. For more information, see [Sorting and pagination](/intl.en-US/SDK Reference/Go SDK/Search Index/Sorting and pagination.md).

-   PrimaryKeySort: Data is sorted by primary key. You can configure the following parameter for PrimaryKeySort:

Order: the order for sorting. You can set this parameter to the ascending or descending order. Default value: SortOrder\_ASC.

-   FieldSort: Data is sorted based on the field value. You can configure the following parameters for FieldSort:

Only fields for which indexes are created and the sorting and aggregation features are enabled can be presorted.

    -   FieldName: the name of the field for sorting.
    -   Order: the order for sorting. You can set this parameter to the ascending or descending order. Default value: SortOrder\_ASC.
    -   Mode: the sorting method used when the field has multiple values. |

## Examples

```
/**
 *Create a search index that contains the Col_Keyword and Col_Long columns. Set the type of data in Col_Keyword to Keyword. Set the type of data in Col_Long to Long.
 */
func CreateSearchIndex(client *tablestore.TableStoreClient, tableName string, indexName string) {
    request := &tablestore.CreateSearchIndexRequest{}
    request.TableName = tableName // Set the name of the table.
    request.IndexName = indexName // Set the name of the search index.

    schemas := []*tablestore.FieldSchema{}
    field1 := &tablestore.FieldSchema{
        FieldName: proto.String("Col_Keyword"), // Set the field name by calling the proto.String method. This method is used to request a string pointer.
        FieldType: tablestore.FieldType_KEYWORD, // Set the field type.
        Index:     proto.Bool(true), // Set Index to true.
        EnableSortAndAgg: proto.Bool(true), // Set EnableSortAndAgg to true to enable the sorting and aggregation features.
    }
    field2 := &tablestore.FieldSchema{
        FieldName: proto.String("Col_Long"),
        FieldType: tablestore.FieldType_LONG,
        Index:     proto.Bool(true),
        EnableSortAndAgg: proto.Bool(true),
    }
    schemas = append(schemas, field1, field2)

    request.IndexSchema = &tablestore.IndexSchema{
        FieldSchemas: schemas, // Set the fields included in the search index.
    }
    resp, err := client.CreateSearchIndex(request) // Call a client to create the search index.
    if err != nil {
        fmt.Println("error :", err)
        return
    }
    fmt.Println("CreateSearchIndex finished, requestId:", resp.ResponseInfo.RequestId)
}
```

