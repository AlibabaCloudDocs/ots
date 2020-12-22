# Full export \(script mode\)

This topic describes how to use the DataWorks console to export full data from Tablestore to MaxCompute.

## Step 1: Add a Tablestore data source

To add a Tablestore database as the data source, perform the following steps:

1.  Log on to the [DataWorks console](https://workbench.data.aliyun.com/console) as a workspace administrator.

    **Note:** Only the workspace administrator can add data sources. Members of other roles can only view data sources.

2.  In the top navigation bar, select a region. Then, in the left-side navigation pane, click Workspaces.

3.  On the Workspaces page, click **Data Integration** in the Actions column corresponding to a workspace.

4.  In the Data Integration console, click **Connection**.

5.  Add a data source.

    1.  On the Data Source page, click **New data source** in the upper-right corner.

    2.  In the Add data source dialog box, click **OTS** in the **NoSQL** section.

    3.  In the Add OTS data source dialog box, configure the parameters.

        ![fig_otssource](../images/p127135.png)

        |Parameter|Description|
        |---------|-----------|
        |Data Source Name|The name of the data source. Example: gps\_data.|
        |Description|The description of the data source.|
        |Endpoint|The endpoint of the Tablestore instance. For more information, see [Endpoint](/intl.en-US/Function Introduction/Terms/Endpoint.md).         -   If the Tablestore instance is in the same region as the MaxCompute project, enter the internal endpoint.
        -   If the Tablestore instance is not in the same region as the MaxCompute project, enter the public endpoint.
        -   Do not enter a VPC endpoint. |
        |Table Store instance name|The name of the Tablestore instance.|
        |AccessKey ID|The AccessKey ID and AccessKey secret of the Alibaba Cloud account. For information about how to obtain the AccessKey ID and AccessKey secret, see [Create an AccessKey]().|
        |AccessKey Secret|

    4.  Click **Test connectivity** to test the connectivity of the data source.

6.  Click **Complete**.

    On the **Data Source** page, information about the data source is displayed.


## Step 2: Add a MaxCompute data source

The procedure is similar to that in Step 1, except that in the Add data source dialog box, you must click **MaxCompute \(ODPS\)** in the Big Data Storage section.

In this example, the data source is named OTS2ODPS, as shown in the following figure.

![fig_odps_001](../images/p130237.png)

## Step 3: Create a synchronization task

To create and configure a task to synchronize data from Tablestore to MaxCompute, perform the following steps:

1.  Log on to the [DataWorks console](https://workbench.data.aliyun.com/console) as a workspace administrator.

    **Note:** Only the workspace administrator can add data sources. Members of other roles can only view data sources.

2.  In the top navigation bar, select a region. Then, in the left-side navigation pane, click Workspaces.

3.  On the Workspaces page, click **Data Integration** in the Actions column corresponding to a workspace.

4.  Create a synchronization task node.

    Each synchronization task must have a corresponding node created.

    1.  On the Home Page of the Data Integration console, click **New offline synchronization**.

    2.  In the Create Node dialog box, configure Node Name and Location.

        ![fig_newtask](../images/p130243.png)

    3.  Click **Commit**.

5.  Configure the data source.

    1.  In the left-side navigation pane, double click the synchronization task node under **Data Integration**.

    2.  On the synchronization task node edit page, configure Source and Target in the Connections section.

        -   Configure Source.

            In the **Source** section, select **OTS** from the drop-down list next to **Connection**, and click the ![script](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5815446061/p127620.png) icon or **Switch to the code editor** to configure the script.

            **Note:** Tablestore supports only the script mode. For more information about how to configure the script, see [How to Configure ots Reader]() and [How to Configure stream Writer]().

            ```
            {
            "type": "job",
            "version": "1.0",
            "configuration": {
            "setting": {
              "errorLimit": {
                "record": "0"    # The maximum number of errors allowed.
              },
              "speed": {
                "mbps": "1",   # The maximum traffic, in MB/s.
                "concurrent": "1", // The number of concurrent threads.
              }
            },
            "reader": {
              "plugin": "ots",  # The plug-in name read.
              "parameter": {
                "datasource": "", // The name of the data source.
                "table": "",  // The name of the data table.
                "column": [  # The name of the column in Tablestore that needs to be exported to MaxCompute.
                  {
                    "name": "column1"
                  },
                  {
                    "name": "column2"
                  },
                  {
                    "name": "column3"
                  },
                  {
                    "name": "column4"
                  },
                  {
                    "name": "column5"
                  }
                ],
                "range": {  # The range of data to be exported. In the full export mode, the range is from INF_MIN to INF_MAX.
                  "begin": [ # The start position of data to be exported. The minimum value is INF_MIN. The number of configuration items in begin must be the same as the number of primary key columns in the table in Tablestore.
                    {
                      "type": "INF_MIN"
                    },
                    {
                      "type": "INF_MIN"
                    },
                    {
                      "type": "STRING",  # Specifies to export data in the third column starting from begin1.
                      "value": "begin1"
                    },
                    {
                      "type": "INT",  # Specifies to export data in the fourth column starting from 0.
                      "value": "0"
                    }
                  ],
                  "end": [  # The end position of data to be exported.
                    {
                      "type": "INF_MAX"
                    },
                    {
                      "type": "INF_MAX"
                    },
                    {
                      "type": "STRING",
                      "value": "end1"
                    },
                    {
                      "type": "INT",
                      "value": "100"
                    }
                  ],
                  "split": [  # Configure the partition range. Typically, this parameter is not set. If the read performance is poor, submit a ticket or join the DingTalk group 23307953 to contact Tablestore technical support.
                    {
                      "type": "INF_MIN"
                    },
                    {
                      "type": "STRING",
                      "value": "splitPoint1"
                    },
                    {
                      "type": "STRING",
                      "value": "splitPoint2"
                    },
                    {
                      "type": "STRING",
                      "value": "splitPoint3"
                    },
                    {
                      "type": "INF_MAX"
                    }
                  ]
                }
              }
            },
            "writer": {
              "plugin": "odps",  # The plug-in name written by MaxCompute.
              "parameter": {
                "datasource": "",  # The name of the MaxCompute data source.
                "column": [],  # The name of the column in MaxCompute. The column names are ordered in the same order as in Tablestore.
                "table": "",  # The name of the table in MaxCompute. The table must be created in advance. Otherwise, the task may fail.
                "partition": "",  # This parameter is required if the MaxCompute table is partitioned. Do not specify this parameter if the table is not partitioned. The partition to which data is written. The last-level partition must be specified.
                "truncate": false  # Specifies whether to delete all previous data.
              }
            }
            }
            }
            ```

        -   Configure Target

            In the **Target** section, select **ODPS** from the drop-down list next to **Connection**, and set **Table**.

        ![fig_ots2odps](../images/p130260.png)

    3.  Click the ![save](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5815446061/p127623.png) icon to save the data source configurations.

6.  Run the synchronization task.

    1.  Click the ![start](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5815446061/p127635.png) icon.

    2.  In the Arguments dialog box, select the resource group for scheduling.

    3.  Click **OK** to run the task.

        After the task is run, you can check whether the task was successful and the number of rows of data exported on the Runtime Log tab.


## Step 4: Run the synchronization task at the scheduled time

1.  Log on to the [DataWorks console](https://workbench.data.aliyun.com/console) as a workspace administrator.

    **Note:** Only the workspace administrator can add data sources. Members of other roles can only view data sources.

2.  In the top navigation bar, select a region. Then, in the left-side navigation pane, click Workspaces.

3.  On the Workspaces page, click **Data Analytics** in the Actions column corresponding to a workspace.

4.  On the Data Analytics page of the DataStudio console, click the synchronization task under **Business Flow** in the left-side navigation pane.

5.  Configure the scheduling parameters.

    You can configure the running time, rerun properties, and scheduling dependencies of the synchronization task in **Properties**.

    1.  In the left-side navigation pane, double click the synchronization task node under **Data Integration**.

    2.  Click **Properties** on the right of the synchronization task node edit page to configure the scheduling parameters. For more information, see [Configure recurrence and dependencies for a node]().

6.  Submit the synchronization task.

    1.  On the synchronization task node edit page, click the ![submit](../images/p127669.png) icon.

    2.  In the Commit Node dialog box, enter your comments in the Change description field.

    3.  Click **OK**.

        After the synchronization task is submitted to the scheduling system, the scheduling system automatically executes the synchronization task at the scheduled time based on the configured scheduling parameters.


## Step 5: View the synchronization task

1.  Log on to the [DataWorks console](https://workbench.data.aliyun.com/console) as a workspace administrator.

    **Note:** Only the workspace administrator can add data sources. Members of other roles can only view data sources.

2.  In the top navigation bar, select a region. Then, in the left-side navigation pane, click Workspaces.

3.  On the Workspaces page, click **Operation Center** in the Actions column corresponding to a workspace.

4.  In the left-side navigation pane of the Operation Center console, choose**Cycle Task Maintenance** \> **Cycle Task**.

5.  On the Cycle Task page, view the details about the submitted synchronization task.

    -   In the left-side navigation pane, choose **Cycle Task Maintenance** \> **Cycle Instance** to view the task that is scheduled to run on the current date. Click the instance name to view the task running details.
    -   You can view logs while a task is running or after the task completes running.
    ![fig_ots2Maxcompute](../images/p130320.png)


## Step 6: View the data imported to MaxCompute

1.  Log on to the [DataWorks console](https://workbench.data.aliyun.com/console) as a workspace administrator.

    **Note:** Only the workspace administrator can add data sources. Members of other roles can only view data sources.

2.  In the top navigation bar, select a region. Then, in the left-side navigation pane, click Workspaces.

3.  On the Workspaces page, click **Data Map** in the Actions column corresponding to a workspace.

4.  In the top navigation bar of the DataMap console, choose **My Data** \> **Managed by Me**.

5.  On the Managed by Me tab, click the name of the imported table.

6.  On the table details page, click the Data Preview tab to view the imported data.


