---
keyword: [local transaction, local transaction limit]
---

# Local transactions

This topic describes how to use the local transaction feature. You can create a local transaction based on a specified partition key value. After you read or write data within a local transaction, you can commit or abort the local transaction. Pessimistic locking is used to control concurrent operations within a local transaction.

The local transaction feature is available for invitational preview. By default, this feature is disabled. To use the local transaction feature, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd) to apply for invitational preview.

The local transaction feature allows you to perform atomic operations to read or write one or more rows.

## Scenarios

-   Read and write operations \(simple scenarios\)

    You can use the following methods to perform read, modify, and write \(RMW\) operations. Different methods have different limits.

    -   Conditional update: processes only one request that involves a single row at a time. It cannot be used to process requests that involve data across rows or requests for multiple write operations. For more information, see [Conditional update](/intl.en-US/Function Introduction/Wide Column model/Conditional update.md).
    -   Atomic counter: processes only one request that involves a single row at a time, and supports only the increment of column values. For more information, see [Atomic counters](/intl.en-US/Function Introduction/Wide Column model/Atomic counters.md).
    You can create a local transaction to perform an RMW operation on data within the range specified based on a partition key value.

    1.  Use StartLocalTransaction to create a local transaction based on a specified partition key value and obtain the ID of the local transaction.
    2.  Use GetRow or GetRange to read data. The transaction ID must be included in the request.
    3.  Modify data on the client.
    4.  Use PutRow, UpdateRow, DeleteRow, or BatchWriteRow to write back the modified data. The transaction ID must be included in the request.
    5.  Use CommitTransaction to commit the transaction.
-   Email \(complex scenarios\)

    You can create a local transaction to perform atomic operations on emails of the same user.

    To use the local transaction feature, include a data table and two index tables in a physical table. The following table describes the primary key columns of the data table and index tables.

    The Type column is used to identify the rows of the data table and the rows of index tables. The IndexField column stores the $Folder field for the Folder index table, and the $SendTime field for the SendTime index table. The IndexField column is empty for the data table.

    |Table|UserID|Type|IndexField|MailID|
    |:----|:-----|:---|:---------|:-----|
    |Data table|User ID|"Main"|"N/A"|Email ID|
    |Folder table|User ID|"Folder"|$Folder|Email ID|
    |SendTime table|User ID|"SendTime"|$SendTime|Email ID|

    You can use a local transaction to perform the following operations:

    -   Perform the following steps to list the last 100 emails sent by a user:
        1.  Use the user ID to create a local transaction and obtain the transaction ID.
        2.  Include the transaction ID in the request when you call GetRange to query 100 emails from the SendTime table.
        3.  Include the transaction ID in the request when you call BatchGetRow to query the detailed information of the 100 emails from the data table.
        4.  Use CommitTransaction to commit the local transaction or use AbortTransaction to abort the local transaction.

            The commit and abort operations have the same effect because no writes are performed in this transaction.

    -   Perform the following steps to transfer all emails from a folder to another folder:
        1.  Use the user ID to create a local transaction and obtain the transaction ID.
        2.  Include the transaction ID in the request when you call GetRange to query multiple emails from the Folder table.
        3.  Include the transaction ID in the request when you call BatchWriteRow to perform write operations on the Folder table.

            The write operation is performed on two rows each time when an email is transferred. Specifically, a row that indicates the original folder is deleted from the Folder table and a row that indicates the new folder is added to the Folder table.

        4.  Use CommitTransaction to commit the transaction.
    -   Perform the following steps to count the numbers of read emails and unread emails in a folder. This method is not the most efficient solution.
        1.  Use the user ID to create a local transaction and obtain the transaction ID.
        2.  Include the transaction ID in the request when you call GetRange to query multiple emails from the Folder table.
        3.  Include the transaction ID in the request when you call BatchGetRow to query the read status of each email from the data table.
        4.  Use CommitTransaction to commit the local transaction or use AbortTransaction to abort the local transaction.

            The commit and abort operations have the same effect because no write operations are performed in this transaction.

    In this scenario, you can add more index tables to accelerate common operations. The local transaction feature ensures the consistency between the status of the data table and index tables, which simplifies development. For example, when you count the number of emails, many emails are read, which results in high overheads. To reduce overheads and accelerate queries, you can use a new index table to store the numbers of read emails and unread emails.


