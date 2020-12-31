# UpdateRow

You can call this operation to update the data of a specified row.

**Note:** If the row does not exist, a new row is added. If the row exists, the values of the specified columns are added, modified, or deleted based on the request content.

## Request structure

```
message UpdateRowRequest {
    required string table_name = 1;
    required bytes row_change = 2;
    required Condition condition = 3;
    optional ReturnContent return_content = 4; 
}
```

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|table\_name|string|Yes|The name of the table whose data is to be updated. |
|row\_change|bytes|Yes|-   The data to be updated. It is in Plainbuffer format and includes the primary key and attribute columns. For more information about encoding, see [Plainbuffer](/intl.en-US/API Reference/Data Types/PlainBuffer.md) encoding.
-   All the attribute columns whose data you want to update for this row. Tablestore adds, modifies, or deletes the values in the specified attribute columns based on each specified update type in row\_change.
-   Columns that exist in this row but are not specified by row\_change are not affected.
-   The following values can be used for UpdateType:
    -   PUT: The value you use to update the column must be a valid attribute column value. This update type indicates that if the specified column does not exist, a new column is added. If the specified column exists, the existing column is overwritten.
    -   DELETE: The value you use to update the column must be empty, and a timestamp must be specified. This update type indicates that data of the specified version is deleted from the column.
    -   DELETE\_ALL: The value you use to update the column and the timestamp must be empty. This update type indicates that all versions of data in the column are deleted.

 **Note:** A row exists even if all attribute columns in the row are deleted. To delete a row, use the DeleteRow operation. |
|condition|[Condition](/intl.en-US/API Reference/Data Types/Condition.md)|Yes|-   This parameter determines whether to perform existence check before data is updated. You can set this parameter to one of the following values:
    -   IGNORE: the row existence check is not performed.
    -   EXPECT\_EXIST: the row is expected to exist.
-   If a row that is expected to exist does not exist, the row cannot be updated and an error is returned. If the existence of the row is ignored, the operation result is not affected by the row existence. |
|return\_content|[ReturnContent](/intl.en-US/API Reference/Data Types/ReturnContent.md)|No|The type of the data to return after the row is updated. Only the primary key is returned. It is used for the auto-increment function of the primary key column.|

## Response structure

```
message UpdateRowResponse {
    required ConsumedCapacity consumed = 1;
    optional bytes row = 2;
}
```

|Parameter|Type|Description|
|---------|----|-----------|
|consumed|[ConsumedCapacity](/intl.en-US/API Reference/Data Types/ConsumedCapacity.md)|[The number of capacity units \(CUs\) consumed](#title_444_bqk_2ce) by this operation.|
|row|bytes|-   The returned data when return\_content is set.
-   If return\_content is not set or no value is returned, NULL is returned.
-   This parameter is in the Plainbuffer format. For more information about encoding, see [Plainbuffer](/intl.en-US/API Reference/Data Types/PlainBuffer.md) encoding. |

## CU consumption:

-   If the row does not exist:
    -   If the value of the condition parameter is IGNORE, the number of consumed write CUs is calculated based on the formula: Number of consumed write CUs = \[\(Size of primary key columns in the row + Size of attribute columns to be updated\)/4 KB\] rounded up. If UpdateRow specifies an attribute column to delete, the length of the column name is calculated as the column size.

    -   If the value of the condition parameter is EXPECT\_EXIST, the row fails to be inserted. One write CU and one read CU are consumed.
-   If the row exists:

    -   If the value of the condition parameter is IGNORE, the number of consumed write CUs is calculated based on the formula: Number of consumed write CUs = \[\(Size of primary key columns in the row + Size of attribute columns to be updated\)/4 KB\] rounded up. If UpdateRow specifies an attribute column to delete, the length of the column name is calculated as the column size.
    -   If the value of the condition parameter is EXPECT\_EXIST, both write and read CUs are consumed. The number of consumed write CUs is calculated by using the same method that is used when the value of the condition parameter is IGNORE. The number of consumed read CUs is calculated based on the formula: Number of consumed read CUs = \(Size of primary key columns in the row/4 KB\) rounded up.
    For more information about how to calculate the data size, see [Billing](/intl.en-US/Pricing/Billing overview.md).

-   If the request times out and the results are undefined, CUs may or may not be consumed.
-   If an internal error code is returned \(HTTP status code: 5XX\), this operation does not consume CUs. If other errors are returned, one write CU and one read CU are consumed.

