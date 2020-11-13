# Query table names

You can call the ListTable operation to query the names of all tables that are created in the current instance.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/. .NET SDK/Initialization.md).
-   A table is created.

## Operations

```
 /// <summary>
 /// Query the names of all tables created in the current instance.
 /// </summary>
 /// <param name="request">Request parameter</param>
 /// <returns>Information returned by ListTable that contains the table name list.</returns> </returns>
 public ListTableResponse ListTable(ListTableRequest request);

 /// <summary>
 /// Asynchronous mode of ListTable.
 /// </summary>
 public Task<ListTableResponse> ListTableAsync(ListTableRequest request);
            
```

## Examples

The following code provides an example on how to query the names of all tables in an instance:

```
 var request = new ListTableRequest();
 try
 {
     var response = otsClient.ListTable(request);
     foreach (var tableName in response.TableNames)
     {
         Console.WriteLine("Table name:{0}", tableName);
     }
     Console.WriteLine("List table succeeded.");
 }
 catch (Exception ex)
 {
     Console.WriteLine("List table failed, exception:{0}", ex.Message);
 }
            
```

