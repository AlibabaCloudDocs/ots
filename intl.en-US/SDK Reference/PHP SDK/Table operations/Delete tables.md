# Delete tables

You can call the DeleteTable operation to delete a specified table from the current instance.

**Note:** For more information about the DeleteTable operation, see [DeleteTable](/intl.en-US/API Reference/Operations/DeleteTable.md).

## Prerequisites

-   OTSClient is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A table is created.
-   The index tables and search indexes for the table are deleted.

## Operations

```
/**
 * Delete a table based on the table name.
 * @api
 * @param [] $request The request parameters.
 * @return [] The response is empty. If the DeleteTable operation succeeds, no message is returned. An empty array is returned to be consistent with other operations.
 * @throws OTSClientException The exception that is returned when a parameter error occurs or the Tablestore server returns a verification error.
 * @throws OTSServerException The exception that is returned when the Tablestore server returns an error.
 */
 public function deleteTable(array $request);            
```

## Parameters

-   Request parameters

    |Parameter|Description|
    |---------|-----------|
    |table\_name|The name of the base table.|

-   Request format

    ```
    $result = $client->deleteTable([
         'table_name' => '<string>', // REQUIRED
    ]);           
    ```

-   Response parameters

    The response is empty. If an error occurs, the system returns an exception.

-   Response format

    ```
        []    
    ```


## Examples

The following code provides an example on how to delete a specified table:

```
$result = $otsClient->deleteTable([
    'table_name' => 'MyTable'
]);        
```

