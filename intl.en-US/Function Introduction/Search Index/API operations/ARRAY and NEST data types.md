# ARRAY and NEST data types

Search index provides the following special field types in addition to the basic field types such as LONG, DOUBLE, BOOLEAN, KEYWORD, TEXT, and GEOPOINT:

-   ARRAY: a type that can be combined with the basic field types. For example, the combination of LONG with ARRAY is used to specify arrays of the LONG INTEGER type. LONG ARRAY fields can contain multiple long integers. If a query matches a component of the array, the corresponding row is returned.
-   NEST: similar to ARRAY. However, NEST supports a wider range of functions.

## ARRAY

Basic types of components of arrays

-   Long Array

    An array of long integers. Example: \[1000, 4, 5555\].

-   Boolean Array

    An array of BOOLEAN values. Example: \[true, false\].

-   Double Array

    An array of double-precision floating-point numbers. Example: \[3.1415926, 0.99\].

-   Keyword Array

    An array of strings in the JSON ARRAY format. Example: \[\\"Hangzhou\\", \\"Xi'an\\"\].

-   Text Array

    An array of text in the JSON ARRAY format. Example: \[\\"Hangzhou\\", \\"Xi'an\\"\]. TEXT JSON arrays are not frequently used.

-   GeoPoint Array

    An array of latitude and longitude coordinate pairs. Example: \[\\"34.2, 43.0\\", \\"21.4, 45.2\\"\].


The ARRAY type is supported only in search indexes. Therefore, when the type of an index field involves ARRAY, the field in the table must be of the STRING type. The basic data type such as LONG or DOUBLE in the search index remains the same as that in the table. For example, when a price field is of the DOUBLE ARRAY type, the field type in the table must be STRING, and the field type must be DOUBLE and isArray must be set to true in the search index.

## NEST

NEST specifies nested documents. Nested documents are used when a row of data \(document\) contains multiple child documents. Multiple child documents are stored in a NEST column. You must specify the schema of child documents in the NEST column. The schema must include the fields of the child documents and the property of each field.

-   Examples

    The following example defines the format of a NEST column in Java:

    ```
    // Specify the FieldSchema class for the child documents.
    List<FieldSchema> subFieldSchemas = new ArrayList<FieldSchema>();
    subFieldSchemas.add(new FieldSchema("tagName", FieldType.KEYWORD)
        .setIndex(true).setEnableSortAndAgg(true));
    subFieldSchemas.add(new FieldSchema("score", FieldType.DOUBLE)
        .setIndex(true).setEnableSortAndAgg(true));
    
    // Specify that FieldSchema of the child documents is used as subfieldSchemas of the NEST column.
    FieldSchema nestedFieldSchema = new FieldSchema("tags", FieldType.NESTED)
        .setSubFieldSchemas(subFieldSchemas);
    ```

    The preceding code defines the format of a nested column named tags. The child documents contain the following fields:

    -   Field name: tagName. Field type: KEYWORD.
    -   Field name: score. Field type: DOUBLE.
    Tablestore writes NEST columns as strings in JSON arrays to the table. The following example shows the data format of a NEST column:

    ```
    [{"tagName":"tag1", "score":0.8}, {"tagName":"tag2", "score":0.2}]
    ```

    This column contains two child documents.

    **Note:** Even when the column contains only one child document, the written strings must be JSON arrays.

-   The NEST type has the following limits:

    -   Nested indexes do not support the IndexSort feature. However, IndexSort can improve query performance in many scenarios.
    -   Nested queries provide lower performance than other types of queries.
    Apart from the preceding limits, the NEST type supports all queries, sorting, and aggregations.


