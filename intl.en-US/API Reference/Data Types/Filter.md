# Filter

Filter specifies the column-based conditions. It is applicable to the Conditional Update and Filter functions.

## Data structure

```language-protobuf
message Filter {
    required FilterType type = 1;
    required bytes filter = 2;  
}
				
```

type:

-   Type: [FilterType](/intl.en-US/API Reference/Data Types/FilterType.md)

-   Column condition type.


filter:

-   Type: bytes

-   The binary data of the condition statement in [CompositeColumnValueFilter](/intl.en-US/API Reference/Data Types/CompositeColumnValueFilter.md), [ColumnPaginationFilter](/intl.en-US/API Reference/Data Types/ColumnPaginationFilter.md), or [SingleColumnValueFilter](/intl.en-US/API Reference/Data Types/SingleColumnValueFilter.md) type after being serialized in Protobuf format.


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


