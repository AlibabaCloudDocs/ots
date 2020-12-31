# 表格存储和 HBase 的区别

Table Store HBase Client 的使用方式与 HBase 类似，但存在一些区别。本节内容介绍 Table Store HBase Client 的特点。

## Table

不支持多列族，只支持单列族。

## Row和Cell

-   不支持设置 ACL
-   不支持设置 Cell Visibility
-   不支持设置 Tag

## GET

表格存储只支持单列族，所以不支持列族相关的接口，包括：

-   setColumnFamilyTimeRange\(byte\[\] cf, long minStamp, long maxStamp\)
-   setMaxResultsPerColumnFamily\(int limit\)
-   setRowOffsetPerColumnFamily\(int offset\)

## SCAN

类似于 GET，既不支持列族相关的接口，也不能设置优化类的部分接口，包括：

-   setBatch\(int batch\)
-   setMaxResultSize\(long maxResultSize\)
-   setAllowPartialResults\(boolean allowPartialResults\)
-   setLoadColumnFamiliesOnDemand\(boolean value\)
-   setSmall\(boolean small\)

## Batch

暂时不支持 BatchCallback。

## Mutations 和 Deletions

-   不支持删除特定列族
-   不支持删除最新时间戳的版本
-   不支持删除小于某个时间戳的所有版本

## Increment 和 Append

暂时不支持

## Filter

-   支持 ColumnPaginationFilter
-   支持 FilterList
-   部分支持 SingleColumnValueFilter，比较器仅支持 BinaryComparator
-   其他 Filter 暂时都不支持

## Optimization

HBase 的部分接口涉及到访问、存储优化等，这些接口目前没有开放：

-   blockcache：默认为 true，不允许用户更改
-   blocksize：默认为 64K，不允许用户更改
-   IsolationLevel：默认为 READ\_COMMITTED，不允许用户更改
-   Consistency：默认为 STRONG，不允许用户更改

## Admin

HBase 中的接口`org.apache.hadoop.hbase.client.Admin`主要是指管控类的 API，而其中大部分的 API 在表格存储中是不需要的。

由于表格存储是云服务，运维、管控类的操作都会被自动执行，用户不需要关注。其他一些少量接口，目前暂不支持。

-   CreateTable

    表格存储只支持单列族，在创建表时只允许设置一个列族，列族中支持 MaxVersion 和 TimeToLive 两个参数。

-   Maintenance task

    在表格存储中，下列的任务维护相关接口都会被自动处理：

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

    在表格存储中，实例名称类似于 HBase 中的 Namespaces，因此不支持 Namespaces 相关的接口，包括：

    -   createNamespace\(NamespaceDescriptor descriptor\)
    -   modifyNamespace\(NamespaceDescriptor descriptor\)
    -   getNamespaceDescriptor\(String name\)
    -   listNamespaceDescriptors\(\)
    -   listTableDescriptorsByNamespace\(String name\)
    -   listTableNamesByNamespace\(String name\)
    -   deleteNamespace\(String name\)
-   Region

    表格存储中会自动处理 Region 相关的操作，因此不支持以下接口：

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

不支持 Snapshots 相关的接口。

## Replication

不支持 Replication 相关的接口。

## Coprocessors

不支持 Coprocessors 相关的接口。

## Distributed procedures

不支持 Distributed procedures 相关的接口。

## Table management

表格存储自动执行 Table 相关的操作，用户无需关注，因此不支持以下接口：

-   compact\(TableName tableName\)
-   compact\(TableName tableName, byte\[\] columnFamily\)
-   flush\(TableName tableName\)
-   getCompactionState\(TableName tableName\)
-   majorCompact\(TableName tableName\)
-   majorCompact\(TableName tableName, byte\[\] columnFamily\)
-   modifyTable\(TableName tableName, HTableDescriptor htd\)
-   split\(TableName tableName\)
-   split\(TableName tableName, byte\[\] splitPoint\)

## 限制项

表格存储是云服务，为了整体性能最优，对部分参数做了限制，且不支持用户通过配置修改，具体限制项请参见[表格存储限制项](/intl.zh-CN/功能介绍/使用限制/通用限制.md)。

