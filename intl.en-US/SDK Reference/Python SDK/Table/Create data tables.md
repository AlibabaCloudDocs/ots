# Create data tables

This topic describes how to create a data table by calling the CreateTable operation. When you call the CreateTable operation, you must specify the schema information and configuration information of the data table when you call the CreateTable operation. You can set reserved read throughput and reserved write throughput for data tables in high-performance instances. You can create one or more index tables when you create a data table.

**Note:**

-   After a data table is created, it takes several seconds to load the data table. During this period, read and write operations performed on the table may fail. Perform operations on the data table after the data table is loaded.
-   You must specify the primary key when you create a data table. A primary key can contain one to four primary key columns. Each primary key column has a name and a data type.

## Prerequisites

-   An instance is created in the Tablestore console. For more information, see [Create instances](/intl.en-US/Quick Start/Create instances.md).
-   Client is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).

## Operations

```
"""
Description: You can call this operation to create a data table based on the specified table schema information. 

table_meta is an instance of the tablestore.metadata.TableMeta class. table_meta specifies the name of the data table and the schema of the primary key. 
For more information, see the documentation for the TableMeta class. After you create a data table, it takes several seconds to load the partitions in the data table. You can perform operations on the data table only after the partitions are loaded. 
table_options is an instance of the tablestore.metadata.TableOptions class. table_options contains the time_to_live, max_version, and max_time_deviation parameters. 
reserved_throughput is an instance of the tablestore.metadata.ReservedThroughput class. reserved_throughput specifies the reserved read throughput and reserved write throughput. 
secondary_indexes is an array that can contain one or more instances of the tablestore.metadata.SecondaryIndexMeta class. secondary_indexes specifies the global secondary index you want to create. 

Return value: none. 
""" 
def create_table(self, table_meta, table_options, reserved_throughput, secondary_indexes=[]):          
```

## Parameters

|Parameters|Description|
|----------|-----------|
|table\_meta|The schema information of the data table. The schema information contains the following items:-   table\_name: the name of the data table.
-   schema\_of\_primary\_key: the schema of the primary key. For more information, see [Primary keys and attributes](/intl.en-US/Function Introduction/Wide Column model/Primary keys and attributes.md).

**Note:** Attribute columns are optional. Different rows in Tablestore can have different attribute columns. You can specify the names of attribute columns when you write data to the attribute columns.

    -   The primary key of a data table can contain one to four primary key columns. Primary key columns are sorted in the order in which they are added. For example, PRIMARY KEY \(A, B, C\) and PRIMARY KEY \(A, C, B\) have different primary key schemas. Tablestore sorts rows based on the values of all primary key columns.
    -   The first primary key column serves as the partition key. Data that has the same partition key value is stored in the same partition. Therefore, we recommend that you assign no more than 10 GB of data to partitions that have the same partition key value. Otherwise, a single partition may be too large to split. We recommend that you evenly distribute requests to read and write data to partitions that have different partition keys for load balancing.
-   defined\_columns: the predefined columns of the data table and the data types of the predefined column values. Primary key columns cannot be set to predefined columns. You can use predefined columns as the indexed columns or attribute columns for index tables. |
|table\_options|The configuration information of the data table. For more information, see [Max versions and TTL](/intl.en-US/Function Introduction/Wide Column model/Max versions and TTL.md). The configurations contain the following items:

-   time\_to\_live: the period for which data in the table can be retained. This period is the validity period of data. When the retention period exceeds the time\_to\_live value, Tablestore automatically deletes expired data.

The minimum time\_to\_live value is 86400, which is equal to one day. A value of -1 specifies that data never expires.

When you create a data table, you can set time\_to\_live to -1 if you expect that data never expires. After the data table is created, you can call the UpdateTable operation to modify the time\_to\_live value.

Unit: seconds.

**Note:** You must set time\_to\_live to -1 if you want to create an index table for a data table.

-   max\_versions: the maximum number of versions of data that can be retained for a single attribute column. When the number of versions of data in attribute columns exceeds the max\_versions value, Tablestore automatically deletes data of earlier versions.

When you create a data table, you can customize the maximum number of versions that can be retained for the data in an attribute column. After the data table is created, you can call the UpdateTable operation to modify the max\_versions value for the data table.

**Note:** You must set max\_versions to 1 if you want to create an index table for a data table.

-   max\_time\_deviation: the max version offset, which is the maximum difference between the timestamp of the written data and the current system time. The difference between the version number and the data written time must be within the value of max\_time\_deviation. Otherwise, an error occurs when you write the data. Unit: seconds.

The valid version range of data in an attribute column is calculated based on the formula: Valid version range = **\[Data written time - Max version offset, Data written time + Max version offset\)**.

When you create a data table, Tablestore uses the default value of 86400 if you do not specify a max version offset. After the table is created, you can call the UpdateTable operation to modify the max\_time\_deviation value.

Unit: seconds. |
|reserved\_throughput|The reserved read throughput and reserved write throughput of the data table. The reserved read throughput and reserved write throughput can be set to 0 for data tables only in capacity instances. Reserved throughput does not apply to these instances.

