# Global secondary index

After you create index tables for a base table, you can read data from the index tables or delete specified index tables.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A table is created. The value of timeToLive is -1. The value of maxVersions is 1.
-   Predefined columns are set for the table.

## Create an index table by calling the CreateIndex operation

You can call the CreateIndex operation to create an index table for an existing base table.

**Note:** You can also create one or more index tables when you create a base table by calling the CreateTable operation. For more information, see [Create tables](/intl.en-US/SDK Reference/PHP SDK/Table operations/Create tables.md).

-   API operations

    ```
        /**
         * Create a global secondary index.
         * @api
         *
         * @param [] $request
         *            The request parameter, which is the table name.
         * @return [] The response.
         * @throws OTSClientException The exception that is returned when a parameter error occurs or the Tablestore server returns a verification error.
         * @throws OTSServerException The exception that is returned when the Tablestore server returns an error.
         * @example "src/examples/CreateIndex.php"
         */
        public function createIndex(array $request)
    ```

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |main\_table\_name|The name of the table.|
    |index\_meta|The schema information of the index table, which includes the following items:    -   name: the name of the index table.
    -   primary\_key: the primary key of the index table, which is a combination of primary key columns and predefined columns of the base table.
    -   defined\_column: the indexed attribute column, which is a combination of predefined columns of the base table.
    -   index\_update\_mode: the update mode of the index table. Only IUM\_ASYNC\_INDEX is supported.
    -   index\_type: the type of the index table. Only IT\_GLOBAL\_INDEX is supported. |
    |include\_base\_data|Specifies whether the index table includes the existing data in the base table.When the include\_base\_data parameter in CreateIndex is set to true, the index includes the existing data. When the value is false, the index excludes the existing data. |

-   Examples

    ```
    $request = array(
        'table_name' => 'MainTableName',
        'index_meta' => array(
            'name' => self::$indexName1,
            'primary_key' => array('col1'),
            'defined_column' => array('col2')
        )
    );
    $this->otsClient->createIndex($request);
    ```


## Read data from the index table

You can read a row of data or read data within a specified range from the index table. If the columns to return are included in the index table, you can query the data from the index table. Otherwise, you must query the base table for the required data.

-   Read a row of data from the index table

    For more information, see [Single-row operations](/intl.en-US/SDK Reference/PHP SDK/Single-row operations.md).

    When you use the GetRow operation to read data from the index table, take note of the following items:

    -   You must set table\_name to the name of the index table.
    -   Tablestore autocompletes the primary key that is not configured in the index columns for the index table. Therefore, when you specify the primary key of a row, you must specify the indexed columns based on which you create the index table and the autocompleted primary key columns.
-   Read data within a specified range from the index table

    For more information, see [Multi-row operations](/intl.en-US/SDK Reference/PHP SDK/Multi-row operations.md).

    When you use the GetRange operation to read data from the index table, take note of the following items:

    -   You must set table\_name to the name of the index table.
    -   Tablestore autocompletes the primary key that is not configured in the index columns for the index table. Therefore, when you specify the start primary key and the end primary key of the range, you must specify the indexed columns based on which you create the index table and the autocompleted primary key columns.

## Delete a index table \(DropIndex\)

You can call the DropIndex operation to delete a specified index table from a base table.

-   API operations

    ```
        /**
         * Delete a global secondary index.
         * @api
         *
         * @param [] $request
         *            The request parameter, which is the table name.
         * @return [] The response.
         * @throws OTSClientException The exception that is returned when a parameter error occurs or the Tablestore server returns a verification error.
         * @throws OTSServerException The exception that is returned when the Tablestore server returns an error.
         * @example "src/examples/DropIndex.php"
         */
        public function dropIndex(array $request)
    ```

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |table\_name|The name of the table.|
    |index\_name|The name of the index table.|

-   Examples

    ```
    $request = array (
        'table_name' => 'MainTableName',
        'index_name' => 'IndexName'
    );
    $otsClient->dropIndex( $request );
    ```


