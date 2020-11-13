# Query table names

You can call the ListTable operation to query the names of all tables that are created in the current instance.

**Note:** For more information about the DescribeTable operation, see [ListTable](/intl.en-US/API Reference/Operations/ListTable.md).

## Prerequisites

-   Client is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/NodeJS SDK/Initialization.md).
-   A table is created.

## Operations

```
/**
 * Query the names of all tables in the current instance.
 */
listTable(params, callback)          
```

## Examples

The following code provides an example on how to query the names of all tables in an instance:

```
var client = require('./client');

client.listTable({}, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', data);
});            
```

For the complete sample code, visit [ListTable@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/listTable.js).

