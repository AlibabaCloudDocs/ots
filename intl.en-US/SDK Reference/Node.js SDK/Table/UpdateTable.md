# UpdateTable

You can call this operation to update the maximum number of versions, reserved read throughput, or reserved write throughput of the specified table.

## Operations

```
  /**
   * Update the reserved read/write throughput of a specified table.
   */
  updateTable(params, callback) 
			
```

## Examples

The following code provides an example on how to update the maximum number of versions of a table to five:

```
var client = require('./client');

var params = {
    tableName: 'sampleTable',
    tableOptions: {
        maxVersions: 5,
    }
};

client.updateTable(params, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', data);
});

			
```

For the detailed sample code, visit [UpdateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/updateTable.js).

