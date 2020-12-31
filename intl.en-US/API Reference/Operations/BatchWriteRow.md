# BatchWriteRow

You can call this operation to simultaneously insert, modify, or delete multiple rows of data in one or more tables.

It is a set of multiple PutRow, UpdateRow, and DeleteRow operations. The execution, returning of results, and capacity unit \(CU\) consumption of each individual operation are carried out independently.

Compared with the execution of a large number of write operations, the use of BatchWriteRow can reduce the request response time and increase the data write rate.

## Request structure

```
message BatchWriteRowRequest {
    repeated TableInBatchWriteRowRequest tables = 1;
}        
```

## Request parameters

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|tables|repeated [TableInBatchWriteRowRequest](/intl.en-US/API Reference/Data Types/TableInBatchWriteRowRequest.md)|Yes|This parameter specifies the information of rows that require write operations.

 An error is returned if any of the following conditions occurs:

 -   Any table listed in the tables parameter does not exist.
-   Tables with the same name exist.
-   The name of any table does not comply with the [Naming conventions and data types](/intl.en-US/Function Introduction/Wide Column model/Naming conventions and data types.md).
-   The primary key is not specified for any row in a table, the primary key column name does not comply with the conventions, or the primary key column type is incorrect.
-   For any attribute column in a table, the column name does not comply with [Naming conventions and data types](/intl.en-US/Function Introduction/Wide Column model/Naming conventions and data types.md).
-   Any row in a table contains an attribute column with the same name as the primary key column.
-   The value of any primary key or attribute column in a table exceeds [General limits](/intl.en-US/Function Introduction/Limits/General limits.md).
-   Any table in the tables contains rows with identical primary keys.
-   The total number of rows for all tables listed in the tables parameter exceeds 200 or the total size of the data exceeds 4 MB.
-   If any table contains no rows, the OTSParameterInvalidException error is returned.
-   The number of columns included in any PutRowInBatchWriteRowRequest of a table exceeds 1,024.
-   The number of ColumnUpdate objects included in any UpdateRowInBatchWriteRowRequest in a table exceeds 1,024. |

## Response structure

```
message BatchWriteRowResponse {
    repeated TableInBatchWriteRowResponse tables = 1;
}      
```

## Response parameters

|Parameter|Type|Description|
|---------|----|-----------|
|tables|[TableInBatchWriteRowResponse](/intl.en-US/API Reference/Data Types/TableInBatchWriteRowResponse.md)|This parameter specifies the response information corresponding to the operations for each table. The response information contains the execution results, error codes, and number of CUs consumed.

 The TableInBatchWriteRowResponse object order in the response message is the same as the TableInBatchWriteRowRequest object order in BatchWriteRowRequest. The RowInBatchWriteRowResponse object orders contained in put\_rows, update\_rows, and delete\_rows in each TableInBatchWriteRowRequest are the same as the respective PutRowInBatchWriteRowRequest, UpdateRowInBatchWriteRowRequest, and DeleteRowInBatchWriteRowRequest object orders contained in put\_rows, update\_rows, and delete\_rows in TableInBatchWriteRowRequest.

 If a row fails to be read, the is\_ok value of the row in RowInBatchWriteRowResponse is false. |

**Note:** The BatchWriteRow operation may partially fail at the row level. However, it still returns an HTTP status code of 200. The application must check for errors in RowInBatchWriteRowResponse to confirm the execution results of each row and then handle the errors.

## CU consumption

-   If the entire operation fails, it does not consume any CUs.
-   If the request times out and the results are undefined, CUs may or may not be consumed.
-   In other situations, each PutRowInBatchWriteRowRequest, UpdateRowInBatchWriteRowRequestDelete, and RowInBatchWriteRowRequest operation corresponds to a separate write operation when the number of write capacity units are counted. For more information, see [PutRow](/intl.en-US/API Reference/Operations/PutRow.md), [UpdateRow](/intl.en-US/API Reference/Operations/UpdateRow.md), and [DeleteRow](/intl.en-US/API Reference/Operations/DeleteRow.md).

