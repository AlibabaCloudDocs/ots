# Query the description of tables

You can call the DescribeTable operation to query the description such as the schema information and the reserved read and write throughput of a table.

**Note:** For more information about the DeacribeTable operation, see [DescribeTable](/intl.en-US/API Reference/Operations/DescribeTable.md).

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/. .NET SDK/Initialization.md).
-   A table is created.

## Operations

```
 /// <summary>
 /// Query the schema information and reserved read and write throughput settings of the specified table.
 /// </summary>
 /// <param name="request">Request parameter, including the table name.</param>
 /// <returns>Contains the schema information and reserved read and write throughput settings of the specified table. </returns>
 public DescribeTableResponse DescribeTable(DescribeTableRequest request);

 /// <summary>
 /// Asynchronous mode of DescribeTable.
 /// </summary>
 public Task<DescribeTableResponse> DescribeTableAsync(DescribeTableRequest request);
            
```

## Parameters

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table.|

## Examples

The following code provides an example on how to obtain the description of a table:

```
 try
 {
     var request = new DescribeTableRequest("SampleTable");
     var response = otsClient.DescribeTable(request);

     //Display the description of the specified table.
     Console.Writeline("Describe table succeeded.") ;
     Console.WriteLine("LastIncreaseTime: {0}", response.ReservedThroughputDetails.LastIncreaseTime);
     Console.WriteLine("LastDecreaseTime: {0}", response.ReservedThroughputDetails.LastDecreaseTime);
     Console.WriteLine("NumberOfDecreaseToday: {0}", response.ReservedThroughputDetails.LastIncreaseTime);
     Console.WriteLine("ReadCapacity: {0}", response.ReservedThroughputDetails.CapacityUnit.Read);
     Console.WriteLine("WriteCapacity: {0}", response.ReservedThroughputDetails.CapacityUnit.Write);
 }
 catch (Exception ex)
 {
     # If the task fails, an exception is returned for you to handle.
     Console.WriteLine("Describe table failed, exception:{0}", ex.Message);
        }
            
```

For the detailed sample code, visit [DescribeTable@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/CreateTableSample.cs).

