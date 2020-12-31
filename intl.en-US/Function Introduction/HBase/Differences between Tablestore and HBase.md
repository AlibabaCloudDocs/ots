# Differences between Tablestore and HBase

Tablestore HBase Client is used in a similar but differentiated way to HBase. This topic introduces the features of Tablestore HBase Client.

## Table

Tablestore supports only single ColumnFamilies.

## Row and Cell

-   Tablestore does not support ACL settings.
-   Tablestore does not support Cell Visibility settings.
-   Tablestore does not support Tag settings.

## GET

Tablestore supports only single ColumnFamilies. Therefore, Tablestore does not support ColumnFamily-related operations, including:

-   setColumnFamilyTimeRange\(byte\[\] cf, long minStamp, long maxStamp\)
-   setMaxResultsPerColumnFamily\(int limit\)
-   setRowOffsetPerColumnFamily\(int offset\)

## SCAN

Similar to GET, Tablestore does not support ColumnFamily-related operations and cannot be used to set partial optimization operations, including:

-   setBatch\(int batch\)
-   setMaxResultSize\(long maxResultSize\)
-   setAllowPartialResults\(boolean allowPartialResults\)
-   setLoadColumnFamiliesOnDemand\(boolean value\)
-   setSmall\(boolean small\)

## Batch

Tablestore does not support BatchCallback.

## Mutations and Deletions

-   Tablestore does not support the deletion of the specified ColumnFamily.
-   Tablestore does not support the deletion of the versions that has the latest timestamp.
-   Tablestore does not support the deletion of all versions earlier than the specified timestamp.

## Increment and Append

Tablestore does not support Increment or Append.

## Filter

-   Tablestore supports ColumnPaginationFilter.
-   Tablestore supports FilterList.
-   Tablestore partially supports SingleColumnValueFilter, and supports only BinaryComparator.
-   Tablestore does not support other filters.

## Optimization

Some of the HBase operations involve access and storage optimization. The following operations are not opened in Tablestore:

-   blockcache: The default value is true, which cannot be modified.
-   blocksize: The default value is 64 KB, which cannot be modified.
-   IsolationLevel: The default value is READ\_COMMITTED, which cannot be modified.
-   Consistency: The default value is STRONG, which cannot be modified.

## Admin

The `org.apache.hadoop.hbase.client.Admin` operations of HBase are used for management and control, most of which are not required in Tablestore.

Tablestore is a cloud service that automatically performs operations such as operation and maintenance, management, and control, which you do not need to concern about. Tablestore does not support a few of other operations.

-   CreateTable

    Tablestore supports only single ColumnFamilies. Therefore, you can create only one ColumnFamily when you create a table. The ColumnFamily supports the MaxVersions and TimeToLive parameters.

-   Maintenance task

    In Tablestore, the following operations related to task maintenance are automatically processed:

    -   abort\(String why, Throwable e\)
    -   balancer\(\)
    -   enableCatalogJanitor\(boolean enable\)
    -   getMasterInfoPort\(\)
    -   isCatalogJanitorEnabled\(\)
    -   rollWALWriter\(ServerName serverName\) -runCatalogScan\(\)
    -   setBalancerRunning\(boolean on, boolean synchronous\)
    -   updateConfiguration\(ServerName serverName\)
    -   updateConfiguration\(\)
    -   stopMaster\(\)
    -   shutdown\(\)
-   Namespaces

    In Tablestore, the instance name is similar to Namespaces in HBase. Therefore, Tablestore does not support Namespaces-related operations, including:

    -   createNamespace\(NamespaceDescriptor descriptor\)
    -   modifyNamespace\(NamespaceDescriptor descriptor\)
    -   getNamespaceDescriptor\(String name\)
    -   listNamespaceDescriptors\(\)
    -   listTableDescriptorsByNamespace\(String name\)
    -   listTableNamesByNamespace\(String name\)
    -   deleteNamespace\(String name\)
-   Region

    Tablestore automatically performs Region-related operations. Therefore, it does not support the following operations:

    -   assign\(byte\[\] regionName\)
    -   closeRegion\(byte\[\] regionname, String serverName\)
    -   closeRegion\(ServerName sn, HRegionInfo hri\)
    -   closeRegion\(String regionname, String serverName\)
    -   closeRegionWithEncodedRegionName\(String encodedRegionName, String serverName\)
    -   compactRegion\(byte\[\] regionName\)
    -   compactRegion\(byte\[\] regionName, byte\[\] columnFamily\)
    -   compactRegionServer\(ServerName sn, boolean major\)
    -   flushRegion\(byte\[\] regionName\)
    -   getAlterStatus\(byte\[\] tableName\)
    -   getAlterStatus\(TableName tableName\)
    -   getCompactionStateForRegion\(byte\[\] regionName\)
    -   getOnlineRegions\(ServerName sn\)
    -   majorCompactRegion\(byte\[\] regionName\)
    -   majorCompactRegion\(byte\[\] regionName, byte\[\] columnFamily\)
    -   mergeRegions\(byte\[\] encodedNameOfRegionA, byte\[\] encodedNameOfRegionB, boolean forcible\)
    -   move\(byte\[\] encodedRegionName, byte\[\] destServerName\)
    -   offline\(byte\[\] regionName\)
    -   splitRegion\(byte\[\] regionName\)
    -   splitRegion\(byte\[\] regionName, byte\[\] splitPoint\)
    -   stopRegionServer\(String hostnamePort\)
    -   unassign\(byte\[\] regionName, boolean force\)

## Snapshots

Tablestore does not support Snapshots-related operations.

## Replication

Tablestore does not support Replication-related operations.

## Coprocessors

Tablestore does not support Coprocessors-related operations.

## Distributed procedures

Tablestore does not support Distributed procedures-related operations.

## Table management

Tablestore automatically performs Table-related operations, which does not need to be concerned. Therefore, Tablestore does not support the following operations:

-   compact\(TableName tableName\)
-   compact\(TableName tableName, byte\[\] columnFamily\)
-   flush\(TableName tableName\)
-   getCompactionState\(TableName tableName\)
-   majorCompact\(TableName tableName\)
-   majorCompact\(TableName tableName, byte\[\] columnFamily\)
-   modifyTable\(TableName tableName, HTableDescriptor htd\)
-   split\(TableName tableName\)
-   split\(TableName tableName, byte\[\] splitPoint\)

## Limits

Tablestore is a cloud service. To guarantee the optimal overall performance, some parameters are limited and cannot be reconfigured. For more information about the limits, see [General limits](/intl.en-US/Function Introduction/Limits/General limits.md).

