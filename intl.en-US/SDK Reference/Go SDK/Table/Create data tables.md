# Create data tables

This topic describes how to create a data table by calling the CreateTable operation. When you call the CreateTable operation, you must specify the schema information and configuration information of the data table when you call the CreateTable operation. You can set reserved read throughput and reserved write throughput for data tables in high-performance instances. You can create one or more index tables when you create a data table.

**Note:**

-   After a table is created, it takes several seconds to load the table. During this period, the read and write operations performed on the table may fail. Perform operations on a table after the table is loaded.
-   You must specify the primary key when you create a table. A primary key can contain one to four primary key columns. Each primary key column has a name and a data type.

## Prerequisites

-   An instance is created in the Tablestore console. For more information, see [Create instances](/intl.en-US/Quick Start/Create instances.md).
-   Client is initialized. For more information, see [Initialize a Tablestore client](/intl.en-US/SDK Reference/Go SDK/Initialize a Tablestore client.md).

## Operations

```
  // Description: Create a data table based on the specified table schema. 
  // request is an instance of the CreateTableRequest class and contains TableMeta, TableOption, and ReservedThroughput. 
  // For more information, see the documentation of the TableMeta class. 
  // After you create a data table, it takes several seconds to load the partitions in the data table. You can perform operations on the data table only after the partitions are loaded. 
  // Response: CreateTableResponse
        CreateTable(request *CreateTableRequest) (*CreateTableResponse, error)       
```

## Parameters

|Parameter|Description|
|---------|-----------|
|TableMeta|The schema information of the data table. The schema information contains the following items:-   TableName: the name of the data table.
-   PrimaryKey: the primary key of the data table. For more information, see [Primary keys and attributes](/intl.en-US/Function Introduction/Wide Column model/Primary keys and attributes.md).

**Note:** Attribute columns are optional. Different rows in Tablestore can have different attribute columns. You can specify the names of attribute columns when you write data to the attribute columns.

    -   The primary key of a data table can contain one to four primary key columns. Primary key columns are sorted in the order in which they are added. For example, PRIMARY KEY \(A, B, C\) and PRIMARY KEY \(A, C, B\) have different primary key schemas. Tablestore sorts rows based on the values of all primary key columns.
    -   The first primary key column serves as the partition key. Data that has the same partition key value is stored in the same partition. Therefore, we recommend that you assign no more than 10 GB of data to partitions that have the same partition key value. Otherwise, a single partition may be too large to split. We recommend that you evenly distribute requests to read and write data to partitions that have different partition keys for load balancing.
-   DefinedColumns: the predefined columns of the data table and the data types of the predefined column values. Primary key columns cannot be specified as predefined columns. You can use predefined columns as the indexed columns or attribute columns for index tables. |
|TableOption|The configuration information of the data table. For more information, see [Max versions and TTL](/intl.en-US/Function Introduction/Wide Column model/Max versions and TTL.md). The configurations contain the following items:

-   TimeToAlive: the period for which data in the table can be retained. This period is the validity period of data. When the retention period exceeds the TimeToAlive value, Tablestore automatically deletes expired data.

The minimum TimeToAlive value is 86400, which is equal to one day. A value of -1 specifies that data never expires.

When you create a data table, you can set TimeToAlive to -1 if you expect that data never expires. After the data table is created, you can call the UpdateTable operation to modify the TimeToAlive value.

Unit: seconds.

**Note:** You must set TimeToAlive to -1 if you want to create an index table for a data table.

-   MaxVersion: the maximum number of versions of data that can be retained for a single attribute column. When the number of versions of data in attribute columns exceeds the MaxVersion value, Tablestore automatically deletes data of earlier versions.

When you create a data table, you can customize the maximum number of versions that can be retained for the data in an attribute column. After the data table is created, you can call the UpdateTable operation to modify the MaxVersion value for the data table.

**Note:** You must set MaxVersion to 1 if you want to create an index table for a data table.

-   DeviationCellVersionInSec: the max version offset, which is the maximum difference between the timestamp of the written data and the current system time. The difference between the version number and the data written time must be within the value of DeviationCellVersionInSec. Otherwise, an error occurs when you write data. Unit: seconds.

