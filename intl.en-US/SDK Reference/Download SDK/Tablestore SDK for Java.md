# Tablestore SDK for Java

This topic describes the version history of Tablestore SDK for Java.

**Note:** Tablestore SDK for Java V4.0.0 and later support max versions and time to live \(TTL\). Both of these parameters are incompatible with Tablestore SDK for Java V2.x.x.

## Version: 5.10.3

Release date: October 26, 2020

Download URL: [5.10.3](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/java/tablestore-5.10.3-release.zip)

Change records:

-   Delivery service is added. For more information about how to use the console or Tablestore SDKs, see [Quick start](/intl.en-US/Function Introduction/Data Delivery/Quick start.md) or [Use SDKs](/intl.en-US/Function Introduction/Data Delivery/Use SDKs.md).
-   Aggregation that queries rows in groups is supported for search index. For more information, see [Aggregation](/intl.en-US/Function Introduction/Search Index/Operations/Aggregation.md).
-   Format check for configuration parameters is added.
-   The SDK is optimized and connected to the computing engine.

## Version: 5.7.0

Release date: April 7, 2020

Download URL: [5.7.0](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/java/tablestore-5.7.0-release.zip)

Change records:

-   Parallel scan is supported for search index. For more information, see [Parallel scan](/intl.en-US/Function Introduction/Search Index/Operations/Parallel scan.md).
-   TableStoreWriter is optimized for Tablestore SDK for Java.

## Version: 5.4.0

Release date: November 18, 2019

Download URL: [5.4.0](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/java/tablestore-5.4.0-release.zip)

Change records:

-   Predefined columns can be added to or deleted from existing tables.
-   Tunnel Service is optimized for Tablestore SDK for Java.

## Version: 5.3.0

Release date: September 10, 2019

Download URL: [5.3.0](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/java/tablestore-5.3.0-release.zip?spm=a2c4g.11186623.2.28.192d2d66COWK0Y&file=tablestore-5.3.0-release.zip)

Change records: Aggregation can be performed on data when search index is used.

## Version: 5.1.0

Release date: July 1, 2019

Download URL: [5.1.0](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/java/tablestore-5.1.0-release.zip)

Change records: Additional tokenization methods are supported in search index.

## Version: 5.0.0

Release date: June 10, 2019

Download URL: [5.0.0](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/java/tablestore-5.0.0-release.zip)

Change records:

-   To unify SDK version numbers, Tablestore SDK for Java is updated to 5.x. Tablestore SDK for Java V5.x is compatible with Tablestore SDK for Java V4.x.
-   Exists query is added to search index.

## Version: 4.12.1

Release date: May 8, 2019

Download URL: [4.12.1](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/java/tablestore-4.12.1-release.zip)

Change records: Timeline model V2 is added.

## Version: 4.10.2

Release date: March 11, 2019

Download URL: [tablestore-4.10.2](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/108096/cn_zh/1552274752739/tablestore-4.10.2-release.zip)

Change records:

-   Remove log4j2 implement and log4j2.xml.
-   TunnelWorker: fix auto retry logic when network condition is too bad.

## Version: 4.7.4

Release date: September 27, 2018

Download URL: [tablestore-4.7.4-release.zip](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/86690/cn_zh/1540433142084/tablestore-4.7.4-release.zip)

Change records:

-   The following features are added to search index:
    -   Boolean query
    -   Range query
    -   Wildcard query
    -   Nested query
    -   Full-text search
    -   Sorting
-   Global secondary index is added.

## Version: 4.0.0

Release date: August 1, 2016

Download URL: [aliyun\_tablestore\_java\_sdk\_4.0.0.zip](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/43005/cn_zh/1470024227102/aliyun_tablestore_java_sdk_4.0.0.zip)

Change records:

-   The time to live \(TTL\) feature is added. For more information, see [Max versions and TTL](/intl.en-US/Function Introduction/Wide Column model/Max versions and TTL.mdsection_cv2_kyf_cfb).
-   Max versions is supported. For more information, see [Max versions and TTL](/intl.en-US/Function Introduction/Wide Column model/Max versions and TTL.md).

## Version: 2.2.4

Release date: May 12, 2016

Download URL: [aliyun\_tablestore\_java\_sdk\_2.2.4.zip](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/31713/cn_zh/1463131862644/aliyun-ots-java-sdk-2.2.4.zip)

Change records:

-   Condition update is added.
-   The filter feature is supported.

## Version: 2.1.0

Release date: November 12, 2015

Download URL: [aliyun-ots-java-sdk-2.1.0.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-ots-java-sdk-2.1.zip)

Change records:

-   Asynchronous network transmission and performance tuning: QPS is increased by several times when the CPU utilization is the same.
-   Flexible and easy-to-use asynchronous operations: Callback is introduced and Future is returned simultaneously.
-   Content related to OSS SDK is removed: The new version of Tablestore SDK for Java includes code related only to Tablestore SDK. The directory structure is fine-tuned.
-   Retry logic optimization: The default retry logic is optimized. Retry attempts are allowed for single-row errors in batch operations.
-   Log optimization: Detailed log records from when requests are sent to when requests are received. Log records of slow requests are provided. Trace IDs can be used to view the comprehensive log records of the communications between Tablestore SDK for Java and the backend service.
-   The OTSWriter plug-in is supported to import multiple data: delivers easy-to-use and efficient data import experience.
-   Other improvements: Tools and functions that support a variety of data types are provided. The operation used to calculate data sizes is provided.

**Note**:

Tablestore SDK for Java V2.1.0 and V2.0.4 have the following incompatibilities:

-   The import path for several classes must be modified after the later version of Tablestore SDK for Java is used. Packages of several classes are modified. For example, the package of ClientConfiguration is changed from com.aliyun.openservices to com.aliyun.openservices.ots. These packages are modified because Tablestore SDK is separated from OSS SDK. The classes shared by the both SDKs must also be in the packages of Tablestore.
-   If you no longer use an OTSClient instance in some scenarios, for example, before the program exits, you must call the shutdown method of OTSClient to release all threads and connection resources of the OTSClient instance.
-   Names of some configuration items of ClientConfiguration are adjusted. For example, time units are added to the names.
-   Adjustments are made to dependencies of earlier Tablestore SDK for Java. For example, HttpAsyncClient and Joda-Time are used. If you encounter problems when you run the program, check whether imported dependencies conflict with the existing dependencies.

## Version: 2.0.4

Release date: September 25, 2015

Download URL: [aliyun-ots-java-sdk-2.0.4.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-openservices-OTS-2.0.4.zip)

