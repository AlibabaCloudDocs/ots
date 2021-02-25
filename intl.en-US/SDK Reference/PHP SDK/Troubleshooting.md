# Troubleshooting

This topic describes the troubleshooting methods in Tablestore SDK for PHP.

## Methods

Tablestore SDK for PHP handles errors as exceptions. The operation is successful if no exceptions are returned for the called operation. If an exception is returned, the operation fails.

**Note:** Batch operations such as BatchGetRow and BatchWriteRow can be called only after the system verifies that no exception is returned and that the status of each row is successful.

## Exceptions

Tablestore SDK for PHP has two types of exceptions: OTSClientException and OTSServerException. Both are inherited from the Exception class.

-   OTSClientException: an internal SDK exception such as an invalid parameter value.
-   OTSServerException: indicates a server error that arises from the resolution of a server error message. OTSServerException has the following components:
    -   getHttpStatus\(\): an HTTP status code such as 200 or 404.
    -   getOTSErrorCode\(\): the error type string returned by Tablestore.
    -   getOTSErrorMessage\(\): the detailed error description returned by Tablestore.
    -   getRequestId\(\): the UUID that identifies a request. If a problem persists, record this request ID and [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd).

