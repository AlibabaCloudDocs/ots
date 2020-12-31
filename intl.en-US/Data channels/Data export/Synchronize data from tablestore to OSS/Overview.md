# Overview

You can use scripts in the Data Integration console to synchronize incremental and full data from Tablestore to OSS.

[Tablestore](https://www.alibabacloud.com/product/table-store) is a distributed NoSQL database service that allows you to store data based on the Apsara distributed system of Alibaba Cloud. Tablestore is designed to provide 99.99% availability and 99.999999999% \(eleven 9's\) data reliability. Tablestore adopts sharding and load balancing technologies to scale out services and handle concurrent transactions. You can use Tablestore to store and query large amounts of structured data in real time.

[Object Storage Service \(OSS\)](https://www.alibabacloud.com/product/oss) is a massive-volume, secure, low-cost, and highly-reliable cloud storage service. It provides 99.99999999% data reliability. You can use RESTful API for storage and access in any place on the Internet. Its capacity and processing capability can be elastically scaled, and multiple storage modes are provided, comprehensively optimizing the storage cost.

## Scenarios

Tablestore: Provides professional data-persistent storage service and user-oriented real-time read/write operations with high concurrency and low latency.

OSS: Supports backup at an extremely low cost.

## Usage

-   Write

    Data can be directly written to Tablestore.

-   Read

    Data can be directly read from Tablestore.

-   Backup

    Automatic backup is supported.

-   Restoration

    Data can be re-written to Tablestore by using Data Integration \(OSSReader and OTSWriter\).


## Limits

-   Write by whole rows

    Tablestore Stream requires that a whole row of data be written to Tablestore each time. Currently, the whole-row data write mode is applied to the writing of time sequence data such as IoT data. Therefore, data cannot be modified subsequently.

-   Synchronization latency

    Currently, periodic scheduling is used and the scheduling interval is 5 minutes. The plugin has a latency of 5 minutes and the total latency of a synchronization task is 5 to 10 minutes.


## Activation

-   [Activate Tablestore](/intl.en-US/Quick Start/Activate Tablestore.md)
-   [Activate OSS](/intl.en-US/Console User Guide/Sign up for OSS.md)

## Data tunnel

Offline

-   Export the full data to OSS.

    -   [Script mode](/intl.en-US/Data channels/Data export/Synchronize data from tablestore to OSS/Full export (script mode).md)
-   Synchronize data to OSS in incremental mode.

    -   [Script mode](/intl.en-US/Data channels/Data export/Synchronize data from tablestore to OSS/Incremental synchronization (script mode).md)
-   Fully import data into Tablestore.

    -   Script mode

