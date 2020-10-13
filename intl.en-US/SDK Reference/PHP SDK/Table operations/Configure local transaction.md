# Configure local transaction

The local transaction feature is available for invitational preview and is disabled by default. To use local transactions, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd) to apply for a trail.

You can use local transaction to specify that the operations on a partition key are atomic, which indicates that operations based on the specified partition key either succeed or fail. The isolation level of the local transaction is serializable.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A data table is created. Data is written to the table.

## Procedure

To implement a local transaction, perform the following operations:

1.  Use startLocalTransaction to create a local transaction based on the specified partition key value and obtain the ID of the local transaction.
2.  Read and write data within the local transaction.

    You can call the following operations on the local transaction: GetRow, PutRow, DeleteRow, UpdateRow, BatchWriteRow, and GetRange.

3.  Use commitTransaction to commit the local transaction or use abortTransaction to abort the local transaction.

## Limits

-   The validity period of a local transaction is up to 60 seconds.

    If a transaction is not committed or aborted within 60 seconds, the server of Tablestore determines that the transaction times out and aborts it.

-   A transaction may be created in the server side of Tablestore even if a timeout error is returned. In this case, you can resend a transaction creation request only after the created transaction times out.
-   If an uncommitted transaction becomes invalid, retry the operation within this transaction.
-   Tablestore imposes the following limits on read and write operations on data within a local transaction:
    -   The transaction ID cannot be used to access data beyond the range specified based on the partition key value that is used to create the transaction.
    -   The partition key values of all write requests in the same transaction must be the same as the partition key value used to create the transaction. This limit does not apply to read requests.
    -   A local transaction can be used by only one request at a time. When the transaction is in use, other operations that use the transaction ID fail.
    -   The maximum interval for read and write operations on a transaction is 60 seconds.

        If a transaction is not read or written for more than 60 seconds, the server of Tablestore determines that the transaction times out and aborts it.

    -   Up to 4 MB of data can be written to each transaction. The volume of written data to each transaction is calculated in the same way as a regular write request.
    -   If you do not specify a version for a cell, the server of Tablestore assigns a version to the cell in the usual way when the cell is written into the transaction \(rather than when the transaction is committed\).
    -   If a BatchWriteRow request includes a transaction ID, all rows in the request can only be written to the table that matches the transaction ID.
    -   When you use a transaction, the data within the range specified based on the corresponding partition key value is locked. Write requests on data within the transaction but without the transaction ID are rejected. The data in the transaction is unlocked when the transaction is committed or aborted, or when the transaction times out.
    -   A transaction remains valid even if a read or write request with the transaction ID is rejected. You can resend the request in the same way as a regular request or abort the transaction.

## API operations

-   startLocalTransaction

    ```
        /**
         * Create a local transaction and obtain the local transaction ID.
         * @api
         * @param [] $request
         *            The request parameters, including the table name and partition key.
         * @return [] Response.
         * @throws OTSClientException The exception that is returned when a parameter error occurs or the Tablestore server returns a verification error.
         * @throws OTSServerException The exception that is returned when the Tablestore server returns an error.
         * @example "src/examples/StartLocalTransaction.php" 50
         */
        public function startLocalTransaction(array $request)
    ```

-   commitTransaction

    ```
        /**
         * Commit a local transaction.
         * @api
         *
         * @param [] $request
         *          The request parameter, which is the transaction ID.
         * @return [] Response.
         * @throws OTSClientException The exception that is returned when a parameter error occurs or the Tablestore server returns a verification error.
         * @throws OTSServerException The exception that is returned when the Tablestore server returns an error.
         * @example "src/examples/CommitTransaction.php" 50
         */
        public function commitTransaction(array $request)
    ```


-   abortTransaction

    ```
        /**
         * Abort a local transaction.
         * @api
         *
         * @param [] $request
         *          The transaction ID, which is the request parameter.
         * @return [] Response.
         * @throws OTSClientException The exception that occurs when a parameter error occurs or the Tablestore server returns a verification error.
         * @throws OTSServerException The exception that occurs when the Tablestore server returns an error.
         * @example "src/examples/AbortTransaction.php" 20
         */
        public function abortTransaction(array $request)
    ```


## Examples

1.  Use startLocalTransaction to create a local transaction based on the specified partition key value and obtain the ID of the local transaction.

    ```
    $response = $this->otsClient->startLocalTransaction (array (
        'table_name' => 'TransactionTable',
        'key' => array(  // Primary keys: [PK0:INTEGER,PK1:STRING]
            array('PK0', 0)
        )
    ));
    ```

2.  Read and write data within the local transaction.

    Local transaction IDs are used to read and write data within the local transaction, which is the same as the normal read and write operations on data.

    ```
    $updateRequest = array(
        'table_name' => 'TransactionTable',
        'condition' => RowExistenceExpectationConst::CONST_IGNORE,
        'primary_key' => array (
            array('PK0', 0),
            array('PK1', '1')
        ),
        'update_of_attribute_columns'=> array(
            'PUT' => array (
                array('attr0', 'new value')
            )
        ),
        'transaction_id' => $response['transaction_id']
    );
    $this->otsClient->updateRow($updateRequest);
    ```

3.  Commit or abort the transaction.
    -   Commit a local transaction for all modified data to take effect.

        ```
        $this->otsClient->commitTransaction(array(
            'transaction_id' => $response['transaction_id']
        ));
        ```

    -   Abort a local transaction. Modifications to the data within the local transaction are ignored.

        ```
        $this->otsClient->abortTransaction(array(
            'transaction_id' => $response['transaction_id']
        ));
        ```


