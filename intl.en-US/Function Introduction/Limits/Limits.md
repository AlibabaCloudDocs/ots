# Limits

This topic describes the limits of Tablestore. You can tailor table schemas and row sizes to improve performance.

## Limits on instances

|Item|Limit|Description|
|----|-----|-----------|
|Number of instances created by an Alibaba Cloud account|10|If your business requirements are not met due to the limit, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd). |
|Number of tables in an instance|64|The number of base tables and index tables. If your business requirements are not met due to the limit, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd). |
|Length of an instance name|3~16 Bytes|The name of an instance can contain letters, digits, and hyphens \(-\). The name must start with a letter and cannot end with a hyphen \(-\). The name of an instance cannot contain the following strings: ali, ay, ots, taobao, or admin. |

## Limits on tables

|Item|Limit|Description|
|----|-----|-----------|
|Length of a table name|1~255 Bytes|The name of a table can contain letters, digits, and underscores \(\_\). The name must start with a letter or an underscore \(\_\).|
|Number of reserved read and write CUs of a single table|0~100000|If your business requirements are not met due to the limit, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd). |

## Limits on columns

|Item|Limit|Description|
|----|-----|-----------|
|Length of a column name|1~255 Bytes|The name of a column can contain letters, digits, and underscores \(\_\). The name must start with a letter or an underscore \(\_\).|
|Number of columns contained in a primary key|1~4|A primary key can contain one to four primary key columns.|
|Size of the value in a STRING primary key column|1 KB|The size of the value in a STRING primary key column cannot exceed 1 KB.|
|Size of the value in a STRING attribute column|2 MB|The size of the value in a STRING attribute column cannot exceed 2 MB.|
|Size of the value in a BINARY primary key column|1 KB|The size of the value in a BINARY primary key column cannot exceed 1 KB.|
|Size of the value in a BINARY attribute column|2 MB|The size of the value in a BINARY attribute column cannot exceed 2 MB.|

## Limits on rows

|Item|Limit|Description|
|----|-----|-----------|
|Number of attribute columns in a single row|None|Tablestore does not impose limits on the number of attribute columns in a single row.|
|Size of a single row|None|Tablestore does not impose limits on the total size of column names and column values for a row.|

## Limits on operations

|Item|Limit|Description|
|----|-----|-----------|
|Number of attribute columns written by one request|1,024|During a PutRow, UpdateRow, or BatchWriteRow operation, the number of attribute columns written to a row cannot exceed 1024.|
|Number of columns specified in columns\_to\_get in a read request|0~128|The maximum number of columns obtained from a row of data in a read request cannot exceed 128.|
|Queries per second \(QPS\) at the table level|10|The QPS for a table cannot exceed 10. For more information about operations performed on tables, see [OperationsSummary](/intl.en-US/API Reference/Operations/OperationsSummary.md).|
|Count of UpdateTable operations on a single table|Upper limit: unlimited Lower limit: unlimited

|The limit on the count of UpdateTable operations for a table follows the limit on the frequency of adjustment for a table.|
|Frequency of UpdateTable operations for a table|Once every two minutes|The reserved read and write throughput for a table can be adjusted once every two minutes at most.|
|Number of rows read by one BatchGetRow request|100|None.|
|Number of rows written by one BatchWriteRow request|200|None.|
|Size of data written by one BatchWriteRow request|4 MB|None.|
|Amount of data scanned by one GetRange request|5,000 rows or 4 MB|The amount of data returned by a request cannot exceed 5,000 rows or 4 MB. When one of the limits is exceeded, data that exceeds the limits is truncated at the row level. The primary key information of the next row of data is returned.|
|Data size of an HTTP request body|5 MB|None.|
|Number of filters in one request|10|None.|

