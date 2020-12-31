# Cross-account authorization

This topic describes how to establish a seamless connection between Tablestore and MaxCompute across Alibaba Cloud accounts.

**Note:** To learn about how to connect Tablestore to MaxCompute within the same account, see [Use MaxCompute to access Tablestore](/intl.en-US/Compute-Analysis/MaxCompute /Use MaxCompute to access Tablestore.md).

## Preparations

Prepare two Alibaba Cloud accounts. If Account A grants access permissions to Account B, Account B can be used to access table data of Account A when you run MaxCompute. The following table describes the basic information.

**Note:** The following information is for reference only. Replace the information with actual information during operations.

|Item|Tablestore|MaxCompute|
|:---|:---------|:---------|
|Alibaba Cloud account name|Account A|Account B|
|UserId|12345|56789|

Before you use MaxCompute to access Tablestore, make sure that the following preparations are made:

1.  Activate MaxCompute and create a workspace for Account B. For more information, see [MaxCompute](https://www.alibabacloud.com/product/maxcompute) and [Create a workspace]().

2.  Create an AccessKey pair for each of Account A and Account B. For more information, see [Create an AccessKey pair](https://www.alibabacloud.com/help/doc-detail/53045.htm).

3.  Use Account A to log on to the [RAM console](https://ram.console.aliyun.com/). On the RAM Roles page, create a RAM role. In the example, the created role name is AliyunODPSRoleForOtherUser.

4.  In the list of RAM roles, find the AliyunODPSRoleForOtherUser role. Click the RAM role name, and set the policy content. Follow the code to set the policy content:

    ```
    {
      "Statement": [
        {
          "Action": "sts:AssumeRole",
          "Effect": "Allow",
          "Principal": {
            "Service": [
              "1xxxx@odps.aliyuncs.com"
            ]
          }
        }
      ],
      "Version": "1"
    }
                            
    ```

    **Note:** Replace 1xxxx with your UID.

5.  After the RAM role is created, view the Alibaba Cloud Resource Name \(ARN\) of the role on the Basic Information page.

6.  Go back to the RAM console homepage. Go the Policies page. Click **Create Policy**.

7.  On the RAM Roles page, find the AliyunODPSRoleForOtherUser role. Then, click **Add Permissions**.

8.  In the Add Permissions panel, select the AliyunODPSRolePolicyForOtherUse permission. Click **OK**.

9.  In the Tablestore console, create an instance and a table. For more information, see [Create instances](/intl.en-US/Quick Start/Create instances.md) and [Create tables](/intl.en-US/Quick Start/Create tables.md).

    In this example, the following content describes the information about the created instance and table:

    -   Instance name: cap1
    -   Table name: vehicle\_track
    -   Primary key information: vid \(integer\) and gt \(integer\)
    -   Endpoint: `https://cap1.cn-hangzhou.ots-internal.aliyuncs.com`

        **Note:** When you use MaxCompute to access Tablestore, we recommend that you use the internal network address of Tablestore.

    -   Set the network type to access the instance to **Any Network**.

## Use MaxCompute to access Tablestore

Repeat the steps described in Use MaxCompute to access Tablestore. You must specify a roleArn when you create an external table for cross-account access.

Account B is used to create an external table by using MaxCompute and access Tablestore by using the roleArn created in [Preparations](#preparation).

For more information about specific operations, see [Use MaxCompute to access Tablestore](/intl.en-US/Compute-Analysis/MaxCompute /Use MaxCompute to access Tablestore.md). In Step 2, when you create an external table, use the following code:

```
CREATE EXTERNAL TABLE ads_log_ots_pt_external
(
vid bigint,
gt bigint,
longitude double,
latitude double,
distance double ,
speed double,
oil_consumption double
)
STORED BY 'com.aliyun.odps.TableStoreStorageHandler'
WITH SERDEPROPERTIES (
'tablestore.columns.mapping'=':vid, :gt, longitude, latitude, distance, speed, oil_consumption',
'tablestore.table.name'='vehicle_track',
'odps.properties.rolearn'='acs:ram::12345:role/aliyunodpsroleforotheruser'
)
LOCATION 'tablestore://cap1.cn-hangzhou.ots-internal.aliyuncs.com'
USING 'odps-udf-example.jar'
            
```

