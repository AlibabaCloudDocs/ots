# Use Function Compute

You can use Function Compute to perform real-time computing on incremental data stored in Tablestore.

Alibaba Cloud Function Compute is an event-driven computing service that allows you to focus on writing and uploading code without the need to manage servers. Function Compute prepares computing resources for you and runs your code in an elastic and reliable way. You need to pay only for the resources that are consumed when the code is run. For more information, see [What is Function Compute?](https://www.alibabacloud.com/help/doc-detail/52895.htm) For examples on how to use Function Compute, see [Examples on how to use Function Compute in Tablestore](https://yq.aliyun.com/articles/672331).

Tablestore Stream is a data channel used to obtain incremental data in Tablestore tables. By creating [Tablestore triggers](https://yq.aliyun.com/articles/679488), Tablestore Stream and Function Compute can be automatically docked. This allows the custom program logic in the computing function to automatically process changed data in Tablestore tables.

## Scenarios

The tasks that you can use Function Compute to perform.

-   Data synchronization: You can use Function Compute to synchronize real-time data stored in Tablestore to data caches, search engines, or other database instances.
-   Data archiving: You can use Function Compute to incrementally archive data stored in Tablestore to OSS for cold archiving.
-   Event-driven application: You can create triggers to trigger functions to call API operations provided by IoT suites or cloud-based applications or send notifications.

## Configure a Tablestore trigger

You can create a Tablestore trigger in the Tablestore console to process the real-time data stream generated by the incremental data in a Tablestore table.

1.  Create a table and enable the stream feature for the table.

    1.  Long on to the Tablestore console and [create an instance](/intl.en-US/Quick Start/Create instances.md).

    2.  [Create a table](/intl.en-US/Quick Start/Create tables.md) in the created instance and enable the stream feature for the table.

2.  Create a function.

    1.  Log on to the [Function Compute console](https://fc.console.aliyun.com/#/serviceList/).

    2.  In the left-side navigation pane, click **Service/Function**.

    3.  On the Service/Function page, click **Create Function**.

    4.  On the Create Function page, click **Event Function** and then click **Next**.

    5.  Configure the parameters based on your requirements, and then click **Create**.

        ![fig_newfunction](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7232243061/p120852.png)

3.  Configure services.

    1.  In the left-side navigation pane, click **Service/Function**.

    2.  On the Service/Function page, click **Service Configurations**.

    3.  On the **Service Configurations** tab, configure roles used to authorize the function to collect logs and access other resources. For more information, see [Permissions](https://www.alibabacloud.com/help/doc-detail/52885.htm).

4.  Create and test a Tablestore trigger.

    **Note:** If you use Function Compute in the Tablestore console for the first time, authorize Tablestore to send event notifications in the previous version of the Tablestore console.

    1.  On the Trigger tab in the details page of the table, click **Use Existing Function Compute**.

    2.  In the Create Triggers dialog box that appears, select the Function Compute service and function, and enter the name of the trigger.

        **Note:** When you create a trigger in the previous version of the Tablestore console for the first time, click **Grant Tablestore the permission to send event notifications.**

        After authorization, you can see the AliyunTableStoreStreamNotificationRole role that is automatically created in the [RAM console](https://ram.console.aliyun.com/).

    3.  Click **OK**.


## Data Processing

-   Data format

    A Tablestore trigger encodes the incremental data in the [CBOR](http://cbor.io/) format to construct a Function Compute events. The following code provides an example of the format of incremental data:

    ```
    {
        "Version": "string",
        "Records": [
            {
                "Type": "string",
                "Info": {
                    "Timestamp": int64
                },
                "PrimaryKey": [
                    {
                        "ColumnName": "string",
                        "Value": formated_value
                    }
                ],
                "Columns": [
                    {
                        "Type": "string",
                        "ColumnName": "string",
                        "Value": formated_value,
                        "Timestamp": int64
                    }
                ]
            }
        ]
    }
    ```

-   Elements

    The following table describes the elements included in the preceding format.

    |Element|Description|
    |-------|-----------|
    |Version|The version of the payload, which is Sync-v1. Data type: string.|
    |Records|The array that contains the incremental data in the table. This element includes the following members:     -   Type: the type of the columns. Valid values: PutRow, UpdateRow, and DeleteRow. Data type: string.
    -   Info: includes the Timestamp member, which indicates the last time when the row was modified. The value of Timestamp must be in the UTC format. Data type: int64. |
    |PrimaryKey|The array that stores the primary key column. This element includes the following members:     -   ColumnName: the name of the primary key column. Data type: string.
    -   Value: the content of the primary key column. Data type: formated\_value. Valid values: integer, string, and blob. |
    |Colunms|The array that stores the attribute columns. This element includes the following members:     -   Type: the type of the attribute column. Valid values: PutOneVersion, DeleteOneVersion, and DeleteAllVersions. Data type: string.
    -   ColumnName: the name of the attribute column. Data type: string.
    -   Value: the content of the attribute column. Data type: formated\_value. Valid values: integer, boolean, double, string, and blob.
    -   Timestamp: the last time when the attribute column was modified. The value of Timestamp must be in the UTC format. Data type: int64. |

-   Sample data

    ```
    {
        "Version": "Sync-v1",
        "Records": [
            {
                "Type": "PutRow",
                "Info": {
                    "Timestamp": 1506416585740836
                },
                "PrimaryKey": [
                    {
                        "ColumnName": "pk_0",
                        "Value": 1506416585881590900
                    },
                    {
                        "ColumnName": "pk_1",
                        "Value": "2017-09-26 17:03:05.8815909 +0800 CST"
                    },
                    {
                        "ColumnName": "pk_2",
                        "Value": 1506416585741000
                    }
                ],
                "Columns": [
                    {
                        "Type": "Put",
                        "ColumnName": "attr_0",
                        "Value": "hello_table_store",
                        "Timestamp": 1506416585741
                    },
                    {
                        "Type": "Put",
                        "ColumnName": "attr_1",
                        "Value": 1506416585881590900,
                        "Timestamp": 1506416585741
                    }
                ]
            }
        ]
    }
    ```


## Online debugging

Function Compute supports online debugging for functions. You can construct an event to trigger the function and test whether the function logic is as expected.

Tablestore events that trigger Function Compute are in the CBOR format, which is a JSON-like binary format. Therefore, you can perform online debugging in the following methods:

1.  Add both "import CBOR" and "import JSON" in the code.

2.  On the Service/Function page, click the name of the function that you want to debug.

3.  On the details page of the function, click **Code**.

4.  On the Code tab, click **Event**. On the Test Event panel that appears, select **Custom**. Copy the preceding sample data to the edit box and modify the data based on your requirements. Click **OK**.

5.  Test the function.

    1.  Add `records = json.loads(event)` in the code to process custom events. Click **Save and Invoke**. Check whether the results are as expected.

    2.  After you test the function by using `records=json.loads(event)`, you can modify this line of code to `records = cbor.loads(event)` and click **Save**. This way, the corresponding function logic is triggered when data is written to the Tablestore table.

    ![fig_functionname_001](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7232243061/p127029.png)

    Sample code:

    ```
    import logging
    import cbor
    import json
    def get_attrbute_value(record, column):
        attrs = record[u'Columns']
        for x in attrs:
            if x[u'ColumnName'] == column:
                return x['Value']
    def get_pk_value(record, column):
        attrs = record[u'PrimaryKey']
        for x in attrs:
            if x['ColumnName'] == column:
                return x['Value']
    def handler(event, context):
        logger = logging.getLogger()
        logger.info("Begin to handle event")
        #records = cbor.loads(event)
        records = json.loads(event)
        for record in records['Records']:
            logger.info("Handle record: %s", record)
            pk_0 = get_pk_value(record, "pk_0")
            attr_0 = get_attrbute_value(record, "attr_0")
        return 'OK'
    ```

