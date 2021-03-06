# Create data tables

This topic describes how to create a data table by calling the CreateTable operation. When you call the CreateTable operation, you must specify the schema information and configuration information of the data table when you call the CreateTable operation. You can set reserved read throughput and reserved write throughput for data tables in high-performance instances. You can create one or more index tables when you create a data table.

**Note:**

-   After a data table is created, it takes several seconds to load the data table. During this period, read and write operations performed on the table may fail. Perform operations on the data table after the data table is loaded.
-   You must specify the primary key when you create a data table. A primary key can contain one to four primary key columns. Each primary key column has a name and a data type.

## Prerequisites

-   An instance is created in the Tablestore console. For more information, see [Create instances](/intl.en-US/Quick Start/Create instances.md).
-   Client is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Node.js SDK/Initialization.md).

## Operations

```
  /**
   * Create a data table based on the specified table schema. 
   */
  createTable(params, callback)           
```

## Parameters

|Parameter|Description|
|---------|-----------|
|tableMeta|The schema information of the data table. The schema information contains the following items:-   tableName: the name of the data table.
-   primaryKey: the schema of the primary key for the data table. For more information, see [Primary keys and attributes](/intl.en-US/Function Introduction/Wide Column model/Primary keys and attributes.md).

**Note:** Attribute columns are optional. Different rows in Tablestore can have different attribute columns. You can specify the names of attribute columns when you write data to the attribute columns.

    -   The primary key of a data table can contain one to four primary key columns. Primary key columns are sorted in the order in which they are added. For example, PRIMARY KEY \(A, B, C\) and PRIMARY KEY \(A, C, B\) have different primary key schemas. Tablestore sorts rows based on the values of all primary key columns.
    -   The first primary key column serves as the partition key. Data that has the same partition key value is stored in the same partition. Therefore, we recommend that you assign no more than 10 GB of data to partitions that have the same partition key value. Otherwise, a single partition may be too large to split. We recommend that you evenly distribute requests to read and write data to partitions that have different partition keys for load balancing.
-   definedColumn: the predefined columns of the table and the data types of the predefined column values. Primary key columns cannot be set to predefined columns. You can use predefined columns as the indexed columns or attribute columns for index tables. |
|tableOptions|The configuration information of the data table. For more information, see [Max versions and TTL](/intl.en-US/Function Introduction/Wide Column model/Max versions and TTL.md). The configurations include the following items:

-   timeToLive: the period for which data in the table can be retained. This period is the validity period of data. When the retention period exceeds the timeToLive value, Tablestore automatically deletes expired data.

The minimum timeToLive value is 86400, which is equal to one day. A value of -1 specifies that data never expires.

When you create a data table, you can set timeToLive to -1 if you expect that data never expires. After the table is created, you can call the UpdateTable operation to modify the timeToLive value.

Unit: seconds.

**Note:** You must set timeToLive to -1 if you want to create an index table for a data table.

-   maxVersions: the maximum number of versions of data that can be retained for a single attribute column. When the number of versions of data in attribute columns exceeds the maxVersions value, Tablestore automatically deletes the data of earlier versions.

When you create a data table, you can customize the maximum number of versions that can be retained for the data in an attribute column. After the data table is created, you can call the UpdateTable operation to modify the maxVersions value for the data table.

**Note:** You must set maxVersions to 1 if you want to create an index table for a data table. |
|reservedThroughput|The reserved read throughput and reserved write throughput of the data table. The reserved read throughput and reserved write throughput can be set to 0 for data tables only in capacity instances. Reserved throughput does not apply to these instances.

The default value 0 specifies that all throughput is billed on a pay-as-you-go basis.

Unit: capacity unit \(CU\).

