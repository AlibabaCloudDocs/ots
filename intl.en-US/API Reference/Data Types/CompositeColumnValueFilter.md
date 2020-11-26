# CompositeColumnValueFilter

CompositeColumnValueFilter specifies a group of conditions, for example, column\_a \> 5 AND column\_b = 10. It is applicable to the ConditionUpdate and Filter functions.

## Data structure

```language-protobuf
message CompositeColumnValueFilter {
    required LogicalOperator combinator = 1;
    repeated Filter sub_filters = 2;
}

```

combinator:

-   Type: [LogicalOperator](/intl.en-US/API Reference/Data Types/LogicalOperator.md)

-   The logical operator.


sub\_filter:

-   Type: [Filter](/intl.en-US/API Reference/Data Types/Filter.md)

-   The sub-condition expression.


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


