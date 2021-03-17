# Configure local transaction

This topic describes how to use local transactions. You can create a local transaction based on a specified partition key value. After you read or write data within a local transaction, you can commit or abort the local transaction.

目前局部事务功能处于邀测中，默认关闭。如果需要使用该功能，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)进行申请或者加入钉钉群23307953（表格存储技术交流群-2）进行咨询。

The local transaction feature is available for invitational preview and is disabled by default. To use local transactions, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd) to request an invitational preview.

The local transaction feature is available for invitational preview and is disabled by default. To use local transactions, submit a ticket to request an invitational preview.

You can use local transaction to specify that the operations on a partition key are atomic, which indicates that operations based on the specified partition key either succeed or fail. The isolation level of the local transaction is read committed.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A data table is created. Data is written to the table.

## Usage notes

1.  Use start\_local\_transaction to create a local transaction based on the specified partition key value and obtain the ID of the local transaction.
2.  Read and write data within the local transaction.

    You can call the following operations on the local transaction: GetRow, PutRow, DeleteRow, UpdateRow, BatchWriteRow, and GetRange.

3.  Use commit\_transaction to commit the local transaction or use abort\_transaction to abort the local transaction.

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
|table\_name|The name of the table.|
|key|The partition key of the table.When you create a local transaction, you need only to specify the partition key value for the local transaction. |
|primary\_key|The primary key of the table.After a local transaction is created, you must specify a complete primary key when you read data from or write data to the local transaction. |
|transaction\_id|The ID that identifies a local transaction. After a local transaction is created, the transaction ID must be included for operations on the local transaction. |

## Examples

1.  Use start\_local\_transaction to create a local transaction based on the specified partition key value and obtain the ID of the local transaction.

    ```
    # Create a local transaction for the partition key PK0.
    key = [('PK0', 1)]
    # The returned value of the start_local_transaction method is the transaction ID.
    transaction_id = client.start_local_transaction(table_name, key)
    ```

2.  Read and write data within the local transaction.

    Local transaction IDs are used to read and write data within the local transaction, which is the same as the normal read and write operations on data.

    -   Write a row of data.

        ```
        primary_key = [('PK0', 1), ('PK1', 'transaction')]
        attribute_columns = [('value', 'origion value')]
        row = Row(primary_key, attribute_columns)
        condition = Condition(RowExistenceExpectation.IGNORE)
        consumed, return_row = client.put_row(table_name, row, condition)
        ```

    -   Read the row of data.

        ```
        primary_key = [('PK0', 1), ('PK1', 'transaction')]
        columns_to_get = ['value']
        
        consumed, return_row, next_token = client.get_row(
            table_name, primary_key, columns_to_get, None, 1, None, None, None, None, transaction_id
        )
        
        for att in return_row.attribute_columns:
            print ('\tname:%s\tvalue:%s' % (att[0], att[1]))
        ```

3.  Commit or abort a local transaction.
    -   Commit a local transaction for all modified data to take effect.

        ```
        client.commit_transaction(transaction_id)
        ```

    -   Abort a local transaction. Modifications to the data within the local transaction are ignored.

        ```
        client.abort_transaction(transaction_id)
        ```


