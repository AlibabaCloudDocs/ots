# Use MaxCompute to access Tablestore

This topic describes how to establish a seamless connection between Tablestore and MaxCompute that belongs to the same Alibaba Cloud account.

## Background information

[MaxCompute](https://www.alibabacloud.com/product/maxcompute) is a cost-effective and fully managed platform for petabytes of data warehousing. You can use this service to process and analyze large amounts of data in a fast and efficient manner. You can execute a simple Data Definition Language \(DDL\) statement to create an external table in MaxCompute. Then, you can use this table to associate MaxCompute with external data sources. This allows access to and output of data in various formats. MaxCompute tables can contain only structured data. However, external tables can contain either structured or non-structured data.

Tablestore and MaxCompute have their own type systems, and the following table lists their mappings.

|Tablestore|MaxCompute|
|:---------|:---------|
|STRING|STRING|
|INTEGER|BIGINT|
|DOUBLE|DOUBLE|
|BOOLEAN|BOOLEAN|
|BINARY|BINARY|

## Preparations

Before you use MaxCompute to access Tablestore, make sure that the following preparations are made:

1.  Activate [MaxCompute](https://www.alibabacloud.com/product/maxcompute).

2.  [Create a workspace]().

3.  Create an AccessKey pair for the RAM user.

4.  Create a role in the RAM console to authorize MaxCompute to access Tablestore. For more information, see [Cross-account authorization](/intl.en-US/Compute-Analysis/MaxCompute /Cross-account authorization.md).

5.  In the Tablestore console, create an instance and a table. For more information, see [Create instances](/intl.en-US/Quick Start/Create instances.md) and [Create tables](/intl.en-US/Quick Start/Create tables.md).

    In this example, the following content describes the information about the created instance and table:

    -   Instance name: cap1
    -   Table name: vehicle\_track
    -   Primary key information: vid\(integer\) and gt\(integer\)
    -   Endpoint: `https://cap1.cn-hangzhou.ots-internal.aliyuncs.com`

        **Note:** When you use MaxCompute to access Tablestore, we recommend that you use the internal network address of Tablestore.

    -   Set the network type to access the instance to **Any Network**.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3515309951/p11958.png)


## Step 1: Install the client

1.  Download the [MaxCompute client](https://github.com/aliyun/aliyun-odps-console/releases) package and download the package.

    **Note:** Make sure that your machine is installed with JRE 1.7 or later.

2.  Edit the conf/odps\_config.ini file, and configure the client, as shown in the following code:

    ```
    access_id=*******************
    access_key=*********************
     # Specify the AccessKey ID and AccessKey secret of your Alibaba Cloud account. To obtain the AccessKey ID and AccessKey secret, log on to the Alibaba Cloud Management Console and view the AccessKey pair on the AccessKey Management page.
    project_name=my_project
     # Specify the name of the project that you want to access.
    end_point=https://service.odps.aliyun.com/api
     # Specify the endpoint of MaxCompute.
    tunnel_endpoint=https://dt.odps.aliyun.com
     # Specify the link to access the MaxCompute Tunnel service.
    log_view_host=http://logview.odps.aliyun.com
     # Specify the LogView URL that the client returns after a job is run. After you access the LogView URL, you can view detailed information of the job.
    https_check=true
     # Specify whether to enable access over HTTPS.
    ```

    **Note:** In the odps\_config.ini file, the number sign \(`#`\) is used to add comments. The MaxCompute client uses `--` to add comments.

3.  Run bin/odpscmd.bat. Enter `show tables;`.

    If the tables in the current MaxCompute projects are displayed, the preceding configurations are valid.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3515309951/p11959.png)


## Step 2. Create an external table

Create a MaxCompute data table ots\_vehicle\_track and associate it with a Tablestore table vehicle\_track.

Information of the associated Tablestore table:

-   Instance name: cap1
-   Table name: vehicle\_track
-   Primary key information: vid\(int\) and gt\(int\)
-   Endpoint: `https://cap1.cn-hangzhou.ots-internal.aliyuncs.com`

```
CREATE EXTERNAL TABLE IF NOT EXISTS ots_vehicle_track
(
vid bigint,
gt bigint,
longitude double,
latitude double,
distance double ,
speed double,
oil_consumption double
)
STORED BY 'com.aliyun.odps.TableStoreStorageHandler' -- (1)
WITH SERDEPROPERTIES ( -- (2)
'tablestore.columns.mapping'=':vid, :gt, longitude, latitude, distance, speed, oil_consumption', -- (3)
'tablestore.table.name'='vehicle_track' -- (4)
)
LOCATION 'tablestore://cap1.cn-hangzhou.ots-internal.aliyuncs.com'; -- (5)
```

The following table lists the parameters.

|Label|Parameter|Description|
|:----|:--------|:----------|
|\(1\)|com.aliyun.odps.TableStoreStorageHandler|StorageHandler built in MaxCompute. StorageHandler processes Tablestore data. StorageHandler defines the interaction between MaxCompute and Tablestore. MaxCompute implements related logic.|
|\(2\)|SERDEPROPERITES|The operation that provides parameter options. The tablestore.columns.mapping and tablestore.table.name parameters are required for TableStoreStorageHandler.|
|\(3\)|tablestore.columns.mapping|Required. The columns of the Tablestore table to be accessed by MaxCompute. The columns include primary key columns and attribute columns. Columns that contain colons \(`:`\) are the primary key columns of Tablestore. Examples: `:vid` and `:gt`. Other columns in the example are attribute columns. You must specify all primary key columns of the table in Tablestore when you specify the mapping. You need only to specify the attribute columns that MaxCompute accesses instead of specifying all attribute columns.|
|\(4\)|tablestore.table.name|The name of the Tablestore table to be accessed. If the specified name of the Tablestore table does not exist, an error occurs. MaxCompute does not proactively create Tablestore tables.|
|\(5\)|LOCATION|The information of the Tablestore instance to be accessed. The information includes the name of the instance and endpoint.|

## Step 3: Use an external table to access Tablestore data

After you create an external table, Tablestore data is imported to MaxCompute. Then, you can use MaxCompute SQL commands to access Tablestore data.

```
// Collect statistics for the average speed and fuel consumption of vehicles whose VIDs are smaller than 4 before the timestamp 1469171387.
select vid,count(*),avg(speed),avg(oil_consumption) from ots_vehicle_track where vid <4 and gt<1469171387  group by vid;
```

A similar output is returned.

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3515309951/p11960.jpeg)

