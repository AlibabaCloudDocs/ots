# Condition

Condition specifies the row judgment conditions in PutRow, UpdateRow, and DeleteRow. It contains the row\_existence and column\_condition parameters.

## Data structure

```
message Condition {
    required RowExistenceExpectation row_existence = 1;
    optional bytes column_condition      = 2;
}
			
```

row\_existence:

-   Type: see [RowExistenceExpectation](/intl.en-US/API Reference/Data Types/RowExistenceExpectation.md)
-   Description: the row existence check settings for this row.

column\_condition:

-   Type: bytes
-   Description: the column-based condition settings. This parameter indicates the binary data \(in bytes\) after the filter is serialized in the Protobuf format. For more information, see [Filter](/intl.en-US/API Reference/Data Types/Filter.md).

## Operations

[PutRow](/intl.en-US/API Reference/Operations/PutRow.md)

[UpdateRow](/intl.en-US/API Reference/Operations/UpdateRow.md)

[DeleteRow](/intl.en-US/API Reference/Operations/DeleteRow.md)

[BatchWriteRow](/intl.en-US/API Reference/Operations/BatchWriteRow.md)