-   If reserved read throughput or reserved write throughput is set to a value greater than 0 for a data table, Tablestore allocates and reserves related resources for the data table. After the data table is created, Tablestore charges reserved throughput resources. Additional throughput is billed on a pay-as-you-go basis. For more information, see [Billing overview](/intl.en-US/Pricing/Billing overview.md).
-   If reserved read throughput or reserved write throughput is set to 0, Tablestore does not allocate or reserve related resources for the data table. |
|indexMetas|The schema information of the index tables. Each indexMeta contains the following items:-   name: the name of the index table.
-   primaryKey: the indexed columns of the index table. The indexed columns are a combination of primary key columns and predefined columns of the data table.

If you use the local secondary index feature, the first primary key column of an index table must be the same as the first primary key column of the corresponding data table.

-   definedColumn: the indexed attribute columns. The attribute columns are a combination of predefined columns of the data table.
-   includeBaseData: specifies whether to include the existing data from the data table in the index table.

When the includeBaseData parameter in CreateIndex is set to true, the index table includes the existing data. When the value is set to false, the index table excludes the existing data.

-   indexType: the type of the index. Valid values: IT\_GLOBAL\_INDEX and IT\_LOCAL\_INDEX.
    -   If indexType is not specified or is set to IT\_GLOBAL\_INDEX, the global secondary index feature is used.

If you use the global secondary index feature, Tablestore automatically synchronizes the columns to index and data in primary key columns from a data table to an index table in asynchronous mode. The synchronization latency is within a few milliseconds.

    -   If indexType is set to IT\_LOCAL\_INDEX, the local secondary index feature is used.

If you use the local secondary index feature, Tablestore automatically synchronizes data from the indexed columns and the primary key columns of a data table to the columns of an index table in synchronous mode. After the data is written to the data table, you can query the data from the index table.

-   indexUpdateMode: the update mode of the index. Valid values: IUM\_ASYNC\_INDEX and IUM\_SYNC\_INDEX.
    -   If indexUpdateMode is not specified or is set to IUM\_ASYNC\_INDEX, the asynchronous mode is used to update the global secondary index.

If you use the global secondary index feature, you must set the index update mode to IUM\_ASYNC\_INDEX.

    -   If you set indexUpdateMode to IUM\_SYNC\_INDEX, the synchronous update mode is used.

If you use the local secondary index feature, you must set the index update mode to IUM\_SYNC\_INDEX. |

## Examples

-   Create a data table without creating an index table

    The following code provides an example on how to create a data table that has two primary key columns and reserved read and write throughput of \(0, 0\):

    ```
    var client = require('./client');
    
    var params = {
      tableMeta: {
        tableName: 'sampleTable',
        primaryKey: [
          {
            name: 'gid',
            type: 'INTEGER'
          },
          {
            name: 'uid',
            type: 'INTEGER'
          }
        ]
      },
      reservedThroughput: {
        capacityUnit: {
          read: 0,
          write: 0
        }
      },
      tableOptions: {
        timeToLive: -1, // Specify the validity period of data. A value of -1 specifies that the data never expires. Unit: seconds. If you set the validity period to a year, the value of timeToLive is 31,536,000 (365 × 24 × 3600). 
        maxVersions: 1  // Specify the maximum number of versions that can be retained for each column. A value of 1 specifies that only the latest version is retained for each column. 
      }
    };
    
    client.createTable(params, function (err, data) {
      if (err) {
        console.log('error:', err);
        return;
      }
      console.log('success:', data);
    });
                
    ```

    For the detailed sample code, visit [CreateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/createTable.js).

