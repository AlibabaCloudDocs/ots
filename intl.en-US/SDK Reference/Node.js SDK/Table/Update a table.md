# Update a table

You can call the UpdateTable operation to update the maximum number of versions and reserved read/write throughput of a table.

## API

```
  /**
   * Update the reserved read/write throughput of a specified table.
   */
  updateTable(params, callback) 
        
```

## Examples

Set maxVersions of a table to 5.

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

**Note:** Code details can be obtained at [updateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/updateTable.js).

