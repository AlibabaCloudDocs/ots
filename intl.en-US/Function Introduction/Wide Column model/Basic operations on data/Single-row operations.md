# Single-row operations

Tablestore provides the following single-row operations: PutRow, GetRow, UpdateRow, and DeleteRow.

**Note:** A row is the basic unit of a table. Rows consist of primary keys and attributes. A primary key is required for a row. Rows within a table contain primary key columns of the same names and same data types. Attributes are optional for a row. Rows within a table can contain different attributes. For more information, see [Overview](/intl.en-US/Function Introduction/Wide Column model/Overview.md).

## Operations

Single-row operations include PutRow, GetRow, UpdateRow, and DeleteRow. The following table describes these operations.

|Operation|Description|
|---------|-----------|
|[GetRow](/intl.en-US/API Reference/Operations/GetRow.md)|Reads a row.|
|[PutRow](/intl.en-US/API Reference/Operations/PutRow.md)|Inserts a row into a table. If the row exists, data in the row is deleted and new data is inserted.|
|[UpdateRow](/intl.en-US/API Reference/Operations/UpdateRow.md)|Updates a row. You can add attribute columns to or delete attribute columns from a row. You can also update the values of attribute columns in a row. If the row you want to update does not exist, a new row is inserted into the table. However, if an UpdateRow operation only deletes specified columns from a row and the row does not exist, no row is inserted into the table. |
|[DeleteRow](/intl.en-US/API Reference/Operations/DeleteRow.md)|Deletes a row. If the row you want to delete does not exist, no changes are made to the table. |

## Use Tablestore SDKs

You can use the following Tablestore SDKs to perform single-row operations:

-   Tablestore SDK for Java: [Single row operations](/intl.en-US/SDK Reference/Java SDK/Single-row operations.md)
-   Tablestore SDK for Go: [Single-row operations](/intl.en-US/SDK Reference/Go SDK/Single-row operations.md)
-   Tablestore SDK for Python: [Single-row operations](/intl.en-US/SDK Reference/Python SDK/Single-row operations.md)
-   Tablestore SDK for Node.js: [Single-row operations](/intl.en-US/SDK Reference/NodeJS SDK/Single-row operations.md)
-   Tablestore SDK for .NET: [Single-row operations](/intl.en-US/SDK Reference/.NET SDK/Single-row operations.md)
-   Tablestore SDK for PHP: [Single-row operations](/intl.en-US/SDK Reference/PHP SDK/Single-row operations.md)

## PutRow

You can call this operation to insert a row. If the row exists, data of all versions in all columns of the row is deleted before new data is inserted.

The GetRow operations do not consumed write capacity units \(CUs\). The number of consumed read CUs is calculated based on the formula: Number of consumed read CUs = \[\(Size of primary key columns in the row + Size of attribute columns that are read\)/4 KB\] rounded up. If the specified row does not exist, one read CU is consumed by the operation.

Responses vary based on whether the operation succeeds.

-   If an operation succeeds, Tablestore returns the number of CUs consumed by the operation.

    **Note:** Write operations consume read CUs based on the conditions that you specify.

    By specifying the condition field in the write request of a single row, you can specify whether to perform row existence check before the write operation. The following table describes the conditions you can configure to perform row existence check.

    |condition|Description|
    |---------|-----------|
    |IGNORE|The row existence check is not performed.|
    |EXPECT\_EXIST|The row is expected to exist.     -   If the row exists, the operation result is not affected.
    -   If the row does not exist, the operation fails. |
    |EXPECT\_NOT\_EXIST|The row is expected not to exist.     -   If the row does not exist, the operation result is not affected.
    -   If the row exists, the operation fails.
**Note:** If you set condition to EXPECT\_NOT\_EXIST in a DeleteRow or UpdateRow operation, the operation is unnecessary because rows that do not exist cannot be deleted or updated. To update a row that does not exist, you can use the PutRow operation. |