The default value 0 specifies that all throughput is billed on a pay-as-you-go basis.

Unit: capacity unit \(CU\).

-   If reserved read throughput or reserved write throughput is set to a value greater than 0 for a data table, Tablestore allocates and reserves related resources for the data table. After the data table is created, Tablestore charges reserved throughput resources. Additional throughput is billed on a pay-as-you-go basis. For more information, see [Billing overview](/intl.en-US/Pricing/Billing overview.md).
-   If reserved read throughput or reserved write throughput is set to 0 for a data table, Tablestore does not allocate or reserve related resources for the data table. |
|secondary\_indexes|The schema information of the index table. The schema information contains the following items:-   index\_name: the name of the index table.
-   primary\_key\_names: the indexed columns of the index table. The indexed columns are a combination of primary key columns and predefined columns of the data table.

If you use the local secondary index feature, the first primary key column of an index table must be the same as the first primary key column of the corresponding data table.

-   defined\_column\_names: the indexed attribute columns. The attribute columns are a combination of predefined columns of the data table.
-   index\_type: the type of the index. Valid values: IT\_GLOBAL\_INDEX and IT\_LOCAL\_INDEX.
    -   If index\_type is not specified or set to IT\_GLOBAL\_INDEX, the global secondary index feature is used.

If you use the global secondary index feature, Tablestore automatically synchronizes the columns to index and data in primary key columns from a data table to an index table in asynchronous mode. The synchronization latency is within a few milliseconds.

    -   If index\_type is set to IT\_LOCAL\_INDEX, the local secondary index feature is used.

If you use the local secondary index feature, Tablestore automatically synchronizes the data from the indexed columns and primary key columns of a data table to the columns of an index table in synchronous mode. After data is written to the data table, you can query the data from the index table. |

## Examples

-   Create a data table without creating an index table

    The following code provides an example on how to create a data table. The data table contains two primary key columns. In this example, time\_to\_live is set to 31536000 \(one year\), max\_versions to 3, max\_time\_deviation to 86400 \(one day\), and reserved\_throughput to \(0,0\).

    ```
    # Create a schema for primary key columns, including the number, names, and types of the primary key columns. 
    # The first primary key column is named pk0 and requires an INTEGER value. The first primary key column is also the partition key. 
    # The second primary key column is named pk1 and requires an INTEGER value. The data type is specified as INTEGER in this example. You can also set the data type to STRING or BINARY. 
    schema_of_primary_key = [('pk0', 'INTEGER'), ('pk1', 'INTEGER')]
    
    # Create a tableMeta instance based on the name of the data table and the schema of the primary key columns. 
    table_meta = TableMeta('SampleTable', schema_of_primary_key)
    
    # Create a TableOptions instance. Set time_to_live to 31536000 to automatically delete expired data, max_versions to 3, and max_time_deviation to 86,400 (one day). 
    table_options = TableOptions(31536000, 3, 86400)
    
    # Set the reserved read throughput and reserved write throughput to 0. 
    reserved_throughput = ReservedThroughput(CapacityUnit(0, 0))
    
    # Call create_table of the client. If no exceptions occur, the data table is created. If an exception occurs, the data table fails to be created. 
    try:
        ots_client.create_table(table_meta, table_options, reserved_throughput)
        print "create table succeeded"
    # If the task fails, an exception is returned and handled. 
    except Exception:
        print "create table failed."            
    ```

    For the detailed sample code, visit [CreateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/table_operations.py).

-   Create a data table and an index table whose index type is global secondary index

    ```
    schema_of_primary_key = [('gid', 'INTEGER'), ('uid', 'STRING')]
    defined_columns = [('i', 'INTEGER'), ('bool', 'BOOLEAN'), ('d', 'DOUBLE'), ('s', 'STRING'), ('b', 'BINARY')]
    table_meta = TableMeta(table_name, schema_of_primary_key, defined_columns)
    table_option = TableOptions(-1, 1)
    reserved_throughput = ReservedThroughput(CapacityUnit(0, 0))
    secondary_indexes = [
        SecondaryIndexMeta('index1', ['i', 's'], ['bool', 'b', 'd']),
        ]
    client.create_table(table_meta, table_option, reserved_throughput, secondary_indexes)                    
    ```

-   Create a data table and an index table whose index type is local secondary index

    ```
    schema_of_primary_key = [('gid', 'INTEGER'), ('uid', 'STRING')]
    defined_columns = [('i', 'INTEGER'), ('bool', 'BOOLEAN'), ('d', 'DOUBLE'), ('s', 'STRING'), ('b', 'BINARY')]
    table_meta = TableMeta(table_name, schema_of_primary_key, defined_columns)
    table_option = TableOptions(-1, 1)
    reserved_throughput = ReservedThroughput(CapacityUnit(0, 0))
    secondary_indexes = [
        SecondaryIndexMeta('index1', ['gid', 's'], ['bool', 'b', 'd'],index_type= SecondaryIndexType.LOCAL_INDEX),
        ]
    client.create_table(table_meta, table_option, reserved_throughput, secondary_indexes)                    
    ```


