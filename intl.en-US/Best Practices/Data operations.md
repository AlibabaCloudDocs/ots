# Data operations

This topic provides you with best practices for data operations.

## Split tables based on access frequency differences among attribute columns

If rows of a table have many attribute columns, but each operation accesses only a portion of these columns, you can split the table into multiple tables. The attribute columns of different access frequencies can be placed into different tables.

For example, in a merchandise management system, rows contain the item quantity, item price, and item description. Item quantities and prices are INTEGER values that consume little storage space. Item descriptions are STRING values that consume more storage space. Item descriptions are modified infrequently because most operations update item quantities and prices without modifying the item descriptions. The table can be split into two tables. One table contains item quantities and prices, and the other contains item descriptions.

## Compress text-based attribute columns

If an attribute column contains a large amount of text, the attribute columns can be compressed and stored as BINARY data in Tablestore. This process saves space and reduces the capacity units consumed by access operations to reduce the cost of Tablestore usage.

## Store attribute column in OSS

Tablestore limits the size of a single attribute column to 2 MB. If you must store a file that exceeds 2 MB, we recommend that you use Alibaba Cloud Object Storage Service \(OSS\).[OSS](https://www.alibabacloud.com/product/oss) is an open storage service provided by Alibaba Cloud to storage a large amount of data. Compared to Tablestore, OSS stores files at a lower unit price. Therefore, OSS is more suitable for storing objects.

If OSS cannot be used, the attribute column whose value is greater than 2 MB can be split into multiple smaller rows, and stored in Tablestore.

## Add error retry intervals

Tablestore may encounter hardware or software problems, which result in some requests of the application to fail and return retries errors. If the request from an application fails and returns a try again error, we recommend that you wait a period of time before you try the request again. As a best practice, randomized or exponentially-increasing backoffs are helpful to avoid an avalanche effect.