The valid version range of data in an attribute column is calculated based on the formula: Valid version range = **\[Data written time - Max version offset, Data written time + Max version offset\)**.

When you create a data table, Tablestore uses the default value of 86400 if you do not specify a max version offset. After the data table is created, you can call the UpdateTable operation to modify the DeviationCellVersionInSec value.

Unit: seconds. |
|ReservedThroughput|The reserved read throughput and reserved write throughput of the data table. The reserved read throughput and reserved write throughput can be set to 0 for data tables only in capacity instances. Reserved throughput does not apply to these instances.

The default value 0 specifies that all throughput is billed on a pay-as-you-go basis.

Unit: CU.

-   When reserved read throughput or reserved write throughput is set to a value greater than 0 for a data table, Tablestore allocates and reserves related resources for the data table. After the data table is created, Tablestore charges reserved throughput resources. Additional throughput is billed on a pay-as-you-go basis. For more information, see [Billing overview](/intl.en-US/Pricing/Billing overview.md).
-   When reserved read throughput or reserved write throughput is set to 0 for a data table, Tablestore does not allocate or reserve related resources for the data table. |
|IndexMeta|The schema information of the index table. The schema information contains the following items:-   IndexName: the name of the index table.
-   PrimaryKey: the indexed columns of the index table. The indexed columns are a combination of primary key columns and predefined columns of the data table.

If you use the local secondary index feature, the first primary key column of an index table must be the same as the first primary key column of the corresponding data table.

-   DefinedColumns: the attribute column of the index table. The attribute columns are a combination of predefined columns of the data table.
-   IndexType: the type of the index. Valid values: IT\_GLOBAL\_INDEX and IT\_LOCAL\_INDEX.
    -   If IndexType is not specified or set to IT\_GLOBAL\_INDEX, the global secondary index feature is used.

When you use the global secondary index feature, Tablestore automatically synchronizes the columns to be indexed and data in primary key columns from a data table to an index table in asynchronous mode. The latency of synchronization is within a few milliseconds.

    -   If IndexType is set to IT\_LOCAL\_INDEX, the local secondary index feature is used.

If you use the local secondary index feature, Tablestore automatically synchronizes the data from the indexed columns and primary key columns of a data table to the columns of an index table in synchronous mode. After data is written to the data table, you can query the data from the index table. |

## Examples

-   Create a data table without creating an index table

    The following code provides an example on how to create a data table that has two primary key columns and reserved read and write throughput of \(0, 0\):

    ```
     // Create a schema for primary key columns, which includes the number, names, and types of the primary key columns. 
     // The first primary key column is named pk0 and requires an INTEGER value. The first primary key column is also the partition key. 
     // The second primary key column is named pk1 and requires an INTEGER value. 
     tableMeta := new(tablestore.TableMeta)
     tableMeta.TableName = tableName
     tableMeta.AddPrimaryKeyColumn("pk0", tablestore.PrimaryKeyType_INTEGER)
     tableMeta.AddPrimaryKeyColumn("pk1", tablestore.PrimaryKeyType_STRING)
     tableOption := new(tablestore.TableOption)
     tableOption.TimeToAlive = -1
     tableOption.MaxVersion = 3
     reservedThroughput := new(tablestore.ReservedThroughput)
     reservedThroughput.Readcap = 0
     reservedThroughput.Writecap = 0
     createtableRequest.TableMeta = tableMeta
     createtableRequest.TableOption = tableOption
     createtableRequest.ReservedThroughput = reservedThroughput
     response, err = client.CreateTable(createtableRequest)
     if (err != nil) {
         fmt.Println("Failed to create table with error:", err)
     } else {
        fmt.Println("Create table finished")
     }
                
    ```

    For the detailed sample code, visit [CreateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/TableOperation.go).

