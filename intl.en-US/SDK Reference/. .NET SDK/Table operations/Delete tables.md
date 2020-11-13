# Delete tables

You can call the DeleteTable operation to delete a specified table from the current instance.

**Note:** For more information about the DeleteTable operation, see [DeleteTable](/intl.en-US/API Reference/Operations/DeleteTable.md).

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/. .NET SDK/Initialization.md).
-   A table is created.
-   The index tables and search indexes for the table are deleted.

## Operations

```
 /// <summary>
 /// Delete a table based on the table name.
 /// </summary>
 /// <param name="request">Request parameter, including the table name.</param>
 /// <returns>Information returned by DeleteTable. The result is null.
 /// </returns>
 public DeleteTableResponse DeleteTable(DeleteTableRequest request);

 /// <summary>
 /// Asynchronous mode of DeleteTable.
 /// </summary>
 public Task<DeleteTableResponse> DeleteTableAsync(DeleteTableRequest request);
            
```

## Examples

The following code provides an example on how to delete a specified table:

```
 var request = new DeleteTableRequest("SampleTable");
 try
 {
     otsClient.DeleteTable(request);
     Console.Writeline("Delete table succeeded.");
 }
 catch (Exception ex)
 {
     Console.WriteLine("Delete table failed, exception:{0}", ex.Message);
 }
            
```

For the complete sample code, visit [DeleteTable@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/CreateTableSample.cs).

