# Troubleshooting

This topic describes the error handling methods and retry policies in Tablestore SDK for Python.

## Methods

Tablestore SDK for Python handles errors as exceptions. If the operation succeeds, the called operation does not return an exception. If the operation fails, an exception is returned.

**Note:** Batch operations such as BatchGetRow and BatchWriteRow can be called only after the system verifies that no exception is returned and that the status of each row is successful.

## Exceptions

Tablestore SDK for Python has two types of exceptions: OTSClientError and OTSServiceError. Both are inherited from Exception.

-   OTSClientError: an internal SDK exception, such as an invalid parameter value or a failure to parse the results returned by the server.
-   OTSServiceError: a server error. When a server error occurs, the server parses and returns the error message to the client. OTSServiceError has the following components:
    -   get\_http\_status: an HTTP status code such as 200 and 404.
    -   get\_error\_code: an error type string returned by Tablestore.
    -   get\_error\_message: an error message string returned by Tablestore.
    -   get\_request\_id: the UUID that identifies a request. If a problem persists, record this request ID and[submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd).

## Retry

-   Tablestore SDK for Python automatically retries an operation when an error occurs. In the default retry policy, the maximum retry attempts is 20, and the maximum retry interval is 3,000 ms. For more information about the retry policies for throttling errors and internal server errors related to write operations, see tablestore/retry.py.
-   You can also customize a retry policy by inheriting the RetryPolicy class, and pass in the custom retry policy when you construct an OTSClient object.

Tablestore SDK for Python provides the following retry policies:

-   DefaultRetryPolicy: the default retry policy. Only read operations are retried. The maximum number of retry attempts is 20, and the maximum retry interval is 3,000 ms.
-   NoRetryPolicy: No operation is retried.
-   NoDelayRetryPolicy: a retry policy without delay. Exercise caution when you configure this policy.
-   WriteRetryPolicy: The write operations are retried based on the default retry policy.

