# Synchronize incremental data \(script mode\)

This topic describes how to use the DataWorks console to synchronize incremental data from Tablestore to Object Storage Service \(OSS\).

## Step 1: Add a Tablestore data source

If a Tablestore data source is added, skip this step.

For more information about how to add Tablestore data sources, see [Step 1: Add a Tablestore data source](/intl.en-US/Data channels/Data export/Synchronize data from tablestore to OSS/Export full data (script mode).md).

## Step 2: Add an OSS data source

If an OSS data source is added, skip this step.

For more information about how to add an OSS data source, see [Step 2: Add an OSS data source](/intl.en-US/Data channels/Data export/Synchronize data from tablestore to OSS/Export full data (script mode).md).

## Step 3: Configure a scheduled synchronization task

To create and configure a task to synchronize incremental data from Tablestore to OSS, perform the following steps:

1.  Go to the Data Integration homepage.

    1.  Log on to the [DataWorks console](https://workbench.data.aliyun.com/console) as a project administrator.

        **Note:** Only the project administrator role can add data sources. Members who assume other roles can only view data sources.

    2.  Select a region. In the left-side navigation pane, click Workspaces.

    3.  On the Workspaces page, click **Data Integration** in the Actions column corresponding to the required workspace.

2.  Create a synchronization task node.

    You must create a node for each synchronization task.

    1.  On the Home Page of the Data Integration console, click **New Batch Sync Node**.

    2.  In the Create Node dialog box, configure Node Name and Location.

        ![fig_oss_001](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7099939061/p198829.png)

    3.  Click **Commit**.

3.  Configure the data source.

    1.  In the left-side navigation pane, double click the synchronization task node under **Data Integration**.

    2.  On the edit page of the synchronization task node, configure Source and Target in the Connections section.

        -   Configure Source.

            Set **Connection** to **OTS Stream** for **Source**. Select a data source and table. Configure the start time and end time of the task, the name of the status table, and the maximum number of retry attempts.

        -   Configure Target.

            Set **Connection** to **OSS** for **Target**. Select a data source. Configure the prefix of the object name, text type, and delimiter for the column.

        ![fig_oss_002](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7099939061/p198831.png)

    3.  Click the ![script](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5815446061/p127620.png) icon to configure the script.

        When you use the script to configure the connection, you must configure OTSStream Reader and OSS Writer plug-ins. For more information about specific operations, see [Configure OTSStream Reader](Configure OTSStream Readert1840948.dita#concept_m4h_5gr_p2b) and [OSS Writer]().

        On the configuration page of the script, complete the configurations based on the following example:

        ```
        {
        "type": "job",
        "version": "1.0",
        "configuration": {
        "setting": {
        "errorLimit": {
         "record": "0"  # The maximum number of errors that are allowed. The synchronization task fails when the number of errors exceeds the record value.
        },
        "speed": {
         "mbps": "1",  # The maximum bandwidth of each synchronization task.
         "concurrent": "1"   # The concurrency level of each synchronization task.
        }
        },
        "reader": {
        "plugin": "otsstream",  # The name of the Reader plug-in.
        "parameter": {
         "datasource": "", # The name of the Tablestore data source. If datasource is specified, endpoint, accessId, accessKey, and instanceName are optional.
         "dataTable": "", # The name of the table in Tablestore.
         "statusTable": "TablestoreStreamReaderStatusTable", # The table that stores the status of Tablestore Stream. In most cases, you do not need to modify this parameter value.
         "startTimestampMillis": "",  # The start time of data export. The task must be started in loops because this task is used for incremental export. The start time for each loop is different. Therefore, you must set a variable such as ${start_time}.
         "endTimestampMillis": "",  # The end time of data export. You must set this parameter to a variable such as ${end_time}.
         "date": "yyyyMMdd",  # The date based on which to export data. This feature is the same as startTimestampMillis and endTimestampMillis. Delete the date parameter.
         "mode": "single_version_and_update_only", # The mode in which Tablestore Stream exports data. Set this parameter to single_version_and_update_only. If the configuration template does not contain this parameter, add this parameter.
         "column":[  # Set the columns that you want to export to OSS. If the configuration template does not contain this parameter, add this parameter. You can customize the number of columns.
                  {
                     "name": "uid"  # The name of a primary key column of Tablestore.
                  },
                  {
                     "name": "name"  # The name of an attribute column of Tablestore.
                  },
         ],
         "isExportSequenceInfo": false, # Specify whether to export time series information. If you set the mode parameter to single_version_and_update_only, this parameter can be set only to false.
         "maxRetries": 30 # The maximum number of retry attempts.
        }
        },
        "writer": {
        "plugin": "oss", # The name of the Writer plug-in.
        "parameter": {
         "datasource": "", # The name of the OSS data source.
         "object": "",  # The prefix of the name of the file you want to synchronize to OSS. We recommend that you use the "Tablestore instance name/Table name/date" format. Example: "instance/table/{date}".
         "writeMode": "truncate", # The operation the system performs when files of the same name exist. Valid values: truncate, append, and nonConflict. truncate indicates that files of the same name are deleted. append indicates that data is appended to the content of files of the same name. nonConflict indicates that an error is reported if files of the same name exist.
         "fileFormat": "csv", # The format of the file. Valid values: csv, txt, and parquet.
         "encoding": "UTF-8", # The encoding type.
         "nullFormat": "null", # The string used to identify the null field value. The value can be an empty string.
         "dateFormat": "yyyy-MM-dd HH:mm:ss", # # The format of the time.
         "fieldDelimiter": "," # The delimiter to separate each column.
        }
        }
        }
        }
        ```

    4.  Click the ![save](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5815446061/p127623.png) icon to save the data source configurations.

4.  Run the synchronization task.

    1.  Click the ![start](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5815446061/p127635.png) icon.

    2.  In the Arguments dialog box, select the resource group for scheduling.

    3.  Click **OK** to run the task.

        After the task is completed, you can check whether the task is successful and view the number of exported rows on the Runtime Log tab.

        Incremental data is automatically synchronized from Tablestore to OSS at the latency of 5 to 10 minutes.

5.  Configure the scheduling parameters.

    You can configure the running time, rerun properties, and scheduling dependencies of the synchronization task in **Properties**.

    1.  In the left-side navigation pane, double-click the synchronization task node under **Data Integration**.

    2.  Click **Properties** on the right side of the edit page of the synchronization task node to configure the scheduling parameters. For more information, see [Configure recurrence and dependencies for a node]().

6.  Submit the synchronization task.

    After the synchronization task is submitted to the scheduling system, the scheduling system executes the synchronization task at the scheduled time based on the configured scheduling parameters.

    1.  On the edit page of the synchronization task node, click the ![submit](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7099939061/p127669.png) icon.

    2.  In the Commit Node dialog box, enter your comments in the Change description field.

    3.  Click **OK**.


## Step 4: View the synchronization task

1.  Go to Operation Center.

    **Note:** You can also click **Operation Center** in the upper-right corner of the DataStudio console to go to Operation Center.

    1.  Log on to the [DataWorks console](https://workbench.data.aliyun.com/console) as a project administrator.

    2.  Select a region. In the left-side navigation pane, click Workspaces.

    3.  On the Workspaces page, click **Operation Center** in the Actions column corresponding to the required workspace.

2.  In the left-side navigation pane of the Operation Center console, choose **Cycle Task Maintenance** \> **Cycle Task**.

3.  On the Cycle Task page, view the details about the submitted synchronization task.

    -   In the left-side navigation pane, choose **Cycle Task Maintenance** \> **Cycle Instance** to view the task that is scheduled to run on the current date. Click the instance name to view the task running details.
    -   You can view logs while a task is running or after the task completes running.

## Step 5: View the data exported to OSS

1.  Log on to the [OSS console](https://oss.console.aliyun.com/).

2.  Select the corresponding bucket and object name. You can check whether the object contains the content as expected after downloads.