## Limits

-   The validity period of a local transaction is up to 60 seconds.

    If a transaction is not committed or aborted within 60 seconds, the Tablestore server determines that the transaction times out and aborts the transaction.

-   A transaction may be created on the Tablestore server even if a timeout error is returned. In this case, you can resend a transaction creation request after the created transaction times out.
-   If a local transaction is not committed, it may become invalid. In this case, retry the operation within this transaction.
-   Tablestore imposes the following limits on read and write operations on data within a local transaction:
    -   The transaction ID cannot be used to access data beyond the range specified based on the partition key value that is used to create the transaction.
    -   The partition key values of all write requests in the same transaction must be the same as the partition key value used to create the transaction. This limit does not apply to read requests.
    -   A local transaction can be used only by one request at a time. When the transaction is in use, other operations that use the transaction ID fail.
    -   The maximum interval for read and write operations on data within a transaction is 60 seconds.

        If a transaction is not read or written for more than 60 seconds, the Tablestore server determines that the transaction times out and aborts the transaction.

    -   Up to 4 MB of data can be written to each transaction. The volume of data written to each transaction is calculated in the same way as a regular write request.
    -   If you do not specify a version number for a cell, the Tablestore server assigns a version number to the cell in the usual way when the cell is written to the transaction \(rather than when the transaction is committed\).
    -   If a BatchWriteRow request includes a transaction ID, all rows in the request can be written only to the table that matches the transaction ID.
    -   When you use a transaction, the data within the range specified based on the corresponding partition key value is locked. If a request that is sent to write data within the local transaction does not contain the transaction ID, the request fails. Data within the transaction is unlocked when the transaction is committed or aborted, or when the transaction times out.
    -   A transaction remains valid even if a read or write request with the transaction ID is rejected. You can resend the request in the same manner as a regular request or you can abort the transaction.

## Operations

The following table describes the operations that can be performed on local transactions.

|Operation|Description|
|---------|-----------|
|StartLocalTransaction|Creates a local transaction.|
|CommitTransaction|Commits a local transaction.|
|AbortTransaction|Aborts a local transaction.|
|GetRow|Read and write data within a local transaction. For more information, see [Single-row operations](/intl.en-US/Function Introduction/Wide Column model/Basic operations on data/Single-row operations.md) and [Multi-row operations](/intl.en-US/Function Introduction/Wide Column model/Basic operations on data/Multi-row operations.md). **Note:** Data within a local transaction is within a range specified based on a partition key value. For more information about partition keys, see [Primary keys and attributes](/intl.en-US/Function Introduction/Wide Column model/Primary keys and attributes.md). |
|PutRow|
|UpdateRow|
|DeleteRow|
|BatchWriteRow|
|GetRange|

## Use Tablestore SDKs

You can use the following Tablestore SDKs to implement local transactions:

-   Tablestore SDK for Java: [Local transactions](/intl.en-US/SDK Reference/Java SDK/Table/Configure local transaction.md)
-   Tablestore SDK for Go: [Local transactions](/intl.en-US/SDK Reference/Go SDK/Table/Configure local transaction.md)
-   Tablestore SDK for Python: [Local transactions](/intl.en-US/SDK Reference/Python SDK/Table/Configure local transaction.md)
-   Tablestore SDK for Node.js: [Local transactions](/intl.en-US/SDK Reference/Node.js SDK/Table/Configure local transaction.md)
-   Tablestore SDK for PHP: [Local transactions](/intl.en-US/SDK Reference/PHP SDK/Table operations/Configure local transaction.md)

## Parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the data table.|
|PrimaryKey|The primary key of the data table. -   You must specify a partition key value when you create a local transaction.
-   You must specify all primary key columns when you read and write data in a local transaction. |
|TransactionId|The local transaction ID that identifies the local transaction. You must specify a transaction ID when you read and write data within the local transaction. |

## Examples

1.  Call the startLocalTransaction method of AsyncClient or SyncClient to create a local transaction based on a specified partition key value and obtain the ID of the created local transaction.

    ```
    PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
    primaryKeyBuilder.addPrimaryKeyColumn("pk1", PrimaryKeyValue.fromString("txnKey"));
    PrimaryKey primaryKey = primaryKeyBuilder.build();
    StartLocalTransactionRequest request = new StartLocalTransactionRequest(tableName, primaryKey);
    String txnId = client.startLocalTransaction(request).getTransactionID();
    ```

2.  Read and write data within a local transaction.

    You must specify the transaction ID to read and write data within the local transaction. The read and write operations are similar to regular operations.

    -   Write a row to the local transaction.

        ```
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn("pk1", PrimaryKeyValue.fromString("txnKey"));
        primaryKeyBuilder.addPrimaryKeyColumn("pk2", PrimaryKeyValue.fromLong("userId"));
        PrimaryKey primaryKey = primaryKeyBuilder.build();
        RowPutChange rowPutChange = new RowPutChange(tableName, primaryKey);
        rowPutChange.addColumn(new Column("Col", ColumnValue.fromLong(columnValue)));
        
        PutRowRequest request = new PutRowRequest(rowPutChange);
        request.setTransactionId(txnId);
        client.putRow(request);
        ```

    -   Read the row from the local transaction.

        ```
        PrimaryKeyBuilder primaryKeyBuilder;
        primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn("pk1", PrimaryKeyValue.fromString("txnKey"));
        primaryKeyBuilder.addPrimaryKeyColumn("pk2", PrimaryKeyValue.fromLong("userId"));
        PrimaryKey primaryKey = primaryKeyBuilder.build();
        SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(tableName, primaryKey);
        criteria.setMaxVersions(1); // Specify that the latest version of data is read. 
        
        GetRowRequest  request = new GetRowRequest(criteria);
        request.setTransactionId(txnId);
        GetRowResponse getRowResponse = client.getRow(request);
        ```

3.  Commit or abort a local transaction.
    -   Commit a local transaction so that all data modifications within the local transaction take effect.

        ```
        CommitTransactionRequest commitRequest = new CommitTransactionRequest(txnId);
        client.commitTransaction(commitRequest);
        ```

    -   Abort a local transaction so that all data modifications within the local transaction do not take effect.

        ```
        AbortTransactionRequest abortRequest = new AbortTransactionRequest(txnId);
        client.abortTransaction(abortRequest);
        ```


## Billing

-   Each StartLocalTransaction, CommitTransaction, and AbortTransaction operation consumes one write capacity unit \(CU\).
-   Requests sent to read or write data in local transactions are billed in the same way as regular read and write requests. For more information, see [Billing overview](/intl.en-US/Pricing/Billing overview.md).

## Error codes

|Error code|Description|
|----------|-----------|
|OTSRowOperationConflict|The error message returned because the specified partition key value is used by an existing local transaction.|
|OTSSessionNotExist|The error message returned because the transaction that has the specified transaction ID does not exist, or the specified transaction is invalid or timed out.|
|OTSSessionBusy|The error message returned because the last request on the transaction is incomplete.|
|OTSOutOfTransactionDataSizeLimit|The error message returned because the amount of data within a transaction exceeds the maximum volume.|
|OTSDataOutOfRange|The error message returned because data operation is beyond the range specified by the partition key value used to create the transaction.|

