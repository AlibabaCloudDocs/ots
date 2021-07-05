# Create search indexes

You can call the CreateSearchIndex operation to create one or more search indexes for a table.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A table is created for which time\_to\_live is set to -1 and max\_versions is set to 1.

## Operations

```
/**
 * Create a search index.
 * @api
 *
 * @param [] $request
 *            The request parameters, including the table name and index configuration.
 * @return [] The response.
 * @throws OTSClientException The exception that is returned when a parameter error occurs or the Tablestore server returns a verification error.
 * @throws OTSServerException The exception that is returned when the Tablestore server returns an error.
 * @example "src/examples/CreateSearchIndex.php"
 */
public function createSearchIndex(array $request)
```

## Parameters

The following table describes the parameters that you can specify when you create a search index, including table\_name, index\_name, and schema. You can configure field\_schemas, index\_setting, and index\_sort for schema.

|Parameter|Description|
|---------|-----------|
|table\_name|The name of the table.|
|index\_name|The name of the search index.|
|field\_schemas|The list of field schemas. You can configure the following parameters for each field schema: -   field\_name: required. This parameter specifies the name of the field in the search index, which is also called the column name. Type: String.

A field in a search index can be a primary key column or an attribute column.

-   field\_type: required. This parameter specifies the type of the field. Use FieldType.XXX to set the type. For more information, see [Data types of column values](/intl.en-US/Function Introduction/Search Index/Operations/Overview.md).
-   is\_array: optional. This parameter specifies whether the value is an array. Type: Boolean.

A value of true indicates that the column is an array. Data written to the column must be a JSON array. Example: \["a","b","c"\].

Nested values are an array. If you set field\_type to Nested, ignore the is\_array parameter.

-   index: optional. This parameter specifies whether to create an index for the column. Type: Boolean.

A value of true indicates that Tablestore creates an inverted index or spatial index for the column. A value of false indicates that Tablestore does not create indexes for the column.

-   analyzer: optional. This parameter specifies the type of the analyzer. If the field type is set to Text, you can set this parameter. If the field type is not set, the type of the analyzer is set to the single-word tokenization. For more information, see [Tokenization](/intl.en-US/Function Introduction/Search Index/Operations/Tokenization.md).
-   enable\_sort\_and\_agg: optional. This parameter specifies whether to enable the sorting and aggregation features. Type: Boolean.

Only fields with the enable\_sort\_and\_agg parameter set to true can be sorted. For more information, see [Sorting and pagination](/intl.en-US/SDK Reference/PHP SDK/Search index/Sorting and pagination.md).

-   store: optional. This parameter specifies whether to store the value of the field in the search index. Type: Boolean. |
|index\_setting|The settings of the search index, including routing\_fields. routing\_fields: optional. You can use this parameter to customize routing fields. You can specify that part of primary key columns are used as routing fields. Tablestore distributes data that is written to a search index to different partitions based on the specified routing fields. The data with the same routing field values is distributed to the same data partition. |
|index\_sort|The presorting settings of the search index, including sorters. If index\_sort is not set, data is sorted by primary key.**Note:** Search indexes that contain Nested fields do not support IndexSort.

sorters: required. This parameter specifies the presorting method for the search index. PrimaryKeySort and FieldSort are supported. For more information, see [Sorting and pagination](/intl.en-US/SDK Reference/Python SDK/Search Index/Sorting and pagination.md).

-   PrimaryKeySort: Data is sorted by primary key. You can configure the following parameter for PrimaryKeySort:

order: the order for sorting. You can set this parameter to the ascending or descending order. Default value: SortOrderConst::SORT\_ORDER\_ASC.

-   FieldSort: Data is sorted based on the field value. You can configure the following parameters for FieldSort:

Only fields for which indexes are created and the sorting and aggregation features are enabled can be presorted.

    -   field\_name: the name of the field for sorting.
    -   order: the order for sorting. You can set this parameter to the ascending or descending order. Default value: SortOrderConst::SORT\_ORDER\_ASC.
    -   mode: the sorting method used when the field has multiple values. |

## Examples

```
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index',
    'schema' => array(
        'field_schemas' => array(
            array(
                'field_name' => 'keyword',
                'field_type' => FieldTypeConst::KEYWORD,
                'index' => true,
                'enable_sort_and_agg' => true,
                'store' => true,
                'is_array' => false
            ),
            array(
                'field_name' => 'text',
                'field_type' => FieldTypeConst::TEXT,
                'analyzer' => 'single_word',
                'index' => true,
                'enable_sort_and_agg' => false,
                'store' => true,
                'is_array' => false
            ),
            array(
                'field_name' => 'geo',
                'field_type' => FieldTypeConst::GEO_POINT,
                'index' => true,
                'enable_sort_and_agg' => true,
                'store' => true,
                'is_array' => false
            ),
            array(
                'field_name' => 'long',
                'field_type' => FieldTypeConst::LONG,
                'index' => true,
                'enable_sort_and_agg' => true,
                'store' => true,
                'is_array' => false
            ),
            array(
                'field_name' => 'double',
                'field_type' => FieldTypeConst::DOUBLE,
                'index' => true,
                'enable_sort_and_agg' => true,
                'store' => true,
                'is_array' => false
            ),
            array(
                'field_name' => 'boolean',
                'field_type' => FieldTypeConst::BOOLEAN,
                'index' => true,
                'enable_sort_and_agg' => false,
                'store' => true,
                'is_array' => false
            ),
            array(
                'field_name' => 'array',
                'field_type' => FieldTypeConst::KEYWORD,
                'index' => true,
                'enable_sort_and_agg' => false,
                'store' => true,
                'is_array' => true
            ),
            array(
                'field_name' => 'nested',
                'field_type' => FieldTypeConst::NESTED,
                'index' => false,
                'enable_sort_and_agg' => false,
                'store' => false,
                'field_schemas' => array(
                    array(
                        'field_name' => 'nested_keyword',
                        'field_type' => FieldTypeConst::KEYWORD,
                        'index' => false,
                        'enable_sort_and_agg' => false,
                        'store' => false,
                        'is_array' => false
                    )
                )
            ),
        ),
        'index_setting' => array(
            'routing_fields' => array("pk1")
        ),
//        "index_sort" => array(// Search indexes that contain NEST fields do not support IndexSort.
//            array(
//                'field_sort' => array(
//                    'field_name' => 'keyword',
//                    'order' => SortOrderConst::SORT_ORDER_ASC,
//                    'mode' => SortModeConst::SORT_MODE_AVG,
//                )
//            ),
//            array(
//                'pk_sort' => array(
//                    'order' => SortOrderConst::SORT_ORDER_ASC
//                )
//            ),
//        )
    )
);
$response = $otsClient->createSearchIndex();
```