-   Create a data table and an index table whose index type is global secondary index

    ```
    func CreateTableWithGlobalIndexSample(client *tablestore.TableStoreClient, tableName string) {
        createTableRequest := new(tablestore.CreateTableRequest)
    
        tableMeta := new(tablestore.TableMeta)
        tableMeta.TableName = tableName
        tableMeta.AddPrimaryKeyColumn("pk1", tablestore.PrimaryKeyType_STRING)
        tableMeta.AddPrimaryKeyColumn("pk2", tablestore.PrimaryKeyType_INTEGER)
        tableMeta.AddDefinedColumn("definedcol1", tablestore.DefinedColumn_STRING)
        tableMeta.AddDefinedColumn("definedcol2", tablestore.DefinedColumn_INTEGER)
    
        indexMeta := new(tablestore.IndexMeta) // Create IndexMeta for the index table. 
        indexMeta.AddPrimaryKeyColumn("definedcol1") // Add a primary key column to the index table. Specify the definedcol1 column of the data table as the primary key column of the index table. 
        indexMeta.AddDefinedColumn("definedcol2") // Add an attribute column to the index table. Specify the definedcol2 column of the data table as the attribute column of the index table. 
        indexMeta.IndexName = "indexSample"
    
        tableOption := new(tablestore.TableOption)
        tableOption.TimeToAlive = -1 // Specify the validity period of data. A value of -1 specifies that the data never expires. Unit: seconds. You must set TimeToAlive to -1 for a data table that has index tables. 
        tableOption.MaxVersion = 1 // Specify the maximum number of versions that can be retained for each column. A value of 1 specifies that only the latest version is retained for each column. You must set MaxVersion to 1 for a data table that has index tables. 
        reservedThroughput := new(tablestore.ReservedThroughput)
    
        createTableRequest.TableMeta = tableMeta
        createTableRequest.TableOption = tableOption
        createTableRequest.ReservedThroughput = reservedThroughput
        createTableRequest.AddIndexMeta(indexMeta)
    
        _, err := client.CreateTable(createTableRequest)
        if err != nil {
            fmt.Println("Failed to create table with error:", err)
        } else {
            fmt.Println("Create table finished")
        }
    }
    ```

-   Create a data table and an index table whose index type is local secondary index

    ```
    func CreateTableWithGlobalIndexSample(client *tablestore.TableStoreClient, tableName string) {
        createTableRequest := new(tablestore.CreateTableRequest)
    
        tableMeta := new(tablestore.TableMeta)
        tableMeta.TableName = tableName
        tableMeta.AddPrimaryKeyColumn("pk1", tablestore.PrimaryKeyType_STRING)
        tableMeta.AddPrimaryKeyColumn("pk2", tablestore.PrimaryKeyType_INTEGER)
        tableMeta.AddDefinedColumn("definedcol1", tablestore.DefinedColumn_STRING)
        tableMeta.AddDefinedColumn("definedcol2", tablestore.DefinedColumn_INTEGER)
    
        indexMeta := new(tablestore.IndexMeta) // Create IndexMeta for the index table. 
        indexMeta.IndexType = IT_LOCAL_INDEX // Set the index type to IT_LOCAL_INDEX. 
    
        indexMeta.AddPrimaryKeyColumn("pk1") // Add a primary key column to the index table. The first primary key column of the index table must be the same as that of the data table. 
        indexMeta.AddPrimaryKeyColumn("definedcol1") // Add a primary key column to the index table. Specify the definedcol1 column of the data table as the primary key column of the index table. 
        indexMeta.AddDefinedColumn("definedcol2") // Add an attribute column to the index table. Specify the definedcol2 column of the data table as the attribute column of the index table. 
        indexMeta.IndexName = "indexSample"
    
        tableOption := new(tablestore.TableOption)
        tableOption.TimeToAlive = -1 // Specify the validity period of data. A value of -1 specifies that the data never expires. Unit: seconds. You must set TimeToAlive to -1 for a data table that has index tables. 
        tableOption.MaxVersion = 1 // Specify the maximum number of versions that can be retained for each column. A value of 1 specifies that only the latest version is retained for each column. You must set MaxVersion to 1 for a data table that has index tables. 
        reservedThroughput := new(tablestore.ReservedThroughput)
    
        createTableRequest.TableMeta = tableMeta
        createTableRequest.TableOption = tableOption
        createTableRequest.ReservedThroughput = reservedThroughput
        createTableRequest.AddIndexMeta(indexMeta)
    
        _, err := client.CreateTable(createTableRequest)
        if err != nil {
            fmt.Println("Failed to create table with error:", err)
        } else {
            fmt.Println("Create table finished")
        }
    }
    ```


