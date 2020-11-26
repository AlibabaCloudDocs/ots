# ColumnPaginationFilter

ColumnPaginationFilter specifies the filtering conditions for reading a wide row. It is applicable to the Filter function.

## Data structure

```language-protobuf
message ColumnPaginationFilter {
    required int32 offset = 1;
    required int32 limit = 2;
}

```

offset:

-   Type: int32

-   The position of the starting column, that is, the first column to be read


limit:

-   Type: int32

-   The number of columns to be read


## Related operations

Filter

[GetRow](/intl.en-US/API Reference/Operations/GetRow.md)

[GetRange](/intl.en-US/API Reference/Operations/GetRange.md)

[BatchGetRow](/intl.en-US/API Reference/Operations/BatchGetRow.md)