-   If errors occur, such as parameter check failures, excessive data in a row, or existence check failures, Tablestore returns the corresponding error codes.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |tableName|The name of the table.|
    |primaryKey|The primary key of the row.**Note:**

    -   The configured number and types of primary key columns must be the same as the number and types of primary key columns of the table.
    -   If a primary key column is an auto-increment column, you need only to set the value of the auto-increment column to a placeholder. For more information, see [Auto-increment of primary key columns](/intl.en-US/Function Introduction/Wide Column model/Auto-increment of primary key columns.md). |
    |condition|The condition you can configure to perform the PutRow operation. You can configure a row existence condition or a column-based condition. For more information, see [Conditional update](/intl.en-US/Function Introduction/Wide Column model/Conditional update.md).**Note:**

    -   RowExistenceExpectation.IGNORE indicates that new data is inserted into a row regardless of whether the specified row exists. If the specified row exists, the existing data is overwritten.
    -   RowExistenceExpectation.EXPECT\_EXIST indicates that new data is inserted only when the specified row exists. The existing data is overwritten.
    -   RowExistenceExpectation.EXPECT\_NOT\_EXIST indicates that data is inserted only when the specified row does not exist. |
    |column|The attribute column of the row.    -   An attribute column is specified by parameters in the following sequence: the attribute column name, attribute column value \(ColumnValue\), attribute column value type \(ColumnType, which is optional\), and timestamp \(optional\).
    -   You can set ColumnType to ColumnType.INTEGER, ColumnType.STRING, ColumnType.BINARY, ColumnType.BOOLEAN, or ColumnType.DOUBLE, which separately indicates INTEGER, STRING \(a UTF-8 encoded string\), BINARY, BOOLEAN, or DOUBLE. If you want to set the column value type to BINARY, you must set ColumnType to ColumnType.BINARY. If you want to use other types of column values, the setting of ColumnType is optional.
    -   The timestamp is the data version number. For more information, see [Max versions and TTL](/intl.en-US/Function Introduction/Wide Column model/Max versions and TTL.md).

You can customize a data version number or use the data version number generated by Tablestore. By default, if you do not specify this parameter, the data version number generated by Tablestore is used.

        -   The version number generated by Tablestore is calculated based on the number of milliseconds that have elapsed since 00:00:00 UTC on January 1, 1970.
        -   When you choose to specify the version number, ensure that the version number is a 64-bit timestamp accurate to the millisecond within the valid version range. |

-   Example 1

    The following code provides an example on how to write a row that contains 10 attribute columns and write one version of data for each column. The version number \(timestamp\) is generated by Tablestore.

    ```
    private static void putRow(SyncClient client, String pkValue) {
        // Construct the primary key.
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
        PrimaryKey primaryKey = primaryKeyBuilder.build();
        // Specify the name of the table.
        RowPutChange rowPutChange = new RowPutChange(TABLE_NAME, primaryKey);
    
        // Add attribute columns.
        for (int i = 0; i < 10; i++) {
            rowPutChange.addColumn(new Column("Col" + i, ColumnValue.fromLong(i)));
        }
    
        client.putRow(new PutRowRequest(rowPutChange));
    }
                        
    ```

-   Example 2

    The following code provides an example on how to write a row that contains 10 attribute columns and write three versions of data for each column. In this example, a custom version number \(timestamp\) is used.

    ```
    private static void putRow(SyncClient client, String pkValue) {
        // Construct the primary key.
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
        PrimaryKey primaryKey = primaryKeyBuilder.build();
        // Specify the name of the table.
        RowPutChange rowPutChange = new RowPutChange(TABLE_NAME, primaryKey);
    
        // Add attribute columns.
        long ts = System.currentTimeMillis();
        for (int i = 0; i < 10; i++) {
            for (int j = 0; j < 3; j++) {
                rowPutChange.addColumn(new Column("Col" + i, ColumnValue.fromLong(j), ts + j));
            }
        }
    
        client.putRow(new PutRowRequest(rowPutChange));
    }
                        
    ```

-   Example 3

    The following code provides an example on how to write a row that contains 10 attribute columns and write three versions of data for each column. In this example, a condition that expects the row not to exist is set and a custom version number \(timestamp\) is used.

    ```
    private static void putRow(SyncClient client, String pkValue) {
        // Construct the primary key.
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
        PrimaryKey primaryKey = primaryKeyBuilder.build();
        // Specify the name of the table.
        RowPutChange rowPutChange = new RowPutChange(TABLE_NAME, primaryKey);
    
        // Set a condition that expects the specified row not to exist.
        rowPutChange.setCondition(new Condition(RowExistenceExpectation.EXPECT_NOT_EXIST));
    
        // Add attribute columns.
        long ts = System.currentTimeMillis();
        for (int i = 0; i < 10; i++) {
            for (int j = 0; j < 3; j++) {
                rowPutChange.addColumn(new Column("Col" + i, ColumnValue.fromLong(j), ts + j));
            }
        }
    
        client.putRow(new PutRowRequest(rowPutChange));
    }
                        
    ```

