# Read and write data in the console

After a table is created, you can read data from and write data to the table in the Tablestore console.

## Add data

1.  Log on to the [Tablestore console](https://otsnext.console.aliyun.com/).

2.  On the Overview page, click the name of the requested instance or click **Manage Instance** in the Actions column corresponding to the instance.

3.  In the Tables section of the Instance Details tab, click the name of the requested table, and click the Data Editor tab. You can also click **Data Editor** in the Actions column corresponding to the table.

4.  On the Data Editor tab, click **Insert**.

5.  In the Insert dialog box, set Primary Key Value. Click **Add Column**. Set **Name**, **Type**, **Value**, and **Version**.

    By default, **System Time** is selected, which indicates that the current system time is used as the version number of the data. You can also clear **System Time** and enter the version number of the data.

6.  Click **OK**.

    Rows containing the written data are displayed on the Data Editor tab.

    **Note:** A maximum of 50 rows of data can be displayed in the Tablestore console. However, Tablestore does limit the number of rows of data that can be displayed when you use Tablestore SDKs.


## Update data

You can update data in the attribute columns of a row.

1.  Log on to the [Tablestore console](https://otsnext.console.aliyun.com/).

2.  On the Overview page, click the name of the requested instance or click **Manage Instance** in the Actions column corresponding to the instance.

3.  In the Tables section of the Instance Details tab, click the name of the requested table, and click the Data Editor tab. You can also click **Data Editor** in the Actions column corresponding to the table.

4.  On the Data Editor tab, select the row of data that you want to update. Click **Update**.

    ![fig_tablestore_004](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9477925951/p96253.png)

5.  In the Update dialog box, modify the type and value of a primary key, add or remove attribute columns, or update or delete data in attribute columns.

    -   You can click **Add Column** to add an attribute column. You can also click the ![fig_tablestore_005](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5806659951/p96256.png) icon to delete an attribute column.
    -   In the first Actions column, if you select **Update**, you can modify data in attribute columns. If you select **Delete**, you can delete data of the selected version. If you select **Delete All**, you can delete all versions of the data.
6.  Click **OK**.


## Query data

In the Tablestore console, you can query data in a single row \(GetRow\) or data within a specified range \(RangeQuery\).

To query data in a single row, perform the following operations:

1.  Log on to the [Tablestore console](https://otsnext.console.aliyun.com/).

2.  On the Overview page, click the name of the requested instance or click **Manage Instance** in the Actions column corresponding to the instance.

3.  In the Tables section of the Instance Details tab, click the name of the requested table, and click the Data Editor tab. You can also click **Data Editor** in the Actions column corresponding to the table.

4.  On the Data Editor tab, click **Search**.

5.  Set filter conditions.

    1.  Set Modes to **GetRow**.

        ![fig_tablestore_006](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9477925951/p96271.png)

    2.  By default, all columns are returned. To return a specific attribute column, turn off **All Columns**, and then enter the specified attribute columns. Separate the attribute columns with commas\(,\).

        **Note:** You can specify a maximum of 20 attribute columns in the console. This limit does not apply when you use Tablestore SDKs.

    3.  Set **Primary Key Columns**.

        The integrity and accuracy of the primary key values affect the query results.

    4.  Set **Max Versions** to specify the maximum number of versions to return.

        **Note:** You can specify a maximum of 20 versions in the console. This limit does not apply when you use Tablestore SDKs.

6.  Click **OK**.

    Data that meets the filter conditions is displayed on the Data Editor tab.


To query data within a specified range, perform the following steps:

1.  Log on to the [Tablestore console](https://otsnext.console.aliyun.com/).

2.  On the Overview page, click the name of the requested instance or click **Manage Instance** in the Actions column corresponding to the instance.

3.  In the Tables section of the Instance Details tab, click the name of the requested table, and click the Data Editor tab. You can also click **Data Editor** in the Actions column corresponding to the table.

4.  On the Data Editor tab, click **Search**.

5.  Set filter conditions.

    1.  Set Modes to **Range Search**.

        ![fig_tablestore_007](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9477925951/p96285.png)

    2.  By default, all columns are returned. To return a specific attribute column, turn off **All Columns**, and then enter the specified attribute columns. Separate the attribute columns with commas\(,\).

        **Note:** You can specify a maximum of 20 attribute columns in the console. This limit does not apply when you use Tablestore SDKs.

    3.  Set Start Primary Key Column and End Primary Key Column.

        You can set Start Primary Key Column to **Min Value** or **Custom** and End Primary Key Column to **Max Value** or **Custom**. If you select **Custom**, enter a custom value.

        **Note:**

        -   The value in the first primary key column takes priority when the range query mode is used. When the minimum and maximum values for the first primary key column are the same, the system uses the value in the second primary key column to perform the query. The query rules for subsequent primary keys are the same as those for the first two primary keys.
        -   The Custom range is a left-open and right-closed interval.
    4.  Set **Max Versions** to specify the maximum number of versions to return.

        **Note:** You can specify a maximum of 20 versions in the console. This limit does not apply when you use Tablestore SDKs.

    5.  Set Sequence to **Forward Search** or **Backward Search**.

6.  Click **OK**.

    Data that meets the filter conditions is displayed based on the specified order on the Data Editor tab.


## Delete data

You can delete data that you no longer need.

1.  Log on to the [Tablestore console](https://otsnext.console.aliyun.com/).

2.  On the Overview page, click the name of the requested instance or click **Manage Instance** in the Actions column corresponding to the instance.

3.  In the Tables section of the Instance Details tab, click the name of the requested table, and click the Data Editor tab. You can also click **Data Editor** in the Actions column corresponding to the table.

4.  On the Data Editor tab, select the row of data that you want to delete. Click **Delete**.

5.  In the Delete message, click **OK**.


