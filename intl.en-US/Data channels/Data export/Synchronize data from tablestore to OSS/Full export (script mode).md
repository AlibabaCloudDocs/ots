# Full export \(script mode\)

Data Integration supports data synchronization in wizard mode and script mode. Wizard mode is simpler, while script mode is more flexible.

This section describes how to export full data in Table Store to OSS using the script mode of Data Integration, so that you can download the data as needed or save it as backup data of Table Store to OSS.

## Channels

Script mode of Data Integration:

-   Reader: OTSReader
-   Writer: OSSWriter

## Step 1. Create a Table Store data source

**Note:** Skip this step if you have created a Table Store data source.

For more information about how to create a data source.

## Step 2. Create an OSS data source

This operation is similar to Step 1. You only need to select OSS as the data source.

**Note:** During parameter configuration of the OSS data source, Endpoint does not contain bucketName.

## Step 3. Create an export task

1.  Log on to the Data Integration console.
2.  On the Sync Tasks page, select **Script Mode**.
3.  In the Import Template dialog box, set **Source Type** to Table Store \(OTS\) and **Type of Objective** to OSS.
4.  Click **OK** to go to the configuration page.

## Step 4. Set configuration items

1.  On the configuration page, templates for OTSReader and OSSWriter are provided. Complete the configurations by referring to the following annotations.

    ```
    {
    "type": "job",    # It cannot be modified.
    "version": "1.0",  # It cannot be modified.
    "configuration": {
     "setting": {
       "errorLimit": {
         "record": "0"  # The import task fails when the number of error records exceeds the value.
       },
       "speed": {
         "mbps": "1",  # Import speed, in Mbps.
         "concurrent": "1"  # Concurrency.
       }
     },
     "reader": {
       "plugin": "ots",  # It cannot be modified.
       "parameter": {
         "datasource": "",   # Name of the data source in Data Integration, which must be set in advance. You can configure data source or write authentication information such as the AccessKeyID in plaintext. We recommend that you configure data source.
         "table": "",    # Table name in Table Store.
         "column": [   # Name of the column that needs to be exported to OSS. If all the columns need to be exported to OSS, set this parameter to an empty array.
           {
             "name": "column1"    # Name of the column in Table Store, which needs to be imported to OSS
           },
           {
             "name": "column2"   # Name of the column in Table Store, which needs to be imported to OSS
           }
         ],
         "range": {
           "begin": [
             {
               "type": "INF_MIN"   # Start position of the first primary key column in Table Store. If you want to export full data, set this parameter to INF_MIN. If you want to export a portion of the data, set this parameter as needed. The number of configuration items in "begin" must be the same as the number of primary key columns.
             }
           ],
           "end": [
             {
               "type": "INF_MAX"   # End position of the first primary key column in Table Store. If you want to export full data, set this parameter to INF_MAX. If you want to export a portion of the data, set this parameter as needed.
             }
           ],
           "split": [  # Used to configure partition information about the Table Store table, which can accelerates the export. In the next version, this configuration is automatically processed.
           ]
         }
       }
     },
     "writer": {
       "plugin": "oss",
       "parameter": {
         "datasource": "",  # Name of the OSS data source
         "object": "",  # Prefix of the object excluding the bucket name, for example, tablestore/20171111/. If the export is scheduled, a variable, for example, tablestore/${date}, must be used, and ${date} must be configured when the scheduling parameters are set.
         "writeMode": "truncate", # truncate, append, and nonConflict are supported. truncate is used to clear existing files with the same name, append is used to add the data to existing files with the same name, and nonConflict is used to return an error when files with the same name exist. truncate is used during full export.
         "fileFormat": "csv", # CSV and TXT are supported.
         "encoding": "UTF-8",  # Encoding mode
         "nullFormat": "null", # Defines a string identifier that represents the null value. It can be an empty string.
         "dateFormat": "yyyy-MM-dd HH:mm:ss",  # Time format
         "fieldDelimiter": "," # Delimiter of each column
       }
     }
    }
    }
    ```

2.  Click **Save** to save the task.

## Step 5. Run the task

1.  Click **operation** to run the task.

    If the configurations contain variables, for example, $\{date\}, the variable setting page is displayed. You can set only specific values.

2.  View logs in the lower part of the page.

    If no error is logged, the task is successfully executed, and you can check the data in the target OSS instance.

    **Note:** Full export is generally a one-time task, and thus you do not need to set automatic scheduling parameters. For more information about how to set the scheduling parameters, see [Incremental synchronization](/intl.en-US/Data channels/Data export/Synchronize data from tablestore to OSS/Incremental synchronization (script mode).md).


## Step 6. Check the data exported to OSS

1.  Log on to the [OSS console](https://oss.console.aliyun.com/index?spm=a2c4g.11186623.2.11.43efb5c053MiTX).
2.  Select the bucket and file name, and verify its contents.

