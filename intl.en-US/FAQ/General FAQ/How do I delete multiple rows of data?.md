# How do I delete multiple rows of data?

After you obtain the primary key information of the data you want to delete, you can call the BatchWriteRow operation to delete the data.

Tablestore allows you to use primary key information to delete multiple rows of data. To delete multiple rows of data, perform the following operations:

1.  Select a method to query the primary key information of the data you want to delete.
    -   To query the data within the specified primary key range and obtain the primary key information of the data, call the [GetRange](/intl.en-US/API Reference/Operations/GetRange.md) operation. Then, you can delete the data. For more information about specific operations, see [GetRange](/intl.en-US/Function Introduction/Wide Column model/Basic operations on data/Multi-row operations.md).
    -   To delete the data that meets the specified conditions, use search indexes to query the data. Then, obtain the primary key information of the data. For more information about specific operations, see [Create search indexes](/intl.en-US/Function Introduction/Search Index/Operations/Create search indexes.md) and [Query](/intl.en-US/Function Introduction/Search Index/Operations/Overview.md).
2.  To delete multiple rows of data by using primary key information, call the [BatchWriteRow](/intl.en-US/API Reference/Operations/BatchWriteRow.md) operation. For more information about specific operations, see [BatchWriteRow](/intl.en-US/Function Introduction/Wide Column model/Basic operations on data/Multi-row operations.md).

