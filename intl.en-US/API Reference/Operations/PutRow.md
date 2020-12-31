# PutRow

You can call this operation to add data to a specified row.

**Note:**

-   If the specified row does not exist, a new row is added. If the specified row exists, it is overwritten.
-   If the response to an operation do not contain errors, the operation is successful.

## Request structure

```
message PutRowRequest {
    required string table_name = 1;
    required bytes row = 2; // The PlainBuffer encoding is binary.
    required Condition condition = 3;
    optional ReturnContent return_content = 4; 
}           
```

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|table\_name|string|Yes|The name of the table to write data to. |
|row|bytes|Yes|The data to be written to the row. It is in Plainbuffer format and includes the primary key and attribute columns. For more information about encoding, see [Plainbuffer](/intl.en-US/API Reference/Data Types/PlainBuffer.md) encoding. |
|condition|[Condition](/intl.en-US/API Reference/Data Types/Condition.md)|Yes|Determines whether to perform row existence check before writing data. It can be one of three values:

 -   IGNORE: the row existence check is not performed.

-   EXPECT\_EXIST: the row is expected to exist.

-   EXPECT\_NOT\_EXIST: the row is not expected to exist. |
|return\_content|[ReturnContent](/intl.en-US/API Reference/Data Types/ReturnContent.md)|No|The data type after the row is written. Currently, only the primary key can be returned, which is used for the auto-increment feature of the primary key column. |

## Response structure

```
message PutRowResponse {
    required ConsumedCapacity consumed = 1;
    optional bytes row = 2;
}         
```

|Parameter|Type|Description|
|---------|----|-----------|
|consumed|[ConsumedCapacity](/intl.en-US/API Reference/Data Types/ConsumedCapacity.md)|This parameter specifies the number of [capacity units \(CUs\)](#title_xtj_bc1_h21) consumed by this operation. |
|row|bytes|-   This parameter indicates the returned data when return\_content is set.

-   If return\_content is not set or no value is returned, NULL is returned.

-   It is in Plainbuffer format. For more information about encoding, see [Plainbuffer](/intl.en-US/API Reference/Data Types/PlainBuffer.md) encoding. |

## CU consumption

-   If the row does not exist:

    -   If the value of the condition parameter is IGNORE, the number of consumed write CUs is calculated based on the following formula: Number of consumed write CUs = \[\(Size of primary key columns in the row + Size of attribute columns to be inserted\)/4 KB\] rounded up.

    -   If the value of the condition parameter is EXPECT\_NOT\_EXIST, both write and read CUs are consumed. The number of consumed write CUs is calculated based on the following formula: Number of consumed write CUs = \[\(Size of primary key columns in the row + Size of attribute columns to be inserted\)/4 KB\] rounded up. The number of consumed read CUs is calculated based on the following formula: Number of consumed read CUs = \(Size of primary key columns in the row/4 KB\) rounded up.

    -   If the value of the condition parameter is EXPECT\_EXIST, the row fails to be inserted. One write CU and one read CU are consumed.

-   If the row exists:

    -   If the value of the condition parameter is IGNORE, the number of consumed write CUs is calculated based on the following formula: Number of consumed write CUs = \[\(Size of primary key columns in the row + Size of attribute columns to be inserted\)/4 KB\] rounded up.

    -   If the value of the condition parameter is EXPECT\_EXIST, both write and read CUs are consumed. The number of consumed write CUs is calculated based on the following formula: Number of consumed write CUs = \[\(Size of all primary key columns in the row + Size of attribute columns to be inserted\)/4 KB\] rounded up. The number of consumed read CUs is calculated based on the following formula: Number of consumed read CUs = \(Size of primary key columns in the row/4 KB\) rounded up

    -   If the value of the condition parameter is EXPECT\_NOT\_EXIST, the row fails to be inserted. One write CU and one read CU are consumed.

-   Conditional Update:

    -   If the operation succeeds, the number of consumed CUs are calculated based on the preceding formulas.

    -   If the operation fails, one write CU and one read CU are consumed.

    For more information about how to calculate the data size, see [Pricing](/intl.en-US/Pricing/Billing overview.md).

-   If an internal error code is returned \(HTTP status code: 5XX\), this operation does not consume CUs. If other errors are returned, one write CU is consumed.

-   If the request times out and the results are undefined, CUs may or may not be consumed.


