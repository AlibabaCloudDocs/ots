# Use Function Compute to cleanse data

Featured by highly concurrent write performance and low storage cost, Tablestore is suitable for the storage of IoT data, logs, and monitoring data. You can write data to Tablestore, use Function Compute to cleanse the data, and then write the cleansed data back to Tablestore. In addition, you can access the original data and cleansed data in a real-time manner.

## Scenario

You want to write log data that includes three fields to Tablestore. To efficiently query the logs, you need to write the logs in which the value of the level field is larger than 1 to another table. The following table describes the fields included in the logs.

|Field|Type|Description|
|:----|:---|:----------|
|id|Integer|The ID of the log.|
|level|Integer|The level of the log. A larger value indicates a higher level.|
|message|String|The content of the log.|

## Create a function and a trigger

Before your create a trigger, you must enable the stream feature for the table to allow the function to process incremental data written to the table. A trigger can be bound only to an existing function. Therefore, you must create the function in the same region as that of the Tablestore instance.

1.  Create a table and enable the stream feature for the table.

    1.  Long on to the Tablestore console and [create an instance](/intl.en-US/Quick Start/Create instances.md).

    2.  [Create a table](/intl.en-US/Quick Start/Create tables.md) in the created instance and enable the stream feature for the table.

2.  Create a function.

    1.  Log on to the [Function Compute console](https://fc.console.aliyun.com/#/serviceList/).

    2.  In the left-side navigation pane, click **Service/Function**.

    3.  On the Service/Function page, click **Create Function**.

    4.  On the Create Function page, click **Event Function** and then click **Next**.

    5.  Configure the function based on your requirements. Click **Create**.

        -   In this example, Function Name is set to etl\_test and Runtime is set to python2.7.
        -   Funtion Handler is set to etl\_test.handler.
        ![fig_newfunction](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7432243061/p128130.png)

3.  Configure services.

    1.  In the left-side navigation pane, click **Service/Function**.

    2.  On the Service/Function page, click **Service Configurations**.

    3.  On the **Service Configurations** tab, configure the role for the service. For more information, see [Permissions](https://www.alibabacloud.com/help/doc-detail/52885.htm).

        The function needs to write logs to Log Service and perform read and write operations on Tablestore tables. Therefore, you must authorize Function Compute to perform these operations. In this example, the AliyunOTSFullAccess and AliyunLogFullAccess permissions are granted to the role. We recommend that you follow the principle of least privilege \(PoLP\) when you grant permissions to the role.

        ![fig_rule](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7432243061/p128151.png)

4.  Modify the function code.

    1.  On the Service/Function page, click the name of the function you want to modify.

    2.  On the details page of the function, click **code**.

    3.  On the Code tab, modify and save the function code.

        Set the following parameters to actual values: INSTANCE\_NAME, REGION, and ENDPOINT.

        ![fig_code](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7432243061/p128192.png)

        The following code provides an example of the function used to cleanse log data:

        ```
        #! /usr/bin/env python
        # -*- coding: utf-8 -*-
        import cbor
        import json
        import tablestore as ots
        INSTANCE_NAME = 'distribute-test'
        REGION = 'cn-shanghai'
        ENDPOINT = 'http://%s.%s.vpc.tablestore.aliyuncs.com'%(INSTANCE_NAME, REGION)
        RESULT_TABLENAME = 'result'
        def _utf8(input):
            return str(bytearray(input, "utf-8"))
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
        # The obtained credentials can be used to access Tablestore because the AliyunOTSFullAccess permission is granted to the role.
        def get_ots_client(context):
            creds = context.credentials
            client = ots.OTSClient(ENDPOINT, creds.accessKeyId, creds.accessKeySecret, INSTANCE_NAME, sts_token = creds.securityToken)
            return client
        def save_to_ots(client, record):
            id = int(get_pk_value(record, 'id'))
            level = int(get_attrbute_value(record, 'level'))
            msg = get_attrbute_value(record, 'message')
            pk = [(_utf8('id'), id),]
            attr = [(_utf8('level'), level), (_utf8('message'), _utf8(msg)),]
            row = ots.Row(pk, attr)
            client.put_row(RESULT_TABLENAME, row)
        def handler(event, context):
            records = cbor.loads(event)
            #records = json.loads(event)
            client = get_ots_client(context)
            for record in records['Records']:
                level = int(get_attrbute_value(record, 'level'))
                if level > 1:
                    save_to_ots(client, record)
                else:
                    print "Level <= 1, ignore."
        ```

5.  Create and test a Tablestore trigger.

    **Note:** If you use Function Compute in the Tablestore console for the first time, authorize Tablestore to send event notifications in the previous version of the Tablestore console.

    1.  On the Trigger tab in the details page of the table, click **Use Existing Function Compute**.

    2.  In the Create Triggers dialog box that appears, select the Function Compute service and function, and enter the name of the trigger.

        **Note:** When you create a trigger in the previous version of the Tablestore console for the first time, click **Grant Tablestore the permission to send event notifications.**

        After authorization, you can see the AliyunTableStoreStreamNotificationRole role that is automatically created in the [RAM console](https://ram.console.aliyun.com/).

    3.  Click **OK**.


## Test the function

After you create the function and trigger, write data to Tablestore and query the data to verify whether the data is cleansed as expected.

Write data to the table named source\_data. Enter the values of the id, level, and message fields and query the cleansed data in the table named result. Fore more information about how to write and query data, see [Read and write data in the console](/intl.en-US/Quick Start/Read and write data in the console.md).

-   When you write a log in which the value of the level field is larger than 1 to the source\_data table, the log is synchronized to the result table.
-   When you write a log in which the value of the level field is equal to or smaller than 1 to the source\_data table, the log is not synchronized to the result table.

