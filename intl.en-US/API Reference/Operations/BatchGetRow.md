# BatchGetRow

You can call this operation to simultaneously read multiple rows of data from one or more tables.

The BatchGetRow operation is a set of multiple GetRow operations. The execution, returning of results, and capacity unit \(CU\) consumption of each operation are carried out independently.

Compared with the execution of a large number of GetRow operations, the use of the BatchGetRow operation can effectively reduce the request response time and increase the data read rate.

## Request structure

```
message BatchGetRowRequest {
    repeated TableInBatchGetRowRequest tables = 1;
}           
```

## Request parameters

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|tables|repeated [TableInBatchGetRowRequest](/intl.en-US/API Reference/Data Types/TableInBatchGetRowRequest.md)|Yes|This parameter specifies the information of rows to be read.

 The entire operation fails and returns an error if any of the following conditions appears in tables:

 -   Any table listed in the tables parameter does not exist.

-   The name of any table does not comply with [Naming conventions and data types](/intl.en-US/Function Introduction/Wide Column model/Naming conventions and data types.md).

-   The primary key is not specified for any row in a table, the primary key column name does not comply with the conventions, or the primary key column type is incorrect.

-   A column name in columns\_to\_get of any table does not comply with [Naming conventions and data types](/intl.en-US/Function Introduction/Wide Column model/Naming conventions and data types.md).

-   Tables with the same name exist.

-   Any table in the tables contains rows with identical primary keys.

-   The total number of RowInBatchGetRowRequest in all tables exceeds 100.

-   Any table in the tables does not contain any RowInBatchGetRowRequest.

-   The value of columns\_to\_get of any table exceeds 128. |

## Response structure

```
message BatchGetRowResponse {
    repeated TableInBatchGetRowResponse tables = 1;
}          
```

**Note:** The BatchGetRow operation may partially fail at the row level. However, it still returns an HTTP status code of 200. The application must check errors in RowInBatchGetRowResponse to confirm the execution results for each row and handle the errors.

## Response parameters

|Parameter|Type|Description|
|---------|----|-----------|
|tables|repeated [TableInBatchGetRowResponse](/intl.en-US/API Reference/Data Types/TableInBatchGetRowResponse.md)|-   The value of this parameter corresponds to the data to be read in each table.

-   The TableInBatchGetRowResponse object order in the response message is the same as the TableInBatchGetRowRequest object order in BatchGetRowRequest. The order of each RowInBatchGetRowResponse under TableInBatchGetRowResponse is the same as that of RowInBatchGetRowRequest under TableInBatchGetRowRequest.

-   If a row does not exist or does not have data in the specified columns\_to\_get, a corresponding RowInBatchGetRowResponse still appears in TableInBatchGetRowResponse, but the values of primary\_key\_columns and attribute\_columns of the row are null.

-   If a row fails to be read, the is\_ok value in RowInBatchGetRowResponse for the row is false and the row is null. |

## CU consumption

-   If the entire operation fails, it does not consume any CUs.
-   If the request times out and the results are undefined, CUs may or may not be consumed.
-   In other situations, each RowInBatchGetRowRequest operation is considered as one GetRow operation when write CUs are calculated. For more information, see [GetRow](/intl.en-US/API Reference/Operations/GetRow.md).

