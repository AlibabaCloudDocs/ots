# ARRAY and nested data types

Search index provides the following special field types in addition to the basic field types such as LONG, DOUBLE, BOOLEAN, KEYWORD, TEXT, and GEOPOINT:

## ARRAY

ARRAY: a type that can be combined with the basic field types. For example, the combination of LONG with ARRAY is used to specify arrays of the LONG INTEGER type. LONG ARRAY fields can contain multiple long integers. If a query matches a component of an array, the corresponding row is returned.

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

    An array of text in the JSON ARRAY format. Example: \[\\"Hangzhou\\", \\"Xi'an\\"\]. TEXT JSON arrays are not commonly used.

-   GeoPoint Array

    An array of latitude and longitude coordinate pairs. Example: \[\\"34.2, 43.0\\", \\"21.4, 45.2\\"\].


The ARRAY type is supported only in search indexes. Therefore, when the type of an index field involves ARRAY, the field in the table must be of the STRING type. The basic data type such as LONG or DOUBLE in the search index remains the same as that in the table. If a price field is of the DOUBLE ARRAY type, the field type in the table must be STRING, and the field type must be DOUBLE and is Array must be set to true in the search index.

## Nested

Nested indicates nested documents. Nested documents are used when a row of data \(document\) contains multiple child documents. Multiple child documents are stored in a nested column. You must specify the schema of child documents in the nested column. The schema must include the fields of the child documents and the property of each field. Nested is similar to ARRAY. However, nested supports a wider range of features.

-   Examples

    The following example defines the format of a nested column in Java:

    ```
    // Specify the FieldSchema class for the child documents.
    List<FieldSchema> subFieldSchemas = new ArrayList<FieldSchema>();
    subFieldSchemas.add(new FieldSchema("tagName", FieldType.KEYWORD)
        .setIndex(true).setEnableSortAndAgg(true));
    subFieldSchemas.add(new FieldSchema("score", FieldType.DOUBLE)
        .setIndex(true).setEnableSortAndAgg(true));
    
    // Specify that FieldSchema of the child documents is used as subfieldSchemas of the nested column.
    FieldSchema nestedFieldSchema = new FieldSchema("tags", FieldType.NESTED)
        .setSubFieldSchemas(subFieldSchemas);
    ```

    The preceding code defines the format of a nested column named tags. The child documents contain the following fields:

    -   Field name: tagName. Field type: KEYWORD.
    -   Field name: score. Field type: DOUBLE.
    Tablestore writes nested columns as strings in JSON arrays to the table. The following example shows the data format of a nested column that contains two child documents:

    **Note:** Even when the column contains only one child document, the written strings must be JSON arrays.

    ```
    [{"tagName":"tag1", "score":0.8}, {"tagName":"tag2", "score":0.2}]
    ```

-   The nested type has the following limits:

    -   Nested indexes do not support the IndexSort feature. However, IndexSort can improve query performance in multiple scenarios.
    -   If you use a search index of the nested type to query data and require pagination, you must specify the sorting method to return data in the query conditions. Otherwise, Tablestore does not return nextToken when only part of data that meets the query conditions is read.
    -   Nested queries provide lower performance than other types of queries.
    Apart from the preceding limits, the nested type supports all queries, sorting, and aggregations.