-   Create a data table and an index table whose index type is global secondary index

    ```
    var client = require('./client');
    var TableStore = require('../index.js');
    
    var params = {
      tableMeta: {
        tableName: 'sdkGlobalTest',
        primaryKey: [
          {
            name: 'pk1',
            type: TableStore.PrimaryKeyType.INTEGER
          },
          {
            name: 'pk2',
            type: TableStore.PrimaryKeyType.INTEGER
          }
        ],
        definedColumn: [
          {
            "name": "col1",
            "type": TableStore.DefinedColumnType.DCT_INTEGER
          },
          {
            "name": "col2",
            "type": TableStore.DefinedColumnType.DCT_INTEGER
          }
        ],
      },
      reservedThroughput: {
        capacityUnit: {
          read: 0,
          write: 0
        }
      },
      tableOptions: {
        timeToLive: -1, // Specify the validity period of data. A value of -1 specifies that the data never expires. Unit: seconds. You must set timeToLive to -1 for a data table that has index tables. 
        maxVersions: 1  // Specify the maximum number of versions that can be retained for each column. A value of 1 specifies that only the latest version is retained for each column. You must set maxVersions to 1 for a data table that has index tables. 
      },
      streamSpecification: {
        enableStream: false,// The stream feature cannot be enabled for secondary indexes. 
      },
      indexMetas: [
        {
          name: "sdkGlobalIndex1",
          primaryKey: ["pk2"],
          definedColumn: ["col1", "col2"]
        },
        {
          name: "sdkGlobalIndex2",
          primaryKey: ["col1"],
          definedColumn: ["col2"]
        }
      ]
    };
    
    client.createTable(params, function (err, data) {
      if (err) {
        console.log('error:', err);
        return;
      }
      console.log('success:', data);
    });
    ```

-   Create a data table and an index table whose index type is local secondary index

    ```
    var client = require('./client');
    var TableStore = require('../index.js');
    
    var params = {
      tableMeta: {
        tableName: 'sdkLocalTest',
        primaryKey: [
          {
            name: 'pk1',
            type: TableStore.PrimaryKeyType.INTEGER
          },
          {
            name: 'pk2',
            type: TableStore.PrimaryKeyType.INTEGER
          }
        ],
        definedColumn: [
          {
            "name": "col1",
            "type": TableStore.DefinedColumnType.DCT_INTEGER
          },
          {
            "name": "col2",
            "type": TableStore.DefinedColumnType.DCT_INTEGER
          }
        ],
      },
      reservedThroughput: {
        capacityUnit: {
          read: 0,
          write: 0
        }
      },
      tableOptions: {
        timeToLive: -1, // Specify the validity period of data. A value of -1 specifies that the data never expires. Unit: seconds. You must set timeToLive to -1 for a data table that has index tables. 
        maxVersions: 1  // Specify the maximum number of versions that can be retained for each column. A value of 1 specifies that only the latest version is retained for each column. You must set maxVersions to 1 for a data table that has index tables. 
      },
      streamSpecification: {
        enableStream: false,// The stream feature cannot be enabled for secondary indexes. 
      },
      indexMetas: [
        {
          name: "sdklocalIndex1",
          primaryKey: ["pk1","col1"],// Add primary key columns to the index table. The first primary key column of the index table must be the same as the first primary key column of the data table. 
          definedColumn: ["col2"],
          indexUpdateMode: TableStore.IndexUpdateMode.IUM_SYNC_INDEX,// Set the index update mode to IUM_SYNC_INDEX, which specifies the synchronous update mode. If the index type is set to IT_LOCAL_INDEX, the index update mode must be set to IUM_SYNC_INDEX. 
          indexType: TableStore.IndexType.IT_LOCAL_INDEX,// Set the index type to IT_LOCAL_INDEX, which specifies a local secondary index. 
        },
    
        {
          name: "sdklocalIndex2",
          primaryKey: ["pk1","col2"],
          definedColumn: ["col1"],
          indexUpdateMode: TableStore.IndexUpdateMode.IUM_SYNC_INDEX,// Set the index update mode to IUM_SYNC_INDEX, which specifies the synchronous update mode. If the index type is set to IT_LOCAL_INDEX, the index update mode must be set to IUM_SYNC_INDEX. 
          indexType: TableStore.IndexType.IT_LOCAL_INDEX,// Set the index type to IT_LOCAL_INDEX, which specifies a local secondary index. 
        }
      ]
    };
    
    client.createTable(params, function (err, data) {
      if (err) {
        console.log('error:', err);
        return;
      }
      console.log('success:', data);
    });
    ```


