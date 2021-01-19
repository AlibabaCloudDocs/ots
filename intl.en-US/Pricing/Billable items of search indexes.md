# Billable items of search indexes

This topic describes the billable items and billing formulas of search indexes. Search index-based queries require extra space to store indexed data and consume read throughput.

**Note:**

-   Billable items of indexes are independent of data tables.
-   The price of each billable item of a search index is the same as that of a high-performance instance.

## Billable items

|Billable item|Billing method|Description|
|-------------|--------------|-----------|
|Data size|Pay-as-you-go

|Unit: GB. Billed size is rounded up to the next GB.

Tablestore charges you for the total volume of indexed data on an hourly basis. The utilization of system resources varies with field types and index types. Therefore, indexed data is billed based on the volume of data compressed after you create indexes. The raw data volume in a data table does not affect this billing.|
|Read throughput|Reserved Read Throughput|Pay-as-you-go

|Unit: [CU](/intl.en-US/Function Introduction/Terms/Read/write throughput.md).

Tablestore specifies a reserved read throughput based on the indexed data size. The charges of reserved read throughput are calculated based on the following operations:

-   When you create a search index, Tablestore reads data from a data table, which consumes read throughput.
-   Tokenization during the creation of a search index also consumes read throughput. Fees incurred from tokenization are included in the fees for the reserved read throughput.
-   To ensure index and query performance, some indexed data is loaded to the memory in advance and remains in memory. Such data consumes read throughput. Fees incurred from these operations are also included in the fees for the reserved read throughput.

The minimum fees are based on the reserved read throughput value. For example, assume that the reserved read throughput is 10,000 CUs for an index. Each index query reads 10 rows, and the size of each row is less than 4 KB. When the number of queries per second \(QPS\) is less than 1,000, the actual consumed read throughput is less than the reserved read throughput, and no extra fees are charged.

Calculation of reserved read throughput: The reserved read throughput is proportional to the size and number of rows of the indexed data. For example, 1 GB or 2 million rows of indexed data corresponds to a reserved read throughput of 10 CUs. When the reserved read throughput values corresponding to the data size and the number of rows are different, the system uses the larger one as the reserved read throughput.

**Note:**

-   The maximum reserved read throughput for Tablestore is 100,000 CUs.
-   When the data size is less than 200 MB and the number of rows is less than 400,000, the reserved read throughput for Tablestore must be 20 CUs, which is suitable for tests that involve a small amount of data. When the data size is greater than or equal to 200 MB or the number of rows is greater than or equal to 400,000, the reserved read throughput must be greater than 100 CUs. |
|Additional throughput|Pay-as-you-go

|The portion of the actual consumed read throughput that exceeds the reserved read throughput is charged as additional read throughput. Unit: [CU](/intl.en-US/Function Introduction/Terms/Read/write throughput.md). |
|Internet outbound traffic|Pay-as-you-go|Fees for Internet outbound traffic. Unit: GB.|

## Billing formulas

The following table describes how to calculate the data size and read throughput of a search index.

|Billable item|Formula|Description|
|-------------|-------|-----------|
|Data size|![fig_tablestore_en_001](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5247958951/p100047.jpg)

|The Size variable indicates the size of the compressed indexed data.|
|Read throughput|Reserved read throughput per index:

![reservedCU](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6247958951/p63276.png)

Read throughput per query:

![cuperquery](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6247958951/p63277.png)

|-   The Size variable indicates the size of the compressed indexed data.
-   The Rows variable indicates the total number of rows in an index, not including the child rows in nested queries.
-   The ReturnRowSize variable indicates the size of returned rows.
-   The ReturnRowCount variable indicates the number of returned rows. |

## Billing examples

|Storage|Number of rows|Billing|
|:------|:-------------|:------|
|8 GB|9 million|-   Storage fees: 8 GB × USD 0.00030/GB/Hour = USD 0.0024/Hour
-   Calculation of reserved read throughput: 8 GB of data corresponds to 80 CUs and 9 million rows correspond to 45 CUs.

Fees of reserved read throughput: 80 CUs × USD 0.0002/CU/Hour = USD 0.016/Hour

-   Total fees: USD 0.0024/Hour + USD 0.016/Hour = USD 0.0184/Hour

The portion of actual consumed read throughput that exceeds the reserved read throughput is charged as additional read throughput. The billing method of Internet outbound traffic is the same as that of the data table. |
|100 GB|300 million|-   Storage fees: 100 GB × USD 0.00030/GB/Hour = USD 0.03/Hour
-   Calculation of reserved read throughput: 100 GB of data corresponds to 1,000 CUs and 300 million rows correspond to 1,500 CUs. The reserved read throughput is 1,500 CUs.

Fees of reserved read throughput: 1,500 CUs × USD 0.0002/CU/Hour = USD 0.3/Hour

-   Total fees: USD 0.03/Hour + USD 0.3/Hour = USD 0.33/Hour

The portion of actual consumed read throughput that exceeds the reserved read throughput is charged as additional read throughput. The billing method of Internet outbound traffic is the same as that of the data table. |
|30 TB|10 billion|-   Storage fees: 30,000 GB × USD 0.00030/GB/Hour = USD 9/Hour
-   Calculation of reserved read throughput: 30 TB of data corresponds to 300,000 CUs and 10 billion rows correspond to 50,000 CUs. Although the former CU value is larger, it exceeds the maximum reserved read throughput of 100,000 CUs. Therefore, the reserved read throughput is 100,000 CUs.

Fees of reserved read throughput: 100,000 CUs × USD 0.0002/CU/Hour = USD 20/Hour

-   Total fees: USD 9/Hour + USD 20/Hour = USD 29/Hour

The portion of actual consumed read throughput that exceeds the reserved read throughput is charged as additional read throughput. The billing method of Internet outbound traffic is the same as that of the data table. |

**Note:** The price described in the preceding table is for reference only. For more information, see the Tablestore console.

