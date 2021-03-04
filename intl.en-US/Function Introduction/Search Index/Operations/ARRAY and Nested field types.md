# ARRAY and Nested field types

Search index provides the following special field types in addition to the basic field types such as LONG, DOUBLE, BOOLEAN, KEYWORD, TEXT, and GEOPOINT:

## ARRAY

ARRAY is a type that can be combined with basic field types such as LONG, DOUBLE, BOOLEAN, KEYWORD, TEXT, and GEOPOINT. For example, the combination of LONG with ARRAY is used to specify arrays of the LONG INTEGER type. LONG ARRAY fields can contain multiple long integers. If a query matches a component of an array, the corresponding row is returned.

The following table describes arrays combined with basic field types.

|ARRAY|Description|
|-----|-----------|
|Long Array|An array of long integers. Example: \[1000, 4, 5555\].|
|Boolean Array|An array of BOOLEAN values. Example: \[true, false\].|
|Double Array|An array of double-precision floating-point numbers. Example: \[3.1415926, 0.99\].|
|Keyword Array|An array of strings in the JSON ARRAY format. Example: \[\\"Hangzhou\\", \\"Xi'an\\"\].|
|Text Array|An array of text in the JSON ARRAY format. Example: \[\\"Hangzhou\\", \\"Xi'an\\"\].TEXT JSON arrays are not commonly used. |
|GeoPoint Array|An array of latitude and longitude coordinate pairs. Example: \[\\"34.2, 43.0\\", \\"21.4, 45.2\\"\].|

The ARRAY type is supported only in search indexes. Therefore, when the type of an index field involves ARRAY, the field in the table must be of the STRING type. A basic data type such as LONG or DOUBLE in the search index remains the same as that in the table. If a price field is of the DOUBLE ARRAY type, the field type in the table must be STRING, and the field type must be DOUBLE and isArray must be set to true in the search index.

## Nested

Nested indicates nested documents. Nested documents are used when a row of data \(document\) contains multiple child rows \(child documents\). Multiple child documents are stored in a Nested field. You must specify the schema of child rows in the Nested field. The schema must include the fields of the child rows and the property of each field. Nested is similar to ARRAY. However, Nested supports more features.

Nested fields are written as strings in JSON arrays to tables.

**Note:** Even when a field contains a single child row, the written strings must be JSON arrays.

-   Example of single-level Nested fields

    You can create single-level Nested fields in the console or by using Tablestore SDKs.

    This section provides an example on how to create a single-level Nested field by using Tablestore SDK for Java. A Nested field named tags is used in this example. Each child row contains two fields, as shown in the following figure.

    ![fig_sample](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0883284161/p217454.png)

    -   Field name: tagName. Field type: KEYWORD.
    -   Field name: score. Field type: DOUBLE.
    The following data samples are written to the table: `[{"tagName":"tag1", "score":0.8}, {"tagName":"tag2", "score":0.2}]`.

    ```
    // Specify the FieldSchema class for the child rows.
    List<FieldSchema> subFieldSchemas = new ArrayList<FieldSchema>();
    subFieldSchemas.add(new FieldSchema("tagName", FieldType.KEYWORD)
        .setIndex(true).setEnableSortAndAgg(true));
    subFieldSchemas.add(new FieldSchema("score", FieldType.DOUBLE)
        .setIndex(true).setEnableSortAndAgg(true));
    
    // Specify that FieldSchema of the child rows is used as subfieldSchemas of the Nested field.
    FieldSchema nestedFieldSchema = new FieldSchema("tags", FieldType.NESTED)
        .setSubFieldSchemas(subFieldSchemas);
    ```

-   Example of multiple-level Nested fields

    You can create multiple-level Nested fields only by using Tablestore SDKs.

    This section provides an example on how to create a multiple-level Nested field by using Tablestore SDK for Java. A Nested field named user is used in this example. Each child row contains three fields of different basic field types and one Nested field.

    -   Field name: name. Field type: KEYWORD.
    -   Field name: age. Field type: LONG.
    -   Field name: phone. Field type: KEYWORD.
    -   Nested field name: address. Names of the fields contained in each child row: province, city, and street. All fields contained in each child row are of the KEYWORD type.
    The following data sample is written to the table: `[ {"name":"Zhangsan","age":20,"phone":"15600000000","address":[{"province":"Zhejiang Province","city":"Hangzhou City","street":"No. 1201, Xingfu Community, Yangguang Avenue"}]}]`

    ```
    // Construct FieldSchema for each child row of the address Nested field. Each child row contains three fields. The path specified by user.address can be used to query data of fields in a child row.
    List<FieldSchema> addressSubFiledSchemas = new ArrayList<>();
    addressSubFiledSchemas.add(new FieldSchema("province",FieldType.KEYWORD));
    addressSubFiledSchemas.add(new FieldSchema("city",FieldType.KEYWORD));
    addressSubFiledSchemas.add(new FieldSchema("street",FieldType.KEYWORD));
    
    // Construct FieldSchema for each child row of the user Nested field. Each child row contains three fields of different basic field types and one Nested field named address. The path specified by user can be used to query data of fields in a child row.
    List<FieldSchema> subFieldSchemas = new ArrayList<>();
    subFieldSchemas.add(new FieldSchema("name",FieldType.KEYWORD));
    subFieldSchemas.add(new FieldSchema("age",FieldType.LONG));
    subFieldSchemas.add(new FieldSchema("phone",FieldType.KEYWORD));
    subFieldSchemas.add(new FieldSchema("address",FieldType.NESTED).setSubFieldSchemas(addressSubFiledSchemas));
    
    // Specify that FieldSchema of the child rows in the user Nested field is used as subfieldSchemas of the Nested field.
    List<FieldSchema> fieldSchemas = new ArrayList<>();
    fieldSchemas.add(new FieldSchema("user",FieldType.NESTED).setSubFieldSchemas(subFieldSchemas));
    ```


The Nested type has the following limits:

-   Nested indexes do not support the IndexSort feature. However, IndexSort can improve query performance in multiple scenarios.
-   If you use a search index that contains a nested field to query data and require pagination, you must specify the sorting method to return data in the query conditions. Otherwise, Tablestore does not return nextToken when only part of data that meets the query conditions is read.
-   Nested queries provide lower performance than other types of queries.

Apart from the preceding limits, the Nested type supports all queries, sorting, and aggregations.

