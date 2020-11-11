# Create tables

This topic describes how to create a table in the Tablestore console.

## Use the Tablestore console to create a table

1.  Log on to the [Tablestore console](https://otsnext.console.aliyun.com/).

2.  On the Overview page, click the name of the an instance or click **Manage Instance** in the Actions column corresponding to the instance.

3.  On the Instance Details tab, click **Create Table**.

    **Note:** You can create a maximum of 64 tables in an instance.

4.  In the Create Table dialog box, set **Table Name** and **Primary Key**.

    The following table describes the parameters you can configure for the table.

    |Parameter|Description|
    |---------|-----------|
    |Table Name|The name of the table. This name is used to uniquely identify a table in an instance. The name must be 1 to 255 bytes in length and can contain letters, digits, and underscores \(\_\). The name must start with a letter or an underscore \(\_\). |
    |Primary Key|The primary key columns of a table used to uniquely identify a record in a table. Enter a primary key name and select a data type. Click **Add Primary Key Column** to add a primary key column.

You can add one to four primary key columns. The first primary key column is called the partition key. The configurations and order of primary key columns cannot be modified after the table is created.

**Note:**

    -   In Tablestore, only a primary key column can be used as an auto-increment primary key column. Partition keys cannot be used as auto-increment primary key columns.
    -   After a primary key column is set to an auto-increment primary key column, Tablestore automatically generates a value for the auto-increment primary key column when you write a row of data. You do not need to specify a value for the auto-increment primary key column. The values of auto-increment primary key columns are incremental and unique within the rows that share the same partition key.
    -   Naming conventions of primary key columns: The name must be 1 to 255 bytes in length and can contain letters, digits, and underscores \(\_\). The name must start with a letter or underscore \(\_\)
    -   Data types supported by primary key columns are **STRING**, **INTEGER**, and **BINARY**. |

5.  Optional. In the Create Table dialog box, turn on Advanced Settings or Global Secondary Index.

    To configure advanced settings such as Time to Live \(TTL\), turn on Advanced Settings. To create a global secondary index, turn on Global Secondary Index.

    -   Turn on **Advanced Settings**. Configure advanced parameters. The following table describes the parameters.

        |Parameter|Description|
        |---------|-----------|
        |Time to Live|The period for which data in the table can be retained. When the actual retention period exceeds the TTL value, the system automatically clears expired data. The minimum TTL value is 86,400 seconds \(one day\). A value of -1 indicates that data never expires.

Unit: seconds. |
        |Max Versions|The maximum number of versions that can be retained for data in attribute columns of the table. When the number of versions of data in attribute columns exceeds the max versions value, the system automatically deletes data of earlier versions. The max versions value in an attribute column is an integer greater than 0. |
        |Max Version Offset|The difference between the version number and the data written time must be within the value of Max Version Offset. Otherwise, an error occurs when the data is written. Unit: seconds. The valid version range of data in an attribute column is calculated based on the formula: Valid version range = **\[Data written time - Max version offset, Data written time + max version offset\)**. |
        |Reserved Read Throughput|The reserved read or write throughput setting for the table. This parameter is unavailable for capacity instances.

Data type: INTEGER. Valid values: 0 to 100000.

Unit: CU.

        -   When Reserved Read Throughput or Reserved Write Throughput is set to a value greater than 0, Tablestore allocates and reserves related resources for the table. After the table is created, Tablestore charges reserved throughput resources.
        -   When Reserved Read Throughput or Reserved Write Throughput is set to 0, Tablestore does not allocate or reserve related resources for the table. |
        |Reserved Write Throughput|

    -   Optional. Turn on Global Secondary Index. Create a global secondary index.
        -   Click **+ Add** next to Pre-defined Column. Enter the name of the pre-defined column and select a data type from the drop-down list.

            Naming conventions of pre-defined columns: The name must be 1 to 255 bytes in length and can contain letters, digits, and underscores \(\_\). The name must start with a letter or underscore \(\_\).

            Data types supported by pre-defined columns are STRING, INTEGER, BINARY, FLOAT, and BOOLEAN.

        -   Click **Add Global Secondary Index**. Index Name and Primary Key are required. Pre-defined Column is optional.
6.  Click **OK**.

    After a table is created, you can view the table in the **Tables** section. If the created table is not displayed in the list of tables, click the ![fig_tablestore_002](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7506659951/p96207.png) icon to refresh the list of tables.

    After a table is created, you can perform the following operations on the table:

    -   Click the name of the table or click Details, Data Editor, or the More icon in the Actions column and choose Manage Index or Tunnels from the shortcut menu corresponding to the table. On the Manage Table page, you can perform the following operations:
        -   On the Details tab, you can view basic information of the table, advanced features, and primary key columns, modify attributes, and add pre-defined columns.
        -   On the Data Editor tab, you can insert or update data, query data, view data details, and delete multiple data at a time.
        -   On the Manage Index tab, you can create a search index or global secondary index, view index details, query data, and delete indexes.
        -   On the Tunnels tab, you can enable the stream feature, create a tunnel, show the list of channels, and delete a tunnel.
        -   On the Monitoring Indicators tab, you can view monitored data based on tables or indexes, time ranges, categories, and operations.
    -   In the Actions column corresponding to the table, choose **![fig_001](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7506659951/p100545.png)** \> **Delete** from the shortcut menu. You can delete the table.

        **Note:** Before you delete a table, ensure that you have deleted indexes created for the table. Otherwise, the table fails to delete.


## Use Tablestore SDK to create a table

For more information, see [SDK overview](/intl.en-US/SDK Reference/SDK overview.md).

