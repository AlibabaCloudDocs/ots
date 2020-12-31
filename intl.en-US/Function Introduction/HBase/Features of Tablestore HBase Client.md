# Features of Tablestore HBase Client

This topic describes the features and operations supported by Tablestore HBase Client.

## Differences between API operations supported by Tablestore and HBase

As a NoSQL database service, Tablestore hides infrastructure details such as table splitting, Dump, Compact, and Region Server. You need only to pay attention to data usage. Tablestore HBase Client and HBase are similar in terms of data model and features, but they have different operations.

## Features supported by Tablestore HBase Client operations

-   CreateTable

    Tablestore does not support ColumnFamily and all data can be considered to be in the same ColumnFamily. This means that TTL and Max Versions of Tablestore are at the table-level. Therefore, Tablestore supports the following features:

    |Features|Supported or Not|
    |:-------|:---------------|
    |family max version|Table-level Max Versions supported. Default value: 1|
    |family min version|Not supported|
    |family ttl|Table-level TTL supported|
    |is/set ReadOnly|Supported by using the sub-account of RAM|
    |Pre-partitioning|Not supported|
    |blockcache|Not supported|
    |blocksize|Not supported|
    |BloomFilter|Not supported|
    |column max version|Not supported|
    |cell ttl|Not supported|
    |Control parameter|Not supported|

-   Put

    |Features|Supported or Not|
    |:-------|:---------------|
    |Writes multiple columns of data at a time|Supported|
    |Specifies a timestamp|Supported|
    |Uses the system time by default if no timestamp is specified|Supported|
    |Single-row ACL|Not supported|
    |ttl|Not supported|
    |Cell Visibility|Not supported|
    |tag|Not supported|

-   Get

    Tablestore guarantees high data consistency. If the HTTP 200 status code \(OK\) is returned after you perform a write operation, the data is permanently written to all copies, and can be immediately read by Get.

    |Features|Supported or Not|
    |:-------|:---------------|
    |Reads a row|Supported|
    |Reads all columns in a ColumnFamily|Supported|
    |Reads data from a specified column|Supported|
    |Reads data that has a specified timestamp|Supported|
    |Reads data of a specified number of versions|Supported|
    |TimeRange|Supported|
    |ColumnfamilyTimeRange|Not supported|
    |RowOffsetPerColumnFamily|Supported|
    |MaxResultsPerColumnFamily|Not supported|
    |checkExistenceOnly|Not supported|
    |closestRowBefore|Supported|
    |attribute|Not supported|
    |cacheblock:true|Supported|
    |cacheblock:false|Not supported|
    |IsolationLevel:READ\_COMMITTED|Supported|
    |IsolationLevel:READ\_UNCOMMITTED|Not supported|
    |IsolationLevel:STRONG|Supported|
    |IsolationLevel:TIMELINE|Not supported|

-   Scan

    Tablestore guarantees high data consistency. If the HTTP 200 status code \(OK\) is returned after you perform a write operation, the data is permanently written to all copies, which can be immediately read by Scan.

    |Features|Supported or Not|
    |:-------|:---------------|
    |Determines a scanning range based on the specified start and stop|Supported|
    |Globally scans data if no scanning range is specified|Supported|
    |prefix filter|Supported|
    |Reads data using the same logic as Get|Supported|
    |Reads data in reverse order|Supported|
    |caching|Supported|
    |batch|Not supported|
    |maxResultSize, which indicates the maximum size of the returned data volume|Not supported|
    |small|Not supported|
    |batch|Not supported|
    |cacheblock:true|Supported|
    |cacheblock:false|Not supported|
    |IsolationLevel:READ\_COMMITTED|Supported|
    |IsolationLevel:READ\_UNCOMMITTED|Not supported|
    |IsolationLevel:STRONG|Supported|
    |IsolationLevel:TIMELINE|Not supported|
    |allowPartialResults|Not supported|

