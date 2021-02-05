# Allow MaxCompute to access Tablestore by using AccessKey pairs

In addition to account authorization, you can access data in Tablestore by using AccessKey pairs on MaxCompute.

## Preparations

Get the AccessKey credentials \(that is, [AccessKeyId and AccessKeySecret](https://www.alibabacloud.com/help/faq-detail/63482.htm)\) for the account that owns the target Tablestore resources. If the AccessKey pair is for a Resource Access Management \(RAM\) user, the RAM user must be granted at least the following permissions to operate the Tablestore resources:

```
{
  "Version": "1",
  "Statement": [
    {
      "Action": [
        "ots:ListTable",
        "ots:DescribeTable",
        "ots:GetRow",
        "ots:PutRow",
        "ots:UpdateRow",
        "ots:DeleteRow",
        "ots:GetRange",
        "ots:BatchGetRow",
        "ots:BatchWriteRow",
        "ots:ComputeSplitPointsBySize"
      ],
      "Resource": "*",
      "Effect": "Allow"
    }
  ]
}

--You can also add other permissions.
			
```

## Allow MaxCompute to access Tablestore by using AccessKey pairs

Unlike account authorization, you must specify the AccessKey pairs information in the `LOCATION` clause when you create an external table.

```
LOCATION 'tablestore://${AccessKeyId}:${AccessKeySecret}@${InstanceName}. ${Region}.ots-internal.aliyuncs.com'
			
```

Assume that the following information is what MaxCompute must access:

|AccessKeyId|AccessKeySecret|Instance name|Region|Network mode|
|:----------|:--------------|:------------|:-----|:-----------|
|abcd|1234|cap1|cn-hangzhou|Intranet access|

The statement to create the external table is:

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
'tablestore.table.name'='vehicle_track'
)
LOCATION 'tablestore://abcd:1234@cap1.cn-hangzhou.ots-internal.aliyuncs.com'
			
```

For more information about accessing data, see the "Step 3: Use an external table to access Tablestore data" section of the [Use MaxCompute to access Tablestore](/intl.en-US/Compute-Analysis/MaxCompute /Use MaxCompute to access Tablestore.md) topic.

