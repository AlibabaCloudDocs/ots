# Create search indexes

You can call the CreateSearchIndex operation to create one or more search indexes for a table.

## Prerequisites

-   Client is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Node.js SDK/Initialization.md).
-   A table is created for which timeToLive is set to -1 and maxVersions is set to 1.

## Parameters

The following table describes the parameters that you can specify when you create a search index, including tableName, indexName, and schema. You can configure fieldSchemas, indexSetting, and indexSort for schema. The following table provides detailed information about the parameters.

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table.|
|indexName|The name of the search index.|
|fieldSchemas|The list of field schemas. You can configure the following parameters for each field schema:-   fieldName: required. This parameter specifies the name of the field, which is also called the column name. Type: String.

A field in a search index can be a primary key column or attribute column.

-   fieldType: required. This parameter specifies the type of the field. Use FieldType.XXX to set the type. For more information, see [Data types of column values](/intl.en-US/Function Introduction/Search Index/Operations/Overview.md).
-   index: optional. This parameter specifies whether to enable the index. Type: Boolean.

Default value: true. A value of true indicates that Tablestore creates an inverted index or spatial index for the column. A value of false indicates that Tablestore does not create indexes for the column.

-   indexOptions: optional. This parameter specifies whether to store terms such as position and offset in an inverted list. Use the default value in most cases.
-   analyzer: optional. This parameter specifies the type of the analyzer. If the field type is set to Text, you can set this parameter. By default, the type of the analyzer is set to the single-word tokenization. For more information about analyzers, see [Tokenization](/intl.en-US/Function Introduction/Search Index/Operations/Tokenization.md).
-   enableSortAndAgg: optional. This parameter specifies whether to enable the sorting and aggregation features. Type: Boolean.

Only fields with the enableSortAndAgg attribute set to true can be sorted. For more information about sorting, see [Sorting and pagination](/intl.en-US/SDK Reference/Node.js SDK/Search Index/Sorting and pagination.md).

-   store: optional. This parameter specifies whether to store the value of the field in the search index. Type: Boolean.

A value of true indicates that you can read the value of the field directly from the search index without the need to query the table. This optimizes query performance.

-   isAnArray: optional. This parameter specifies whether the value is an array. Type: Boolean.

A value of true indicates that the column is an array. Data written to the column must be a JSON array. Example: \["a","b","c"\].

Nested values are an array. If you set fieldType to Nested, ignore the isAnArray parameter.

-   fieldSchemas: optional. This parameter specifies the list of field schemas for subfields. If the column is a NEST column, you must specify this parameter to configure the index types of subcolumns in the NEST column. |
|indexSetting|The settings of the search index, including routingFields.routingFields: optional. You can use this parameter to customize routing fields. You can specify that part of primary key columns are used as routing fields. Tablestore distributes data that is written to a search index to different partitions based on the specified routing fields. The data with the same routing field values is distributed to the same data partition. |
|indexSort|The presorting settings of the search index, including sorters. By default, data is sorted by primary key if indexSort is not set.**Note:** Search indexes that contain NEST fields do not support IndexSort.

sorters: required. This parameter specifies the presorting method for the search index. PrimaryKeySort and FieldSort are supported. For more information, see [Sorting and pagination](/intl.en-US/SDK Reference/Node.js SDK/Search Index/Sorting and pagination.md).

-   PrimaryKeySort: Data is sorted by primary key. You can configure the following parameter for PrimaryKeySort:

order: the order for sorting. You can set this parameter to the ascending or descending order. Default value: TableStore.SortOrder.SORT\_ORDER\_ASC.

-   FieldSort: Data is sorted based on the field value. You can configure the following parameters for FieldSort:

Only fields for which indexes are created and sorting and aggregation are enabled can be presorted.

    -   fieldName: the name of the field for sorting.
    -   order: the order for sorting. You can set this parameter to the ascending or descending order. Default value: TableStore.SortOrder.SORT\_ORDER\_ASC.
    -   mode: the sorting method used when the field has multiple values. |

## Examples

```
/**
 *Create a search index that contains Col_Keyword, Col_Long, Col_Text, and Col_Nested.
 *Set the type of data in Col_Keyword to Keyword, in Col_Long to Long, in Col_Text to Text, and in Col_Nested to Nested.
 */
client.createSearchIndex({
    tableName: TABLE_NAME, // Set the table name.
    indexName: INDEX_NAME, // Set the search index name.
    schema: {
        fieldSchemas: [
            {
                fieldName: "Col_Keyword",
                fieldType: TableStore.FieldType.KEYWORD, // Set the name and type of the field.
                index: true, // Set index to true.
                enableSortAndAgg: true, // Set enableSortAndAgg to true to enable the sorting and aggregation features.
                store: false,
                isAnArray: false
            },
            {
                fieldName: "Col_Long",
                fieldType: TableStore.FieldType.LONG,
                index: true,
                enableSortAndAgg: true,
                store: true,
                isAnArray: false
            },
            {
                fieldName: "Col_Text",
                fieldType: TableStore.FieldType.TEXT,
                index: true,
                enableSortAndAgg: false,
                store: true,
                isAnArray: false,
                analyzer: "single_word"
            },
            // {
            //     fieldName: "Col_Nested",
            //     fieldType: TableStore.FieldType.NESTED,
            //     index: false,
            //     enableSortAndAgg: false,
            //     store: false,
            //     fieldSchemas: [ // Set the subfield index for nested fields.
            //         {
            //             fieldName: "Sub_Col_KeyWord",
            //             fieldType: TableStore.FieldType.KEYWORD,
            //             index: true,
            //             enableSortAndAgg: true,
            //             store: false
            //         },
            //         {
            //             fieldName: "Sub_Col_Long",
            //             fieldType: TableStore.FieldType.LONG,
            //             index: true,
            //             enableSortAndAgg: true,
            //             store: false
            //         }
            //     ]
            // }
        ],
        indexSetting: { // Configure the search index.
            "routingFields": ["Pk_Keyword"], // Only primary key columns can be set as routing fields.
            "routingPartitionSize": null
        },
        indexSort: {// Search indexes that contain NEST fields do not support indexSort.
            sorters: [
                // { // If indexSort is not set, the default value is PrimaryKeySort. Data is sorted in ascending order by default.
                //     primaryKeySort: {
                //         order: TableStore.SortOrder.SORT_ORDER_ASC
                //     }
                // },
                {
                    fieldSort: {
                        fieldName: "Col_Keyword",
                        order: TableStore.SortOrder.SORT_ORDER_DESC // Set the index sorting order.
                    }
                }
            ]
        }
    }
}, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', data);
});
```

