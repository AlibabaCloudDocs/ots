# Tutorial

After you add a Tablestore data source in the DataV console, you can use DataV to display Tablestore data.

## Add a Tablestore data source

1.  Log on to the[DataV console](https://datav.alibabacloud.com/).

2.  On the Data Sources tab, click **Data Sources** in the left-side navigation pane.

3.  On the Data Sources page, click **Add Source**.

    ![fig_adddata](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7631976061/p172806.png)

4.  In the Add Data Source dialog box, select **Tablestore** from the Type drop-down list. Then, configure other parameters for a Tablestore data source. The following table describes the parameters you can configure for a Tablestore data source.

    |Parameter|Description|
    |---------|-----------|
    |Name|The display name of the data source.|
    |AK ID|The AccessKey ID of the account that has permissions to access the Tablestore instance whose data you want to use as a data source.|
    |AK Secret|The AccessKey secret of the account that has permissions to access the Tablestore instance whose data you want to use as a data source.|
    |Internet|The endpoint of the Tablestore instance. Set this parameter based on the Tablestore instance you want to access.|

5.  Click **OK**.

    The added data source is displayed in the data source list.


## Use DataV to display Tablestore data

1.  Log on to the[DataV console](https://datav.alibabacloud.com/).

2.  On the **Projects** tab, move the pointer over the project that you want to edit and click **Edit**.

3.  On the Canvas Editor page, click a widget.

    **Note:** If no widgets have been added to the canvas, add a widget. For more information, see [Add a widget](/intl.en-US/Manage Widgets/Add a widget.md).

4.  Configure the Tablestore data source.

    1.  In the right-side component configurations panel of the canvas, click the ![fig_data](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7631976061/p172815.png) icon.

    2.  On the Data tab, click **Set**.

        ![fig_datasource11](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7631976061/p172982.png)

    3.  In the Data Source panel, set Data Source Type to **TableStore** and select an existing data source.

    4.  Select an operation to perform on the data source and enter a query statement.

        Tablestore supports getRow and getRange. getRow corresponds to [GetRow](/intl.en-US/API Reference/Operations/GetRow.md) in Tablestore. getRange corresponds to [GetRange](/intl.en-US/API Reference/Operations/GetRange.md) in Tablestore.

        -   If you select getRow, a row whose primary key is specified is read. The following code and table describe the format you can use and the parameters you can configure for a query statement.

            ```
            {
            "table_name": "test",
            "rows": {
            "id": 2
            },
            "columns": [
            "id",
            "test"
            ]
            }
            ```

            |Parameter|Description|
            |---------|-----------|
            |table\_name|The name of the table in Tablestore.|
            |rows|The primary keys of the rows.**Note:** The number and data types of primary key columns specified for each row must be the same as those of primary key columns in the table. |
            |columns|The names of the columns to read. You can specify the names of primary key columns and the names of attribute columns. If you do not specify this parameter, all data in each row is returned. |

        -   If you select getRange, all data whose primary keys are within a specified range is read. The following code and table describe the format you can use and the parameters you can configure for a query statement.

            ```
            {
            "table_name": "test",
            "direction": "FORWARD",
            "columns": [
            "id",
            "test"
            ],
            "range": {
            "limit": 4,
            "start": {
            "id": "InfMin"
            },
            "end": {
            "id": 3
            }
            }
            }
            ```

            |Parameter|Description|
            |---------|-----------|
            |table\_name|The name of the table in Tablestore.|
            |direction|The direction in which to read data.            -   If this parameter is set to FORWARD, the value of the start primary key must be smaller than that of the end primary key. The returned rows are sorted in ascending order based on their primary key values.
            -   If the value is BACKWARD, the value of the start primary key must be greater than that of the end primary key, and the returned rows are sorted in descending order based on their primary key values.
Example: A table contains two primary keys A and B where the value of A is smaller than that of B. If you set direction to FORWARD, the rows whose primary key values are equal to or larger than the value of A and smaller than the value of B are returned in ascending order from A to B. If you set direction to BACKWARD, the rows whose primary key values are equal to or larger than the value of B and smaller than the value of A are returned in descending order from B to A. |
            |columns|The names of the columns to read. You can specify the names of primary key columns and the names of attribute columns. If you do not specify this parameter, all data in each row is returned.

If a row is within the specified range of primary key values to read but does not contain the specified columns to return, the row is not included in the response. |
            |limit|The maximum number of rows to return. The value of this parameter must be greater than 0. An operation stops when the maximum number of rows are returned in a forward or backward direction, even if a part of rows within the specified range are not returned. |
            |start|The start and end primary keys of the range to read. The start and end primary keys must be valid primary keys or virtual points consisting of the InfMin and InfMax type data. The number of columns for each virtual point must be the same as that of each primary key. InfMin indicates an infinitely small value. All values of other types are greater than InfMin. InfMax indicates an infinitely great value. All values of other types are smaller than InfMax.

**Note:** The number and data types of primary key columns specified for each row must be the same as those of primary key columns in the table.

            -   start indicates the start primary key. If the row that contains the start primary key exists, the response includes this row.
            -   end indicates the end primary key. No matter whether the row that contains the end primary key exists, the response does not include this row. |
            |end|

    5.  Click Data Response Result next to the ![refresh](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7631976061/p172862.png) icon to obtain a response.

        **Note:** After you obtain the response, you can click **Preview Data Response** to view the response.

5.  Preview and publish the project

    1.  In the upper-right corner of Canvas Editor, click the ![yulan](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7631976061/p172879.png) icon to preview the project.

    2.  In the upper-right corner of Canvas Editor, click the ![fabu](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7631976061/p172880.png) icon.

    3.  In the **Publish** dialog box, click **Publish Project**.

    4.  Click the ![figcopy](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7631976061/p172883.png) icon on the right side of the URL in the **Project UPL** section.

    5.  Copy and paste the URL to the browser address bar to view a published project.


