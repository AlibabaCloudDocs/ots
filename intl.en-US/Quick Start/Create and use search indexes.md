# Create and use search indexes

Search index uses inverted indexes and column-oriented storage to address complex query needs when a large amount of data exists. After you create a search index in the console, you can use the index to query data.

**Note:** For more information about search indexes, see [Overview](/intl.en-US/Function Introduction/Search Index/Overview.md).

## Create search indexes

1.  Log on to the [Tablestore console](https://otsnext.console.aliyun.com/).

2.  On the Overview page, click the name of the requested instance or click **Manage Instance** in the Actions column corresponding to the instance.

3.  In the Tables section of the Instance Details tab, click the name of a table, or click **Data Editor** in the Actions column corresponding to the table. Click the Manage Index tab.

4.  On the Manage Index tab, click **Create Index**.

5.  In the Create Index dialog box, create a search index.

    ![fig_tablestore_009](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7569132061/p96405.png)

    1.  Set Index Type to **Search Index**.

    2.  The system generates an index name. You can also set Index Name to a specific value.

    3.  Set Routing Key to the names of primary key columns.

        Tablestore distributes data that is written to a search index to different partitions based on the specified routing fields. The data with the same routing field values is distributed to the same data partition.

    4.  Set Schema Generation Type.

        -   When Schema Generation Type is set to **Manual**, set field names and supported data types for the field values. Specify whether to turn on Array.
        -   When Schema Generation Type is set to **Auto Generate**, the system uses the primary key columns and attribute columns of the table as the fields to index. Set supported data types for the field values. Specify whether to turn on Array.
        **Note:** The **Field Name** and **Field Type** values must be the same as those of the table. For more information about the mapping relationship of field types between the table and search index, see [Data types of column values](/intl.en-US/Function Introduction/Search Index/Operations/Overview.md).

6.  Click **OK**.

    After a search index is created, click **Index Details** in the Actions column corresponding to the search index. You can view the information about the index, such as the routing fields and pre-defined columns.


## Data query

You can use an existing search index to query data.

1.  Log on to the [Tablestore console](https://otsnext.console.aliyun.com/).

2.  On the Overview page, click the name of an instance or click **Manage Instance** in the Actions column corresponding to the instance.

3.  In the Tables section of the Instance Details tab, click the name of an table, or click **Data Editor** in the Actions column corresponding to the table. Click the Manage Index tab.

4.  Click **Index Query** in the Actions column .

    ![fig_tablestore_010](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7569132061/p96432.png)

5.  In the Search dialog box, set filter conditions.

    1.  By default, the system returns all columns. To return specified attribute columns, turn off **All Columns**. Enter the attribute columns to return. Separate multiple attribute columns with commas\(,\).

        **Note:** By default, the primary key columns of the base table are returned.

    2.  Select fields to index. Click **Add**. Set query methods and values for the fields.

    3.  By default, the sorting feature is disabled. To enable sorting, turn on **Sort** to sort query results based on the fields to index. Add fields to index and configure sorting methods.

6.  Click **OK**.

    Data that meets the filter conditions is displayed in the specified order on the Data Editor tab.


## Use Tablestore SDKs

For more information about how to use Tablestore SDKs to implement search index, see the following topics:

-   [Java SDK](/intl.en-US/SDK Reference/Java SDK/Search Index/Create search indexes.md)
-   [Go SDK](/intl.en-US/SDK Reference/Go SDK/Search Index/Create search indexes.md)
-   [Python SDK](/intl.en-US/SDK Reference/Python SDK/Search Index/Create search indexes.md)
-   [Node.js SDK](/intl.en-US/SDK Reference/Node.js SDK/Search Index/Create search indexes.md)
-   [.NET SDK](/intl.en-US/SDK Reference/.NET SDK/Search Index/Create search indexes.md)
-   [PHP SDK](/intl.en-US/SDK Reference/PHP SDK/Search index/Create search indexes.md)

