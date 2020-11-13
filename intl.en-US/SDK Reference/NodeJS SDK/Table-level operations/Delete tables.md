# Delete tables

You can call the DeleteTable operation to delete a specified table from the current instance.

## Prerequisites

-   Client is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/NodeJS SDK/Initialization.md).
-   A table is created.
-   The index tables and search indexes for the table are deleted.

## Operations

```
/**
 * Delete a specified table from the current instance.
 */
deleteTable(params, callback)  
```

## Parameters

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table.|

## Examples

The following code provides an example on how to delete a specified table:

```
var client = require('./client');

var params = {
    tableName: 'sampleTable'
};

client.deleteTable(params, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', data);
});
            
```

For the detailed sample code, visit [DeleteTable@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/deleteTable.js).

