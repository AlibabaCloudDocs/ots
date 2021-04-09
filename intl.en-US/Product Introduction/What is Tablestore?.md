# What is Tablestore?

Tablestore is a multi-model data storage service that is developed by Alibaba Cloud. It can store a large amount of structured data and supports fast query and analysis. The distributed storage and powerful index-based search engine enable Tablestore to store petabytes of data while ensuring 10 million transactions per second \(TPS\) and a latency within milliseconds.

## Features

-   Fully managed

    Tablestore is a fully managed storage service for structured data. You need only to focus on business research and development, without worrying about software and hardware presetting, configurations, faults, cluster scale-out, and security. Tablestore ensures high availability of your business while minimizing management and maintenance costs.

-   Seamless scalability

    Tablestore uses shards and load balancing to implement seamless scalability. Tablestore adjusts the size of partitions to store more data. Tablestore can store a minimum of 10 PB of data. A single table can store a minimum of 1 PB of data or 1 trillion records.

-   Powerful query capabilities

    Tablestore supports queries based on secondary indexes and search indexes in addition to primary key-based queries.

    -   Secondary index: predefines a model to distribute data, which makes data query more efficient.
    -   Search index: supports query methods such as Boolean query, fuzzy query, geo query, and full-text search based on inverted indexes and column-oriented storage.
-   High reliability

    Tablestore creates multiple backups of data and stores them in different servers across racks. When a backup fails, the system immediately uses another backup to restore the data. This mechanism ensures service availability of 99.99999999% \(ten 9s\).

-   Strong consistency

    Tablestore ensures consistency among three backups. After data is written, applications can immediately read the written data.

-   Highly concurrent read and write operations

    Tablestore supports tens of millions of concurrent read and write queries per second \(QPS\).


## Quick start

You can use the Tablestore console to get started with Tablestore. For more information, see [Getting started](/intl.en-US/Quick Start/Overview.md).

## Terms

-   time to live \(TTL\)

    A data table attribute measured in seconds. This attribute indicates the validity period of data. For more information, see [Max versions and TTL](/intl.en-US/Function Introduction/Wide Column model/Max versions and TTL.md). To save storage space and minimize storage costs, the Tablestore backend automatically clears data after its TTL expires.

-   region

    A physical data center. Tablestore is deployed across multiple Alibaba Cloud regions. You can select a region as needed. For more information, see [Region](/intl.en-US/Function Introduction/Terms/Region.md).

-   read/write throughput

    A Tablestore attribute that is measured by read and write capacity units \(CUs\). A CU is the basic billing unit for data read and write operations. For more information, see [Read/write throughput](/intl.en-US/Function Introduction/Terms/Read/write throughput.md).


## Implementation modes

-   Console

    Alibaba Cloud provides web pages for you to manage Tablestore. To manage Tablestore instances, log on to the [Tablestore console](https://otsnext.console.aliyun.com/).

-   SDKs

    Alibaba Cloud provides SDKs in various programming languages for you to use Tablestore. For more information, see [SDK overview](/intl.en-US/SDK Reference/SDK overview.md).

-   Tablestore CLI

    Alibaba Cloud provides a command line tool Tablestore CLI for you to manage Tablestore. For more information, see [Tablestore CLI](/intl.en-US/Utilities/Tablestore CLI.md).


## Computing and analysis

You can perform computing and analysis on data in Tablestore.

-   For information about how to combine MaxCompute with Tablestore, see [Use MaxCompute to access Tablestore](/intl.en-US/Compute-Analysis/MaxCompute /Use MaxCompute to access Tablestore.md).

## Data migration for synchronization

You can migrate heterogeneous data to Tablestore without interrupting services. You can also migrate data from Tablestore to services such as OSS.

-   For more information about how to migrate data from Tablestore to OSS, see [Overview](/intl.en-US/Data channels/Data export/Synchronize data from tablestore to OSS/Overview.md).

## Billing

The billing items of Tablestore include data storage usage, reserved read/write throughput, additional read/write throughput, and outbound Internet traffic. For more information, see [Billing overview](/intl.en-US/Pricing/Billing overview.md).

For more information about Tablestore and related resource prices, see [Tablestore Pricing](https://www.alibabacloud.com/product/table-store/pricing).

