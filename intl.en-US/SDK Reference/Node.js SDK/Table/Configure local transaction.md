# Configure local transaction

This topic describes how to use local transactions. You can create a local transaction based on a specified partition key value. After you read or write data within a local transaction, you can commit or abort the local transaction.

The local transaction feature is available for invitational preview and is disabled by default. To use local transactions, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd) to request an invitational preview.

You can use local transaction to specify that the operations on a partition key are atomic, which indicates that operations based on the specified partition key either succeed or fail. The isolation level of the local transaction is serializable.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Node.js SDK/Initialization.md).
-   A data table is created. Data is written to the table.

## Usage notes

1.  Use startLocalTransaction to create a local transaction based on the specified partition key value and obtain the ID of the local transaction.
2.  Read and write data within the local transaction.

    You can call the following operations on the local transaction: GetRow, PutRow, DeleteRow, UpdateRow, BatchWriteRow, and GetRange.

3.  Use commitTransaction to commit local transactions or use abortTransaction to abort local transactions.

## Limits

-   The validity period of a local transaction is up to 60 seconds.

    If a transaction is not committed or aborted within 60 seconds, the server of Tablestore determines that the transaction times out and aborts it.

-   A transaction may be created in the server side of Tablestore even if a timeout error is returned. In this case, you can resend a transaction creation request only after the created transaction times out.
-   If a local transaction is not committed, it may become invalid. In this case, retry the operation within this transaction.
-   Tablestore imposes the following limits on read and write operations on data within a local transaction:
    -   The transaction ID cannot be used to access data beyond the range specified based on the partition key value that is used to create the transaction.
    -   The partition key values of all write requests in the same transaction must be the same as the partition key value used to create the transaction. This limit does not apply to read requests.
    -   A local transaction can be used only by one request at a time. When the transaction is in use, other operations that use the transaction ID fail.
    -   The maximum interval for read and write operations on a transaction is 60 seconds.

        If a transaction is not read or written for more than 60 seconds, the server of Tablestore determines that the transaction times out and aborts it.

    -   Up to 4 MB of data can be written to each transaction. The volume of data written to each transaction is calculated in the same way as a regular write request.
    -   If you do not specify a version number for a cell, the server of Tablestore assigns a version number to the cell in the usual way when the cell is written into the transaction \(rather than when the transaction is committed\).
    -   If a BatchWriteRow request includes a transaction ID, all rows in the request can be written only to the table that matches the transaction ID.
    -   When you use a transaction, the data within the range specified based on the corresponding partition key value is locked. If a request that is sent to write data within the transaction excludes the transaction ID, the request fails. The data in the transaction is unlocked when the transaction is committed or aborted, or when the transaction times out.
    -   A transaction remains valid even if a read or write request with the transaction ID is rejected. You can resend the request in the same manner as a regular request or you can abort the transaction.

## Parameters

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table.|
|primaryKey|The primary key of the table. -   When you create a local transaction, you need only to specify the partition key value for the local transaction.
-   After a local transaction is created, you must specify a complete primary key when you read data from or write data to the local transaction. |
|transactionId|The ID that identifies a local transaction. After a local transaction is created, the transaction ID must be included for operations on the local transaction. |

## Examples

-   Commit a local transaction for all modified data to take effect.

    ```
    (async () => {
        try {
    
            // Create a local transaction.
            const response = await client.startLocalTransaction({
                tableName,
                primaryKey: [{  // You need only to specify the partition key value for the local transaction.
                    "id": "partitionKeyValue"
                }]
            });
    
            // Obtain the local transaction ID.
            const transactionId = response.transactionId;
    
            // Write data within the local transaction.
            await client.putRow({
                tableName,
                condition: new TableStore.Condition(TableStore.RowExistenceExpectation.IGNORE, null),
                primaryKey,
                attributeColumns: [{
                    col: 'updated'
                }],
                transactionId
            });
    
            // Commit the local transaction.
            await client.commitTransaction({
                transactionId
            })
        } catch (e) {
            console.error(e)
        }
    })();
    ```

-   Abort a local transaction to ignore all modified data.

    ```
    (async () => {
        try {
    
            // Create a local transaction.
            const response = await client.startLocalTransaction({
                tableName,
                primaryKey: [{  // You need only to specify the partition key value for the local transaction.
                    "id": "partitionKeyValue"
                }]
            });
            // Obtain the local transaction ID.
            const transactionId = response.transactionId
    
            // Write data within the local transaction.
            await client.putRow({
                tableName,
                condition: new TableStore.Condition(TableStore.RowExistenceExpectation.IGNORE, null),
                primaryKey,
                attributeColumns: [{
                    col: 'updated'
                }],
                transactionId
            });
            // Abort the local transaction.
            await client.abortTransaction({
                transactionId
        })
      } catch (e) {
        console.error(e)
      }
    })();
    ```


