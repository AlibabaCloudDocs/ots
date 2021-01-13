# UpdateTable

You can call this operation to update the settings of reserved read throughput or reserved write throughput of a table.

## Operations

```
 /// <summary>
 /// Update the reserved read throughput or reserved write throughput of the specified table. New settings take effect within a minute after the update succeeds.
 /// </summary>
 /// <param name="request">Request parameter, including the table name and reserved read/write throughput.</param>
 /// <returns>Contains information such as the updated reserved read/write throughput.</returns>
 public UpdateTableResponse UpdateTable(UpdateTableRequest request);

 /// <summary>
 /// Asynchronous mode of UpdateTable.
 /// </summary>
 public Task<UpdateTableResponse> UpdateTableAsync(UpdateTableRequest request);
			
```

## Examples

Update the reserved read throughput to 1 and the reserved write throughput to 2.

```
 // Update the reserved read throughput to 1 and the reserved write throughput to 2.
 var reservedThroughput = new CapacityUnit(1, 2);

 // Construct an UpdateTableRequest object.
 var request = new UpdateTableRequest("SampleTable", reservedThroughput);
 try
 {
     // Call the operation to update the reserved read/write throughput of the table.
     otsClient.UpdateTable(request);

     // If the task succeeds, no exception is returned.
     Console.Writeline("Update table succeeded.");
 }
 catch (Exception ex)
 {
     // If the task fails, an exception is returned. Handle the exception.
     Console.WriteLine("Update table failed, exception:{0}", ex.Message);
 }
			
```

For the detailed sample code, visit [UpdateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/CreateTableSample.cs).