-   Batch

    |Features|Supported or Not|
    |:-------|:---------------|
    |Get|Supported|
    |Put|Supported|
    |Delete|Supported|
    |batchCallback|Not supported|

-   Delete

    |Features|Supported or Not|
    |:-------|:---------------|
    |Deletes a row|Supported|
    |Deletes all versions of the specified column|Supported|
    |Deletes the specified version of the specified column|Supported|
    |Deletes the specified ColumnFamily|Not supported|
    |Specifies a timestamp to delete the versions that are equal to the timestamp|Supported|
    |Specifies a timestamp and use deleteFamily and deleteColumns to delete the versions that are earlier than or equal to the timestamp|Not supported|
    |Uses deleteColumn to delete the latest version without specifying a timestamp|Not supported|
    |Uses deleteFamily and deleteColumns to delete the version of the current system time without specifying a timestamp|Not supported|
    |addDeleteMarker|Not supported|

-   checkAndXXX

    |Features|Supported or Not|
    |:-------|:---------------|
    |CheckAndPut|Supported|
    |checkAndMutate|Supported|
    |CheckAndDelete|Supported|
    |Checks whether the value of a column meets the conditions. If yes, checkAndXXX deletes the column.|Supported|
    |Uses the default value if no value is specified|Supported|
    |Checks row A and executes row B.|Not supported|

-   exist

    |Features|Supported or Not|
    |:-------|:---------------|
    |Checks whether one or more rows exist and does not return any content|Supported|

-   Filter

    |Features|Supported or Not|
    |:-------|:---------------|
    |ColumnPaginationFilter|columnOffset and count not supported|
    |SingleColumnValueFilter|Supported: LongComparator, BinaryComparator, and ByteArrayComparable

 Not supported: RegexStringComparator, SubstringComparator, and BitComparator |


## Operations not supported by Tablestore HBase Client

-   Namespaces

    Tablestore uses instances to manage a data table. An instance is the minimum billing unit in Tablestore. You can manage instances in the [Tablestore console](https://ots.console.aliyun.com). Therefore, the following features are not supported:

    -   createNamespace\(NamespaceDescriptor descriptor\)
    -   deleteNamespace\(String name\)
    -   getNamespaceDescriptor\(String name\)
    -   listNamespaceDescriptors\(\)
    -   listTableDescriptorsByNamespace\(String name\)
    -   listTableNamesByNamespace\(String name\)
    -   modifyNamespace\(NamespaceDescriptor descriptor\)
-   Region management

    Partition is the basic unit for data storage and management in Tablestore. Tablestore automatically splits or merges the partitions based on their data volumes and access conditions. Therefore, Tablestore does not support features related to Region management in HBase.

-   Snapshots

    Tablestore does not support Snapshots, or related features of Snapshots.

-   Table management

    Tablestore automatically splits, merges, and compacts partitions in tables. Therefore, the following features are not supported:

    -   getTableDescriptor\(TableName tableName\)
    -   compact\(TableName tableName\)
    -   compact\(TableName tableName, byte\[\] columnFamily\)
    -   flush\(TableName tableName\)
    -   getCompactionState\(TableName tableName\)
    -   majorCompact\(TableName tableName\)
    -   majorCompact\(TableName tableName, byte\[\] columnFamily\)
    -   modifyTable\(TableName tableName, HTableDescriptor htd\)
    -   split\(TableName tableName\)
    -   split\(TableName tableName, byte\[\] splitPoint\)
-   Coprocessors

    Tablestore does not support coprocessor. Therefore, the following features are not supported:

    -   coprocessorService\(\)
    -   coprocessorService\(ServerName serverName\)
    -   getMasterCoprocessors\(\)
-   Distributed procedures

    Tablestore does not support Distributed procedures. Therefore, the following features are not supported:

    -   execProcedure\(String signature, String instance, Map props\)
    -   execProcedureWithRet\(String signature, String instance, Map props\)
    -   isProcedureFinished\(String signature, String instance, Map props\)
-   Increment and Append

    Tablestore does not support atomic increase and decrease or atomic Append.