-   Example 4

    The following code provides an example on how to write a row that contains 10 attribute columns and write three versions of data for each column. In this example, a condition that expects the row to exist and the Col0 value of the row to be greater than 100 is set and a custom version number \(timestamp\) is used.

    ```
    private static void putRow(SyncClient client, String pkValue) {
        // Construct the primary key.
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
        PrimaryKey primaryKey = primaryKeyBuilder.build();
        // Specify the name of the table.
        RowPutChange rowPutChange = new RowPutChange(TABLE_NAME, primaryKey);
    
        // Set a condition that expects the row to exist and the Col0 value of the row to be greater than 100.
        Condition condition = new Condition(RowExistenceExpectation.EXPECT_EXIST);
        condition.setColumnCondition(new SingleColumnValueCondition("Col0",
                SingleColumnValueCondition.CompareOperator.GREATER_THAN, ColumnValue.fromLong(100)));
        rowPutChange.setCondition(condition);
    
        // Add attribute columns.
        long ts = System.currentTimeMillis();
        for (int i = 0; i < 10; i++) {
            for (int j = 0; j < 3; j++) {
                rowPutChange.addColumn(new Column("Col" + i, ColumnValue.fromLong(j), ts + j));
            }
        }
    
        client.putRow(new PutRowRequest(rowPutChange));
    }
                        
    ```


## GetRow

You can call this operation to read a row.

The number of read and write CUs consumed by a PutRow operation is calculated based on the following rules:

-   The number of consumed write CUs is calculated based on the formula: Number of consumed write CUs = \[\(Size of primary key columns in the row + Size of attribute columns in the row\)/4 KB\] rounded up.
-   If the value of the condition field is not IGNORE, the PutRow operation consumes read CUs. The number of consumed read CUs is calculated based on the formula: Number of consumed read CUs = \(Size of primary key columns in the row/4 KB\) rounded up.
-   If the row existence condition is not met, the operation fails, and one write and one read CU are consumed.

One of the following results of the read request may be returned:

-   If the row exists, the primary key columns and attribute columns of the row are returned.
-   If the row does not exist, no row is returned and no error is reported.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |tableName|The name of the table.|
    |primaryKey|The primary key of the row.**Note:** The configured number and types of primary key columns must be the same as the number and types of primary key columns of the table. |
    |columnsToGet|The names of the columns to read. You can specify the names of primary key columns and the names of attribute columns. If you do not specify this parameter, all data in the row is returned.

**Note:**

    -   By default, if you query a row of data, Tablestore returns all columns of the row. You can configure the columnsToGet parameter to read data from specified columns. For example, the system returns only the values of col0 and col1 if col0 and col1 are included in columnsToGet.
    -   If you configure columnsToGet and filter together, Tablestore first queries the columns specified by columnsToGet, and then returns rows that meet the filter conditions. |
    |maxVersions|The maximum number of versions that can be read.**Note:** You must set at least one of the following parameters: maxVersions and timeRange.

    -   If only maxVersions is specified, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If only timeRange is specified, all data whose versions are within the specified time range or data of the specified version is returned.
    -   If both maxVersions and timeRange are specified, data of up to the specified number of versions within the time range is returned from the latest to the earliest. |
    |timeRange|The versions within a specified time range or the specified version to read. For more information, see [TimeRange](/intl.en-US/API Reference/Data Types/TimeRange.md).**Note:** You must set at least one of the following parameters: maxVersions and timeRange.

    -   If only maxVersions is specified, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If only timeRange is specified, all data whose versions are within the specified time range or data of the specified version is returned.
    -   If both maxVersions and timeRange are specified, data of up to the specified number of versions within the time range is returned from the latest to the earliest.
    -   To query data whose versions are within a specified time range, you must set start and end. start indicates the start timestamp. end indicates the end timestamp. The specified range includes the start value and excludes the end value.
    -   To query data of a specified version, you must set timestamp. timestamp indicates a specified timestamp.
You need only to set one of timestamp and \[start, end\).

The minimum value you can specify for timestamp is 0, and the maximum value is Long.MAX\_VALUE. Unit: milliseconds. |
    |filter|The filter used to filter the query results on the server side of Tablestore. Only rows that meet the filter conditions are returned. For more information, see [Filter](/intl.en-US/Function Introduction/Wide Column model/Filter.md).**Note:** If you configure columnsToGet and filter together, Tablestore first queries the columns specified by columnsToGet, and then returns rows that meet the filter conditions. |

