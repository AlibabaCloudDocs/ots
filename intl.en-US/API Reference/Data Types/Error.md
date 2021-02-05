# Error

Error indicates an error message returned when an operation fails. This parameter is also used to indicate errors for single-row requests in the BatchGetRow and BatchWriteRow operations.

## Data structure

```
Error {
    required string code = 1;
    optional string message = 2;
}
				
```

code:

-   Type: string

-   Description: the error code for the current single-row operation.


message:

-   Type: string

-   Description: the error message for the current single-row operation. For more information about error messages, see the topic about error code.


## Operations

[BatchGetRow](/intl.en-US/API Reference/Operations/BatchGetRow.md)

[BatchWriteRow](/intl.en-US/API Reference/Operations/BatchWriteRow.md)

