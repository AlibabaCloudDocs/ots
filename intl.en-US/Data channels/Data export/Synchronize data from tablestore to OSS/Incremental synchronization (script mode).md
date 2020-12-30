# Incremental synchronization \(script mode\)

Data Integration supports data synchronization in wizard mode and script mode. Wizard mode is simpler while script mode is more flexible.

This section describes how to synchronize incremental data in Table Store to OSS using the script mode of Data Integration.

## Channels

Script mode of Data Integration

-   Reader: OTSStream Reader
-   Writer: OSSWriter

## Configure Table Store

No prior configurations required.

## Configure OSS

No prior configurations required.

## Configure Data Integration

1.  Create a Table Store data source.

    **Note:**

    -   If you have already created a Table Store data source, skip this step.
    -   If you do not want to create a data source, you can specify the endpoint, instanceName, AccessKeyID, and AccessKeySecret on the subsequent configuration page.
    For more information about how to create a data source.

2.  Create an OSS data source.

    This step is similar to Step 1. You only need to select OSS as the data source.

    **Note:** During parameter configuration of the OSS data source, Endpoint does not contain bucketName.

3.  Create a synchronization task.
    1.  Log on to the [Data Integration console](https://di-cn-shanghai.data.aliyun.com/).
    2.  On the Sync Tasks page, select **Script Mode**.
    3.  In the Import Template dialog box that appears, set **Source Type** to Table Store Stream \(OTS Stream\) and **Type of Objective** to OSS.
    4.  Click **OK** to go to the configuration page.
4.  Set configuration items.
    1.  On the configuration page, templates of OTSStreamReader and OSSWriter are provided. Complete the configurations by referring to the following annotations.

        ```
        {
        "type": "job",
        "version": "1.0",
        "configuration": {
        "setting": {
        "errorLimit": {
         "record": "0"  # Allowed number of errors. If the number of errors exceeds the value, the synchronization task fails.
        },
        "speed": {
         "mbps": "1",  # Maximum traffic of each synchronization task.
         "concurrent": "1"   # Number of concurrent synchronization tasks each time.
        }
        },
        "reader": {
        "plugin": "otsstream",  # Name of the Reader plugin.
        "parameter": {
         "datasource": "", # Name of the Table Store data source. If this parameter is set, you do not need to set endpoint, accessID, accessKey, and instanceName.
         "dataTable": "", # Name of the table in Table Store.
         "statusTable": "TableStoreStreamReaderStatusTable", # Table that stores the Table Store Stream status; using the default value is recommended
         "startTimestampMillis": "",  # Start time of the export. In incremental export mode, the task needs to be executed cyclically, and the start time is different at each execution. Therefore, you must set a variable, for example, ${start_time}.
         "endTimestampMillis": "",  # End time of the export. You must set a variable, for example, ${end_time}.
         "date": "yyyyMMdd",  # Date from which data is exported. This parameter is the same as startTimestampMillis and endTimestampMillis, and therefore must be deleted.
         "mode": "single_version_and_update_only", # Format of the data exported from Table Store Stream. Currently, the parameter must be set to single_version_and_update_only. Add this parameter if it is not in the configuration template.
         "column":[  # Names of the columns to be exported from Table Store to OSS. Add this parameter if it is not in the configuration template. Set this parameter as needed.
                  {
                     "name": "uid"  # Name of the column. It is the primary key column in Table Store.
                  },
                  {
                     "name": "name"  # Name of the column. It is an attribute column in Table Store.
                   },
         ],
         "isExportSequenceInfo": false, # This parameter can only be set to false in single_version_and_update_only mode.
         "maxRetries": 30 # Maximum number of retry times.
        }
        },
        "writer": {
        "plugin": "oss", # Name of the Writer plugin
        "parameter": {
         "datasource": "", # Name of the OSS data source
         "object": "",  # Prefix of the name of the last file to be backed up to OSS. The recommended value is the Table Store instance name, table name, or date, for example, "instance/table/{date}".
         "writeMode": "truncate", # truncate, append, and nonConflict are supported. truncate is used to clear existing files with the same name, append is used to add the data to existing files with the same name, and nonConflict is used to return an error when files with the same name exist.
         "fileFormat": "csv", # File format
         "encoding": "UTF-8", # Encoding mode
         "nullFormat": "null", # Mode of representation in a TXT file under control
         "dateFormat": "yyyy-MM-dd HH:mm:ss", # # Time format
         "fieldDelimiter": "," # Delimiter of each column
        }
        }
        }
        }
        ```

    2.  Click **Save**.
5.  Run the task.
    1.  Click **operation**.
    2.  In the dialog box that appears, set the variable parameters.
    3.  Click **OK**.
    4.  After the task is completed, log on to the [OSS console](https://oss.console.aliyun.com/overview) to verify whether files are backed up.
6.  Configure scheduling.
    1.  Click **Submit**.
    2.  In the dialog box that appears, set the scheduling parameters.

        The parameters are described as follows.

        |Parameter|Description|
        |:--------|:----------|
        |Scheduling type|Select **cycle control**.|
        |Automatically re-run|This parameter indicates that the task reruns for three times at an interval of 2 minutes if the task fails.|
        |Start date|The default value is recommended, which is from January 1, 1970 to 100 years later.|
        |Scheduling cycle|Select Minute.|
        |Start Time|Select “00:00 to 23:59”, which indicates that scheduling is required for a full day.|
        |Interval|Select 5 Minutes.|
        |start\_time|Enter $\[yyyymmddhh24miss-10/24/60\], which indicates the time of the scheduling task minus 10 minutes.|
        |end\_time|Enter $\[yyyymmddhh24miss-5/24/60\], which indicates the time of the scheduling task minus 5 minutes.|
        |date|Enter $\{bdp.system.bizdate\}, which indicates the scheduling date.|
        |Dependency attributes|Set this parameter if a dependency exists. If no dependency exists, do not set this parameter.|
        |Cross-cycle dependency|**Self-dependent**: The operation can continue only after the previous scheduling cycle is completed.|

    3.  Click **OK**.

        The periodic synchronization task is configured, the configuration file status is Read-only.

7.  Check the task.
    1.  At the top of the page, click **Operation Center**.
    2.  On the left-side navigation pane, click **Task List** \> **Cycle Task** to view the created synchronization task.
    3.  The new task begins running at 00:00 on the next day.
        -   In the left-side navigation pane, choose **Task O&M** \> **Cycle Instance** to view each pre-created synchronization task of the day. The scheduling interval is 5 minutes and each task processes data from the past 5 to 10 minutes.
        -   Click the instance name to view its details.

    4.  You can view the log when a task is running or after it is completed.
8.  Check the data exported to OSS.

    Log on to the [OSS console](https://oss.console.aliyun.com/overview?spm=a2c4g.11186623.2.13.308b2f02zFs7NH) to check whether a new file is generated and whether the file content is correct.


Once the preceding settings are completed, data in Table Store can be automatically synchronized to OSS at a latency of 5 to 10 minutes.

