# Operations

This topic describes the operations you can call to use secondary indexes.

The following table describes the operations you can call to use global secondary indexes.

|Operation|Description|
|---------|-----------|
|[CreateIndex](/intl.en-US/API Reference/Operations/CreateIndex.md)|Creates one or more index tables for a data table. **Note:**

-   You can specify whether to include the existing data of a data table in an index table that you create by calling the CreateIndex operation.
-   You can create one or more index tables when you create a data table by calling the CreateTable operation. |
| |Reads a row of data from an index table.|
|[GetRange](/intl.en-US/API Reference/Operations/GetRange.md)|Reads data within a specified range from an index table.|
|[DeleteIndex](/intl.en-US/API Reference/Operations/DeleteIndex.md)|Deletes the specified index table from a data table. **Note:** Before you call the DeleteTable operation to delete a data table, you must first delete the index tables that are created on the data table. Otherwise, you cannot delete the data table. |

