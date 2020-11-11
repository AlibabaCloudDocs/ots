# Troubleshooting

This topic describes the troubleshooting methods in Tablestore SDK for Java.

## Methods

Tablestore SDK for Java handles errors as exceptions. The operation is successful if no exceptions are returned. If an exception is returned, the operation fails.

**Note:** Batch operations such as BatchGetRow and BatchWriteRow can be called only after the system verifies that no exception is returned and that the status of each row is successful.

## Exceptions

ClientException and TableStoreException are common exceptions you may encounter when you use Tablestore SDK for Java. Both are inherited from RuntimeException.

-   ClientException: an internal SDK exception such as an invalid parameter value.
-   TableStoreException: a server error parsed from a server error message. TableStoreException provides you with the following information:

    -   getHttpStatus\(\): an HTTP status code such as 200 or 404.
    -   getErrorCode\(\): an error type string returned by Tablestore.
    -   getRequestId\(\): the UUID that identifies a request. If a problem persists, record this request ID and [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd).

## Retry

-   Tablestore SDK for Java automatically retries operations in which an error occurs. In the default retry policy, the maximum retry period is 10s. Retries are performed for throttling errors and internal server errors related to write operations.
-   You can also customize a retry policy by inheriting the RetryStrategy class, and configure the custom retry policy when you create an OTSClient object.

Tablestore SDK for Java provides the following retry policies:

-   DefaultRetryStrategy: the default retry policy. Only read operations are retried. The retry interval increases exponentially from 10 ms and the maximum retry period is 10s.
-   AlwaysRetryStrategy: All operations are retried. A maximum of three retry attempts can be made. The retry interval increases exponentially from 4 ms, and the maximum retry interval is 1s.

