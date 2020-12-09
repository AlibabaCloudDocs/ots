# Search index limits

This topic describes the limits of search index.

## Mapping

|Item|Maximum value|Description|
|----|-------------|-----------|
|Number of index fields|500|The maximum number of fields that can be indexed.|
|Array length|256|The maximum number of elements in an array.|
|Number of fields for EnableSortAndAgg|100|The maximum number of sort and aggregate fields.|
|Number of nested levels|5|A maximum of five levels can be nested.|
|Number of child rows in a single nested field|256|The maximum number of rows that can be included within a single nested field.|
|Number of nested fields|25|The maximum number of fields that can be nested.|
|Total length of primary key columns|1000 Byte|The total length of strings in all primary key columns of each row can be up to 1,000 bytes.|
|Total length of strings in primary key columns|1000 Byte|The total length of strings in all primary key columns of each row can be up to 1,000 bytes.|
|Total length of strings \(indexed as KEYWORD\) in all attribute columns|4 KB|None.|
|Total length of strings \(indexed as TEXT\) in all attribute columns|2 MB|Same as the length limit on attribute columns in tables.|
|String length of a query that contains wildcards|10|The string of a query that contains wildcards can be up to 10 characters in length.|

## Search

|Category|Item|Maximum value|Description|
|--------|----|-------------|-----------|
|General limits|offset+limit|10000|To query more than 10,000 rows, you must specify the next\_token parameter.|
|limit|100|-   When you call the SearchIndex operation to query the data of a specified column, you can set the limit parameter to a value of up to 1000 if the search index contains the data.
-   If your business requirements are not met due to the limit, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd). |
|timeout|10s|None.|
|CU|100000|-   This limit does not apply to scanning and analysis requests.
-   If your business requirements are not met due to the limit, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd). |
|QPS|100000|-   The upper limit for lightweight transaction processing is 100,000 queries per second \(QPS\).
-   If your business requirements are not met due to the limit, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd). |
|Aggregation|Number of Aggregations at the same level|5|The number of Aggregations is recalculated each time you add a new Aggregation to SubGroupBy.|
|Number of GroupBys at the same level|5|The number of GroupBys is recalculated each time you add a new GroupBy to SubGroupBy.|
|Number of nested levels for a GroupBy|3|The root GroupBy is also calculated as a nested level.|
|Number of filters in a GroupByFilter|10|None.|
|Number of groups returned by a GroupByField|2000|None.|
|Number of Ranges in a GroupByRange|100|None.|
|Number of Ranges in a GroupByGeodistance|10|None.|

## ParallelScan

|Category|Item|Description|
|--------|----|-----------|
|General limits|offset+limit|You cannot specify the offset and limit parameters for paging when you use parallel scan. Returned results are displayed sequentially.|
|limit|The maximum value is 2000.|
|CU|Unlimited.|
|QPS|Unlimited.|
|Maximum number of concurrent tasks|The value of MaxParallel in the response to the ComputeSplits operation.|

## Index

|Item|Maximum value|Description|
|:---|:------------|:----------|
|Rate|50,000 rows/s|-   When data is written to a table for the first time or when a large amount of data is required to be written in a short span of time, it can take several minutes for Tablestore to perform load balancing.
-   The rate of indexing based on the TEXT field is limited to 10,000 rows per second because this indexing consumes more CPU resources for tokenization.
-   If your business requirements are not met due to the limit, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd). |
|Synchronization latency|10s|-   When the writing rate is steady, synchronization latency is less than 10 seconds.
-   In most cases, the synchronization latency is less than one minute.
-   It can take up to one minute to initialize a new index. |
|Number of rows|50 billion|If your business requirements are not met due to the limit, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd).|
|Total size|50 TB|If your business requirements are not met due to the limit, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd).|

## Additional limits

|Item|Value|
|:---|:----|
|Supported region|China \(Hangzhou\), China \(Shanghai\), China \(Beijing\), China \(Zhangjiakou\), China \(Shenzhen\), China \(Hong Kong\), Singapore, UK \(London\), US \(Silicon Valley\), US \(Virginia\), and India \(Mumbai\)|
|Region to support|Australia \(Sydney\)|

**Note:** If your business requirements are not met due to these limit, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd) on the Alibaba Cloud official website. Describe the scenario, items, requirements, and reasons in the ticket. Your requirements will be taken into consideration during future development.