-   Example 1

    The following code provides an example on how to read the latest versions of the specified columns from a row:

    ```
    private static void getRow(SyncClient client, String pkValue) {
        // Construct the primary key.
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
        PrimaryKey primaryKey = primaryKeyBuilder.build();
    
        // Specify the table name and primary key to read a row of data.
        SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(TABLE_NAME, primaryKey);
        // Set MaxVersions to 1 to read the latest version of data.
        criteria.setMaxVersions(1);
        GetRowResponse getRowResponse = client.getRow(new GetRowRequest(criteria));
        Row row = getRowResponse.getRow();
    
        System.out.println("Read complete. Result:");
        System.out.println(row);
    
        // Specify the columns to read.
        criteria.addColumnsToGet("Col0");
        getRowResponse = client.getRow(new GetRowRequest(criteria));
        row = getRowResponse.getRow();
    
        System.out.println("Read complete. Result:");
        System.out.println(row);
    } 
    ```

-   Example 2

    The following code provides an example on how to set filter conditions to read a row of data:

    ```
    private static void getRow(SyncClient client, String pkValue) {
        // Construct the primary key.
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
        PrimaryKey primaryKey = primaryKeyBuilder.build();
    
        // Specify the table name and primary key to read a row of data.
        SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(TABLE_NAME, primaryKey);
        // Set MaxVersions to 1 to read the latest version of data.
        criteria.setMaxVersions(1);
    
        // Set a filter to return the row when the value in Col0 is 0.
        SingleColumnValueFilter singleColumnValueFilter = new SingleColumnValueFilter("Col0",
                SingleColumnValueFilter.CompareOperator.EQUAL, ColumnValue.fromLong(0));
        // If the Col0 column does not exist, the row is not returned.
        singleColumnValueFilter.setPassIfMissing(false);
        criteria.setFilter(singleColumnValueFilter);
    
        GetRowResponse getRowResponse = client.getRow(new GetRowRequest(criteria));
        Row row = getRowResponse.getRow();
    
        System.out.println("Read complete. Result:");
        System.out.println(row);
    }
    ```


## UpdateRow

You can call this operation to update data of a specified row. You can add attribute columns to or delete attribute columns from a row, delete a specified version of data from an attribute column, or update the existing data in an attribute column. If the row you want to update does not exist, a new row is inserted into the table.

**Note:** If the UpdateRow request contains only columns to delete, and the specified row does not exist, a new row is not added.

The number of read and write CUs consumed by an UpdateRow operation is calculated based on the following rules:

-   The number of consumed write CUs is calculated based on the formula: Number of consumed write CUs = \[\(Size of primary key columns in the row + Size of attribute columns to update in the row\)/4 KB\] rounded up.

    If the UpdateRow request contains deletion instructions on specified attribute columns, the length of the name of each attribute column to delete is calculated as the column size.

-   If the value of the condition field is not IGNORE, the PutRow operation consumes read CUs. The number of consumed read CUs is calculated based on the formula: Number of consumed read CUs = \(Size of primary key columns in the row/4 KB\) rounded up.
-   If the row existence condition is not met, the operation fails and one write and one read CU are consumed.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |tableName|The name of the table.|
    |primaryKey|The primary key of the row.**Note:** The configured number and types of primary key columns must be the same as the number and types of primary key columns of the table. |
    |condition|The condition you can configure to perform the UpdateRow operation. You can configure a row existence condition or a column-based condition. For more information, see [Conditional update](/intl.en-US/Function Introduction/Wide Column model/Conditional update.md).|
    |column|The attribute column you want to update.    -   An attribute column is specified by parameters in the following sequence: the attribute column name, attribute column value, attribute column value type \(optional\), and timestamp \(optional\).

The timestamp is the version number of the data. You can customize a data version number or use the data version number generated by Tablestore. By default, if you do not specify this parameter, the data version number generated by Tablestore is used. For more information, see [Max versions and TTL](/intl.en-US/Function Introduction/Wide Column model/Max versions and TTL.md).

        -   The version number generated by Tablestore is calculated based on the number of milliseconds that have elapsed since 00:00:00 UTC on January 1, 1970.
        -   When you choose to specify the version number, ensure that the version number is a 64-bit timestamp accurate to the millisecond within the valid version range.
    -   To delete a specified version of data from an attribute column, you need only to set the attribute column name and timestamp.

The timestamp is a 64-bit integer that indicates a specified version of data. Unit: milliseconds.

    -   To delete an attribute column, you need only to set the attribute column name.

**Note:** A row exists even if all attribute columns in the row are deleted. To delete a row, use the DeleteRow operation. |

