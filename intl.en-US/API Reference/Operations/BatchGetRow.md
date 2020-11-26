# BatchGetRow

BatchGetRow reads several data rows from one or more tables.

It is essentially a set of multiple GetRow operations. Each operation is independently executed, returns results independently, and independently consumes capacity units.

Compared to the execution of a large number of GetRow operations, the use of the BatchGetRow operation can reduce the request response time and increase the data read rate.

## Request Structure:

```
message BatchGetRowRequest {
    repeated TableInBatchGetRowRequest tables = 1;
}           
```

tables:

-   Type: repeated [TableInBatchGetRowRequest](/intl.en-US/API Reference/Data Types/TableInBatchGetRowRequest.md)

-   Required Parameter: Yes

-   Specifies the information of rows to be read.

-   The entire operation fails and returns an error if `tables` has any of the following conditions:

    -   Any table in `tables` does not exist.

    -   The name of any table in `tables` does not comply with the naming rule.

    -   The primary key is not specified for any row in `tables`, the primary key name does not comply with the conventions, or the primary key type is incorrect.

    -   For any table in `tables`, a column name in columns\_to\_get does not comply with the naming rule.

    -   `tables` contains tables with identical names.

    -   Any table in `tables` contains rows with identical primary keys.

    -   In `tables`, the total number of RowInBatchGetRowRequest exceeds 100.

    -   Any table in `tables` does not contain any RowInBatchGetRowRequest.

    -   Columns\_to\_get for any table in `tables` exceeds 128 columns.


## Response message structure:

```
message BatchGetRowResponse {
    repeated TableInBatchGetRowResponse tables = 1;
}           
```

tables:

-   Type: repeated [TableInBatchGetRowResponse](/intl.en-US/API Reference/Data Types/TableInBatchGetRowResponse.md)

-   Corresponds to the data to be read in each table.

-   The TableInBatchGetRowResponse object order in the response message is the same as the TableInBatchGetRowRequest object order in BatchGetRowRequest. The order of each RowInBatchGetRowResponse under TableInBatchGetRowResponse is the same as that of RowInBatchGetRowRequest under TableInBatchGetRowRequest.

-   If a row does not exist or does not have data in the specified columns\_to\_get, a corresponding RowInBatchGetRowResponse still appears in TableInBatchGetRowResponse, but the row’s primary\_key\_columns and attribute\_columns are null.

-   If a row fails to be read, the is\_ok value in RowInBatchGetRowResponse for the row is false and the row is null.


**Note:** At the row level, the BatchGetRow operation may partially fail. In this case, it still returns an HTTP status code of 200, but the application must check errors in RowInBatchGetRowResponse to confirm the execution results for each row and then proceed accordingly.

Capacity unit consumption:

-   If the entire operation fails, no capacity units are consumed.

-   If a request time-out occurs and the results are undefined, a capacity unit may or may not be consumed.

-   In other situations, each RowInBatchGetRowRequest operation is viewed as one [GetRow](/intl.en-US/API Reference/Operations/GetRow.md) operation when write capacity units are counted.


