# Use Function Compute for data cleansing {#concept_hz2_dtj_bfb .concept}

This document describes how to use Function Compute to cleanse the data in Table Store.

The highly concurrent write performance and low storage cost of Table Store make it suitable for Internet of Things \(IoT\) applications, including storing logs and monitoring data. You can write data to Table Store, simultaneously perform a simple cleansing of the newly added data in Function Compute, and write the cleansed data back to the Table Store result table. Meanwhile, you can access the original data and the result data in real time.

## Data definition {#section_kzl_ggh_dfb .section}

Assume that the data to be written is log data, which contains three fields:

|Field name|Type|Meaning|
|:---------|:---|:------|
|id|Integer|Log ID|
|level|Integer|Log level \(the greater the number, the higher the level\)|
|message|String|Log content|

Logs of level\>1 must be written to another data table for dedicated querying.

## Creating instances and data tables {#section_cnk_1hh_dfb .section}

Create a Table Store instance in the [Table Store console](https://partners-intl.console.aliyun.com/#/ots)（this time using East China Node 2 distribute-test as an example）, and create a source table（source\_data）and a result table（result）. The primary key is id \(integer\). Since Table Store uses a schemafree structure, other attribute column fields need not be predefined.

Taking source\_data as an example, create as in the following diagram:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20323/153968014811925_en-US.png)

## Enabling the streaming function of the data source table {#section_xqq_nhh_dfb .section}

The trigger function requires that the [Stream function](../../../../reseller.en-US/Functions/Stream/Overview.md#) of the data table be enabled before the incremental data written in Table Store can be processed in Function Compute.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20323/153968014811927_en-US.png)

Expiration time of Stream records is the maximum time for incremental data to be read through the stream API.

Since the trigger can bind only existing functions, first create services and functions in the same region on the Function Compute console.

## Create a Function Compute service {#section_unw_nhg_mfb .section}

The following process describes creating services and processing functions on the [Function Compute console](https://partners-intl.console.aliyun.com/#/fc), using East China Node 2 as an example.

1.  Create a service in East China Node 2.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20323/153968014811928_en-US.png)

2.  Create a function and select: **Blank function** \> **Do not create a trigger**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20323/153968014811929_en-US.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20323/153968014911930_en-US.png)

    -   The function name is: etl\_test. Select the Python 2.7 environment, and edit code online
    -   The function entry is: etl\_test.handler
    -   The code is edited later. Now click **Next**.
3.  Service authorization

    Since Function Compute writes the running log to the log service and simultaneously reads and writes the Table Store data tables, Function Compute must have certain permissions. For convenience, first add AliyunOTSFullAccess and AliyunLogFullAccess permissions. In actual usage, we recommend that you add permissions based on the principle of least privilege.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20323/153968014911931_en-US.png)

4.  Click **Complete authorization** and create a function.
5.  Modify the function code.

    After creating the function, click the corresponding `Function`—`Run Code`, then edit the code and save it. Modify INSTANCE\_NAME \(the Table Store instance name\) and REGION \(region used\) as appropriate:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20323/153968014911932_en-US.png)

    Example code:

    ```
    #! /usr/bin/env python
    # -*- coding: utf-8 -*-
    import cbor
    import json
    import tablestore as ots
    INSTANCE_NAME = 'distribute-test'
    REGION = 'cn-shanghai'
    ENDPOINT = 'http://%s.%s.ots.aliyuncs.com'%(INSTANCE_NAME, REGION)
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
    #Because the AliyunOTSFullAccess permission has been granted, the credentials obtained here are authorized to access Table Store.
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


## Binding a Trigger {#section_qfj_zjh_dfb .section}

1.  Go back to the instance management page of Table Store and click the **Use Trigger** button behind the source\_data table to enter the trigger binding interface. Click **Use an existing function**, select the newly created service and function, and check the `Send event notification of Table Store` permission for confirmation.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20323/153968014911933_en-US.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20323/153968014911934_en-US.png)

2.  After binding is successful, you will see the following information:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20323/153968014911935_en-US.png)


## Run verification {#section_edb_rlh_dfb .section}

1.  Write data to the source\_data table.

    In the **Data Editor** page of source\_data , click **Insert**, and enter the id, level and message information in sequence as follows.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20323/153968015011936_en-US.png)

2.  Query the cleansed data from the result table

    Click on the **Data Editor** page of the result table, where you can query the data that is newly written to source\_data.

    Data of level <= 1 written to soure\_data is not synchronized to the result table.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20323/153968015011940_en-US.png)


