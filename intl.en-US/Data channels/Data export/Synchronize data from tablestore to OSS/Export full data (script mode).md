# Export full data \(script mode\)

This topic describes how to use the DataWorks console to synchronize full data from Tablestore to Object Storage Service \(OSS\). The files synchronized to OSS can be downloaded and stored in OSS as the backup of the data in Tablestore.

## Step 1: Add a Tablestore data source

To add a Tablestore data source, perform the following steps:

1.  Go to the Data Integration homepage.

    1.  Log on to the [DataWorks console](https://workbench.data.aliyun.com/console) as a workspace administrator.

        **Note:** Only the space administrator role can add data sources. Members who assume other roles can only view the data sources.

    2.  Select a region. In the left-side navigation pane, click Workspaces.

    3.  On the Workspaces page, click **Data Integration** in the Actions column corresponding to the required workspace.

2.  Add a data source.

    1.  In the Data Integration console, click **Connection**.

    2.  On the Data Source page, click **New data source** in the upper-right corner.

    3.  In the Add data source dialog box, click **OTS** in the **NoSQL** section.

    4.  In the Add OTS data source dialog box, configure the parameters.

        ![fig_otssource](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8199939061/p127135.png)

        |Parameter|Description|
        |---------|-----------|
        |Data Source Name|The name of the data source.|
        |Data source description|The description of the data source.|
        |Endpoint|The endpoint of the Tablestore instance. For more information, see [Endpoint](/intl.en-US/Function Introduction/Terms/Endpoint.md).         -   If the Tablestore instance and OSS are located in the same region, enter the classic network address.
        -   If the Tablestore instance and OSS are located in different regions, enter the public IP address.
        -   Do not enter a VPC endpoint. |
        |Table Store instance name|The name of the Tablestore instance.|
        |AccessKey ID|The AccessKey ID and AccessKey secret of your logon account. For more information about how to obtain the AccessKey ID and AccessKey secret, see [Create an AccessKey pair for a RAM user](/intl.en-US/Security Settings/AccessKey pairs/Create an AccessKey pair for a RAM user.md).|
        |AccessKey Secret|

    5.  Click **Test connectivity** to test the connectivity of the data source.

3.  Click **Complete**.

    On the **Data Source** page, information about the data source is displayed.


## Step 2: Add an OSS data source

The operations are similar to those of Step 1. However, in this step, you must click OSS in the **Semi-structuredstorage** section.

**Note:** When you configure an OSS data source, note that the endpoint does not contain the bucket name.

In this example, the data source is named OTS2OSS, as shown in the following figure.

![fig_oss_001](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8199939061/p198808.png)

## Step 3: Create a synchronization task

To create and configure a task to synchronize data from Tablestore to OSS, perform the following steps:

1.  Go to the Data Integration homepage.

    1.  Log on to the [DataWorks console](https://workbench.data.aliyun.com/console) as a workspace administrator.

        **Note:** Only the space administrator role can add data sources. Members who assume other roles can only view the data sources.

    2.  Select a region. In the left-side navigation pane, click Workspaces.

    3.  On the Workspaces page, click **Data Integration** in the Actions column corresponding to the required workspace.

2.  Create a synchronization task node.

    You must create a node for each synchronization task.

    1.  On the Home Page of the Data Integration console, click **New Batch Sync Node**.

    2.  In the Create Node dialog box, configure Node Name and Location.

        ![fig_Oss0002](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8199939061/p198818.png)

    3.  Click **Commit**.

3.  Configure the data source.

    1.  In the left-side navigation pane, double click the synchronization task node under **Data Integration**.

    2.  On the edit page of the synchronization task node, configure Source and Target in the Connections section.

        -   Configure Source.

            Set **Connection** to **OTS** for **Source**.

        -   Configure Target.

            Set **Connection** to **OSS** for **Target**. Configure the data source.

        ![fig_oss003](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8199939061/p198826.png)

    3.  Click the ![script](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5815446061/p127620.png) icon or **Switch to the code editor** to configure the script.

        Tablestore supports only the script mode to configure the connection. When you use the script to configure the connection, you must configure Tablestore Reader and OSS Writer plug-ins. For more information about specific operations, see [Configure Table Store Reader]() and [OSS Writer]().

        On the configuration page of the script, complete the configurations based on the following example:

        ```
        {
        "type": "job",    # Do not modify the value.
        "version": "1.0",  # Do not modify the value.
        "configuration": {
         "setting": {
           "errorLimit": {
             "record": "0"  # When the number of errors exceeds the number of records, the task fails to be imported.
           },
           "speed": {
             "mbps": "1",  # The rate based on which to import data. Unit: MB/s.
             "concurrent": "1"  # The concurrency level.
           }
         },
         "reader": {
           "plugin": "ots",  # Do not modify the value.
           "parameter": {
             "datasource": "",   # The name of the data source during data integration. You must configure the name of the data source before data is integrated. You can select a data source of Tablestore or enter authentication information such as the AccessKey ID in plaintext. We recommend that you use a data source.
             "table": "",    # The name of the table in Tablestore.
             "column": [   # Required. The name of the column you want to export to OSS.
               {
                 "name": "column1"    # The name of the column in Tablestore. This column is to be exported to OSS.
               },
               {
                 "name": "column2"   # The name of the column in Tablestore. This column is to be exported to OSS.
               }
             ],
             "range": {
               "begin": [
                 {
                   "type": "INF_MIN"   # The starting position of the first primary key column in Tablestore. To export full data, set the parameter to INF_MIN. To export part of data, set the parameter based on your requirements. When the table contains multiple primary key columns, configure the information about the corresponding primary key columns for begin.
                 }
               ],
               "end": [
                 {
                   "type": "INF_MAX"   # The ending position of the first primary key column in Tablestore. To export full data, set the parameter to INF_MAX. To export part of data, set the parameter based on your requirements. When the table contains multiple primary key columns, configure the information about the corresponding primary key columns for end.
                 }
               ],
               "split": [  # Configure the information of partitions of the Tablestore table. This feature accelerates data export. This parameter is automatically configured in the next version.
               ]
             }
           }
         },
         "writer": {
           "plugin": "oss",
           "parameter": {
             "datasource": "",  # Configure the OSS data source.
             "object": "",  # The prefix of the object name. The prefix excludes the bucket name. Example: tablestore/20171111/. To perform scheduled export, you must use variables in the prefix such as tablestore/${date}. Then, configure the ${date} value when you configure scheduling parameters.
             "writeMode": "truncate", # The operation the system performs when files of the same name exist. Use truncate for full data export. Valid values: truncate, append, and nonConflict. truncate indicates that files of the same name are cleared. append indicates that data is appended to the content of files of the same name. nonConflict indicates that an error is reported if files of the same name exist.
             "fileFormat": "csv", # The format of the file. Valid values: csv, txt, and parquet.
             "encoding": "UTF-8", // The encoding type.
             "nullFormat": "null", # The string used to identify the null field value. The value can be an empty string.
             "dateFormat":"yyyy-MM-dd HH:mm:ss",// The format of the time.
             "fieldDelimiter": "," # The delimiter to separate each column.
           }
         }
        }
        }
        ```

    4.  Click the ![save](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5815446061/p127623.png) icon to save the data source configurations.

    **Note:**

    -   Full data export is used to export all data at a time. Therefore, you do not need to configure scheduling parameters. To configure scheduling parameters, see configure scheduling parameters in [Synchronize incremental data](/intl.en-US/Data channels/Data export/Synchronize data from tablestore to OSS/Synchronize incremental data (script mode).md).
    -   If the script configurations contain variables such as $\{date\}, set the variable to a specific value when you run the task to synchronize data.
4.  Run the synchronization task.

    1.  Click the ![start](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5815446061/p127635.png) icon.

    2.  In the Arguments dialog box, select the resource group for scheduling.

    3.  Click **OK** to run the task.

        After the task is run, you can check whether the task was successful and the number of rows of data exported on the Runtime Log tab.


## Step 4: View the data exported to OSS

1.  Log on to the [OSS console](https://oss.console.aliyun.com/).

2.  Select the corresponding bucket and object name. You can check whether the object contains the content as expected after downloads.


