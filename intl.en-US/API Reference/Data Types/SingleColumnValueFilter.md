# SingleColumnValueFilter

SingleColumnValueFilter specifies a single condition, for example, column\_a \> 5. It is applicable to the ConditionUpdate and Filter functions.

## Data structure

```language-protobuf
message SingleColumnValueFilter {
    required ComparatorType comparator = 1;
    required string column_name = 2;
    required bytes column_value = 3;
    required bool filter_if_missing = 4;
    required bool latest_version_only = 5; 
}
 

```

comparator:

-   Type: [ComparatorType](/intl.en-US/API Reference/Data Types/ComparatorType.md)

-   The comparison type.


column\_name:

-   Type: String

-   The column name.


column\_value:

-   Type: Bytes

-   The value of the column after being encoded in [Plainbuffer](/intl.en-US/API Reference/Data Types/PlainBuffer.md) format.


filter\_if\_missing:

-   Type: Bool

-   Determines whether the condition is filtered if the column of a row does not exist. For example, the condition is column\_a \> 0, and the value of filter\_if\_missing is true. If column\_a does not exist for a row, the condition column\_a \> 0 is true.


latest\_version\_only:

-   Type: Bool

-   Determines whether the condition is valid only for the latest version. If the value is true, the value of the latest version is checked if it meets the condition. If the value is false, the values of all versions are checked if they meet the conditions.


## Related operations

-   ConditionUpdate

    [PutRow](/intl.en-US/API Reference/Operations/PutRow.md)

    [UpdateRow](/intl.en-US/API Reference/Operations/UpdateRow.md)

    [DeleteRow](/intl.en-US/API Reference/Operations/DeleteRow.md)

    [BatchWriteRow](/intl.en-US/API Reference/Operations/BatchWriteRow.md)

-   Filter

    [GetRow](/intl.en-US/API Reference/Operations/GetRow.md)

    [GetRange](/intl.en-US/API Reference/Operations/GetRange.md)

    [BatchGetRow](/intl.en-US/API Reference/Operations/BatchGetRow.md)