-   Example 1

    The following code provides an example of how to update columns, delete the specified version of a column, and delete a specified column of a row:

    ```
    private static void updateRow(SyncClient client, String pkValue) {
        // Construct the primary key.
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
        PrimaryKey primaryKey = primaryKeyBuilder.build();
        // Specify the name of the table.
        RowUpdateChange rowUpdateChange = new RowUpdateChange(TABLE_NAME, primaryKey);
    
        // Update columns.
        for (int i = 0; i < 10; i++) {
            rowUpdateChange.put(new Column("Col" + i, ColumnValue.fromLong(i)));
        }
    
        // Delete a specified version of a specified column.
        rowUpdateChange.deleteColumn("Col10", 1465373223000L);
    
        // Delete a specified column.
        rowUpdateChange.deleteColumns("Col11");
    
        client.updateRow(new UpdateRowRequest(rowUpdateChange));
    }                   
    ```

-   Example 2

    The following code provides an example on how to configure update conditions:

    ```
    private static void updateRow(SyncClient client, String pkValue) {
        // Construct the primary key.
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
        PrimaryKey primaryKey = primaryKeyBuilder.build();
        // Specify the name of the table.
        RowUpdateChange rowUpdateChange = new RowUpdateChange(TABLE_NAME, primaryKey);
    
        // Set a condition that expects the row to exist and the Col0 value of the row to be greater than 100.
        Condition condition = new Condition(RowExistenceExpectation.EXPECT_EXIST);
        condition.setColumnCondition(new SingleColumnValueCondition("Col0",
                SingleColumnValueCondition.CompareOperator.GREATER_THAN, ColumnValue.fromLong(100)));
        rowUpdateChange.setCondition(condition);
    
        // Update columns.
        for (int i = 0; i < 10; i++) {
            rowUpdateChange.put(new Column("Col" + i, ColumnValue.fromLong(i)));
        }
    
        // Delete a specified version of a specified column.
        rowUpdateChange.deleteColumn("Col10", 1465373223000L);
    
        // Delete a specified column.
        rowUpdateChange.deleteColumns("Col11");
    
        client.updateRow(new UpdateRowRequest(rowUpdateChange));
    }             
    ```


## DeleteRow

You can call this operation to delete a row. If the row you want to delete does not exist, no changes are made to the table.

The number of read and write CUs consumed by a DeleteRow operation is calculated based on the following rules:

-   The number of consumed write CUs is calculated based on the formula: Number of consumed write CUs = \(Size of primary key columns in the row/4 KB\) rounded up.
-   If the value of the condition field is not IGNORE, the DeleteRow operation consumes read CUs. The number of consumed read CUs is calculated based on the formula: Number of consumed read CUs = \(Size of primary key columns in the row/4 KB\) rounded up.
-   If the row existence condition is not met, the operation fails, and one write CU is consumed.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |tableName|The name of the table.|
    |primaryKey|The primary key of the row.**Note:** The configured number and types of primary key columns must be the same as the number and types of primary key columns of the table. |
    |condition|The condition you can configure to perform the DeleteRow operation. You can configure a row existence condition or a column-based condition. For more information, see [Conditional update](/intl.en-US/Function Introduction/Wide Column model/Conditional update.md).|

-   Example 1

    The following code provides an example on how to delete a row from a table:

    ```
    private static void deleteRow(SyncClient client, String pkValue) {
        // Construct the primary key.
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
        PrimaryKey primaryKey = primaryKeyBuilder.build();
        // Specify the name of the table.
        RowDeleteChange rowDeleteChange = new RowDeleteChange(TABLE_NAME, primaryKey);
    
        client.deleteRow(new DeleteRowRequest(rowDeleteChange));
    }                    
    ```

-   Example 2

    The following code provides an example on how to set deletion conditions:

    ```
    private static void deleteRow(SyncClient client, String pkValue) {
        // Construct the primary key.
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
        PrimaryKey primaryKey = primaryKeyBuilder.build();
        // Specify the name of the table.
        RowDeleteChange rowDeleteChange = new RowDeleteChange(TABLE_NAME, primaryKey);
    
        // Set a condition that expects the row to exist and the Col0 value of the row to be greater than 100.
        Condition condition = new Condition(RowExistenceExpectation.EXPECT_EXIST);
        condition.setColumnCondition(new SingleColumnValueCondition("Col0",
                SingleColumnValueCondition.CompareOperator.GREATER_THAN, ColumnValue.fromLong(100)));
        rowDeleteChange.setCondition(condition);
    
        client.deleteRow(new DeleteRowRequest(rowDeleteChange));
    }                   
    ```


