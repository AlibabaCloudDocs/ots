# Configure atomic counter

Atomic counters allow you to implement an atomic counter on a column. This feature provides statistic data for online applications such as the number of page views \(PVs\) on various topics.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/.NET SDK/Initialization.md).
-   A data table is created. Data is written to the table.

## Limits

-   You can implement atomic counters only on INTEGER columns.
-   By default, if a column that is specified as an atomic counter does not exist, the value of the column is 0 before you write data. If a column that is specified as an atomic counter is not an INTEGER column, an OTSParameterInvalid error occurs.
-   You can update an atomic counter by using a positive or negative number, but you must avoid an integer overflow. If an integer overflow occurs, an OTSParameterInvalid error is returned.
-   By default, the value of an atomic counter is not returned in the response to an update row request. You can specify that the increased value of an atomic counter is returned.
-   You cannot specify a column as an atomic counter and update the column in a single request. If Attribute Column A is set to an atomic counter, you cannot perform other operations such as overwrite and delete operations on the attribute column A.
-   You can perform multiple update operations on the same row by using a BatchWriteRow request. However, if you perform an atomic counter operation on a row, you can perform only one update operation on the row in a BatchWriteRow request.
-   Only the value of the latest version of an atomic counter is increased. You cannot increase the value of a specified version of an atomic counter. After you update a row, a new version of data is inserted to the atomic counter in the row.

## API operations

The API operations to implement the atomic counter feature are added to the rowUpdateChange class. The following table lists these operations.

|Operation|Description|
|---------|-----------|
|RowUpdateChange Increment\(Column column\)|Increases or decreases a value by a number.|
|List<String\> ReturnColumnNames|Specifies the column names whose value needs to be returned for the columns on which operations are performed to implement atomic counter.|
|ReturnType ReturnType|Specifies the return type. The updated value in the column on which operations are performed to implement atomic counter must be returned.|

## Parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table.|
|ColumnName|The name of the column on which operations are performed to implement atomic counter. Columns whose valid values are integers are supported to implement the atomic counter feature.|
|Value|The value to update the columns on which operations are performed to implement atomic counter.|
|ReturnColumnNames|Specifies the column names whose value needs to be returned for the columns on which operations are performed to implement atomic counter.|
|ReturnType|Sets the return type to ReturnType.RT\_AFTER\_MODIFY and returns the values of the columns on which operations are performed to implement atomic counter.|

## Examples

The following code provides an example on how to call the rowUpdateChange operation to update the columns whose valid values are integers and return the updated values when you write a row of data:

```
public static void Increment(int incrementValue)
{
    Console.WriteLine("Start set increment column...") ;
    OTSClient otsClient = Config.GetClient();

    // Specify the primary key of the row. The primary key must be consistent with the primary key specified in TableMeta when you create a table.
    PrimaryKey primaryKey = new PrimaryKey
    {
        { Pk1, new ColumnValue(0) },
        { Pk2, new ColumnValue("abc") }
    };
    RowUpdateChange rowUpdateChange = new RowUpdateChange(TableName, primaryKey); // Specify the name of the table.
    // Set the return type to ReturnType.RT_AFTER_MODIFY and return the values of the columns on which operations are performed to implement atomic counter.
    rowUpdateChange.ReturnType = ReturnType.RT_AFTER_MODIFY;
    rowUpdateChange.ReturnColumnNames = new List<string>() { IncrementCol};
    // Set the column on which operations are performed to implement atomic counter. The column value starts from 0 and increases by the number specified by the incrementValue parameter.
    rowUpdateChange.Increment(new Column(IncrementCol, new ColumnValue(incrementValue)));

    UpdateRowRequest updateRowRequest = new UpdateRowRequest(rowUpdateChange);

    var response = otsClient.UpdateRow(updateRowRequest);
    Console.WriteLine("set Increment column succeed, Increment result:" + response.Row.GetColumns()[0].Value);
}
```

