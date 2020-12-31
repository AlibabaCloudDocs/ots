# Make Tablestore HBase Client compatible with HBase versions earlier than 1.0

Tablestore HBase Client supports the operations of HBase 1.0.0 and later. This topic describes how to make Tablestore HBase Client compatible with the operations of HBase versions earlier than 1.0.

Compared with earlier versions, HBase 1.0.0 has some major changes, which are incompatible with HBase of earlier versions.

This topic also describes the major changes to facilitate your operations.

## Connections

HConnection is deprecated in HBase 1.0.0 and later. We recommend that you use `org.apache.hadoop.hbase.client.ConnectionFactory` to create a class to implement Connections, and replace the deprecated ConnectionManager and HConnectionManager with ConnectionFactory.

Connection creation is a heavy-weight operation. Connection implementations are thread-safe. You can create a connection, and share it with different threads.

For HBase 1.0.0 and later, you must manage the lifecycle of the connection and close the connection after use.

The following code is the latest code used to create a connection:

```
Connection connection = ConnectionFactory.createConnection(config);
// ...
connection.close();
```

## TableName class

If you use HBase versions earlier than 1.0.0, you can specify a STRING-type table name when you create a table. For HBase 1.0.0 and later, you must use the `org.apache.hadoop.hbase.TableName` class.

The following code is the latest code used to specify a STRING-type table name:

```
String tableName = "MyTable";
// or byte[] tableName = Bytes.toBytes("MyTable");
TableName tableNameObj = TableName.valueOf(tableName);
```

## Table, BufferedMutator, and RegionLocator operations

The HTable operation is replaced by the Table, BufferedMutator, and RegionLocator operations in HBase 1.0.0 and later.

-   `org.apache.hadoop.hbase.client.Table`: performs read and write operations on a single table.

-   `org.apache.hadoop.hbase.client.BufferedMutator`: writes data asynchronously. This operation corresponds to `setAutoFlush(boolean)` of the HTableInterface operation of the earlier HBase versions.

-   `org.apache.hadoop.hbase.client.RegionLocator`: indicates the partition information of the table.


The Table, BufferedMutator, and RegionLocator operations are not thread-safe. However, they are lightweight and can be used to create an object for each thread.

## Admin operations

The HBaseAdmin operation is replaced by `org.apache.hadoop.hbase.client.Admin` in HBase 1.0.0 and later. Tablestore is a cloud service, and most Tablestore O&M operations are automatically processed. Therefore, most Admin operations are not supported by Tablestore. For more information, see [Differences between Tablestore and HBase](/intl.en-US/Function Introduction/HBase/Differences between Table Store and HBase.md).

Use a connection instance to create an admin instance:

```
Admin admin = connection.getAdmin();
```

