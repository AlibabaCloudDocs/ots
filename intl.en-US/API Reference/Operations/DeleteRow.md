# DeleteRow

You can call this operation to delete a row of data.

## Request structure

```
message DeleteRowRequest {
    required string table_name = 1;
    required bytes primary_key = 2; // The Plainbuffer encoding is binary.
    required Condition condition = 3;
    optional ReturnContent return_content = 4; 
}
```

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|table\_name|string|Yes|The name of the table whose data is to be updated.|
|primary\_key|bytes|Yes|The primary key of the row to be deleted, which is in Plainbuffer format. For more information about encoding, see [Plainbuffer](/intl.en-US/API Reference/Data Types/PlainBuffer.md) encoding.|
|condition|[Condition](/intl.en-US/API Reference/Data Types/Condition.md)|Yes|-   This parameter determines whether to perform existence check before data is updated. You can set this parameter to one of the following values:

    -   IGNORE: the row existence check is not performed.

    -   EXPECT\_EXIST: the row is expected to exist.

-   If this row is expected to exist but does not, the delete operation fails and an error is returned. If the existence of the row is ignored, this operation succeeds regardless of the row existence. |
|return\_content|[ReturnContent](/intl.en-US/API Reference/Data Types/ReturnContent.md)|No|The data type after the row is successfully written. Only the primary key can be returned, which is used for the auto-increment function of the primary key column.|

## Response structure

```
message DeleteRowResponse {
    required ConsumedCapacity consumed = 1;
    optional bytes row = 2;
}
```

|Parameter|Type|Description|
|---------|----|-----------|
|consumed|[ConsumedCapacity](/intl.en-US/API Reference/Data Types/ConsumedCapacity.md)|[The number of capacity units \(CUs\) consumed](#section_x9r_auo_rlz) by this operation.|
|row|bytes|-   The returned data when return\_content is set.
-   If return\_content is not set or no value is returned, NULL is returned.
-   This parameter is in the Plainbuffer format. For more information about encoding, see [Plainbuffer](/intl.en-US/API Reference/Data Types/PlainBuffer.md) encoding. |

## CU consumption:

-   If the row does not exist:

    -   If the value of the condition parameter is IGNORE, the number of consumed write CUs is calculated based on the formula: Number of consumed write CUs = \(Size of primary key columns in the row/4 KB\) rounded up.

    -   If the value of the condition parameter is EXPECT\_EXIST, the row fails to be deleted, and one write CU and one read CU are consumed

-   If the row exists:

    -   If the value of the condition parameter is IGNORE, the number of consumed write CUs is calculated based on the formula: Number of consumed write CUs = \(Size of primary key columns in the row/4 KB\) rounded up.

    -   If the value of the condition parameter is EXPECT\_EXIST, both write and read CUs are consumed. The number of consumed write CUs and the number of consumed read CUs are calculated based on the formula: Number of consumed read/write CUs = \(Size of primary key columns in the row/4 KB\) rounded up.

    For more information about how to calculate the data size, see [Billing](/intl.en-US/Pricing/Billing overview.md).

-   If an internal error code is returned \(HTTP status code: 5XX\), this operation does not consume CUs. If other errors are returned, one write CU is consumed.

-   If the request times out and the results are undefined, CUs may or may not be consumed.


