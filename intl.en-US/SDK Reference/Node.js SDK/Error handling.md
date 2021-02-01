# Error handling

This topic describes the error handling methods and retry policies of Tablestore SDK for Node.js.

## Methods

Tablestore SDK for Node.js handles errors as exceptions. If the operation succeeds, the called operation does not return an exception. If the operation fails, an exception is returned.

**Note:** Batch operations such as BatchGetRow and BatchWriteRow can be called only after the system verifies that no exception is returned and that the status of each row is successful.

## Exceptions

All errors are handled by Tablestore SDK for Node.js in a unified manner, and are returned as the value of the err parameter by using the callback method. Before you obtain the returned data, you must check whether the err parameter has a value. If an error occurs at the server side of Tablestore, the request ID is returned. The request ID is the UUID that identifies a request. If a problem persists, record this request ID and[submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd).

## Retry

Tablestore SDK for Node.js automatically retries operations in which an error occurs. In the default retry policy, the maximum retry attempts is 20, and the maximum retry interval is 3,000 ms. For more information about the retry policies for throttling errors and internal server errors related to read operations, see tablestore/lib/retry.js.

