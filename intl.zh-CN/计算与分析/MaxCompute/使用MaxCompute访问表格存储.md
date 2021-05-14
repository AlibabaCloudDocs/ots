# 使用MaxCompute访问表格存储

本文介绍如何在同一个云账号下实现表格存储和MaxCompute之间的无缝连接。

## 背景信息

[MaxCompute](https://www.alibabacloud.com/product/maxcompute)是一项大数据计算服务，它能提供快速、完全托管的PB级数据仓库解决方案，使您可以经济并高效地分析处理海量数据。您只需通过一条简单的DDL语句，即可在MaxCompute上创建一张外部表，建立MaxCompute表与外部数据源的关联，提供各种数据的接入和输出能力。MaxCompute表是结构化的数据，而外部表可以不限于结构化数据。

表格存储与MaxCompute都有其自身的类型系统，两者之间的类型对应关系如下表所示。

|Tablestore|MaxCompute|
|:---------|:---------|
|STRING|STRING|
|INTEGER|BIGINT|
|DOUBLE|DOUBLE|
|BOOLEAN|BOOLEAN|
|BINARY|BINARY|

## 准备工作

使用MaxCompute访问表格存储前，您需要完成以下准备工作：

1.  开通[MaxCompute服务](https://www.alibabacloud.com/product/maxcompute)。

2.  [创建工作空间]()。

3.  创建AccessKey。

4.  在RAM控制台授权MaxCompute访问表格存储的权限。具体请参见[跨账号授权](/intl.zh-CN/计算与分析/MaxCompute/跨账号授权.md)。

5.  在表格存储控制台[创建实例](/intl.zh-CN/快速入门/创建实例.md)和[创建数据表](/intl.zh-CN/快速入门/创建数据表.md)。

    在本示例中，创建的表格存储实例和数据表信息如下：

    -   实例名称：cap1
    -   数据表名称：vehicle\_track
    -   主键信息：vid\(integer\)，gt\(integer\)
    -   访问域名：`https://cap1.cn-hangzhou.ots-internal.aliyuncs.com`

        **说明：** 使用MaxCompute访问表格存储时，建议使用表格存储的私网地址。

    -   设置实例网络类型为**允许任意网络访问**。

        ![odps3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6205309951/p11958.png)


## 步骤一：安装并配置客户端

1.  下载[MaxCompute客户端](https://github.com/aliyun/aliyun-odps-console/releases)并解压。

    **说明：** 确保您的机器上已安装JRE 1.7或以上版本。

2.  编辑conf/odps\_config.ini文件，对客户端进行配置，如下所示。

    ```
    access_id=*******************
    access_key=*********************
     # Accesss ID及Access Key是用户的云账号信息，可登录阿里云官网，进入管理控制台 — accesskeys页面进行查看。
    project_name=my_project
     # 指定用户想进入的项目空间。
    end_point=https://service.odps.aliyun.com/api
     # MaxCompute服务的访问链接。
    tunnel_endpoint=https://dt.odps.aliyun.com
     # MaxCompute Tunnel服务的访问链接。
    log_view_host=http://logview.odps.aliyun.com
     # 当用户执行一个作业后，客户端会返回该作业的LogView地址。打开该地址将会看到作业执行的详细信息。
    https_check=true
     # 决定是否开启HTTPS访问。
    ```

    **说明：** odps\_config.ini文件中使用`#`作为注释，MaxCompute客户端内使用`--`作为注释。

3.  运行bin/odpscmd.bat，输入`show tables;`。

    如果显示当前MaxCompute项目中的表，则表示上述配置正确。

    ![odps2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6205309951/p11959.png)


## 步骤二：创建外部表

创建一张MaxCompute的数据表（ots\_vehicle\_track）关联到Tablestore的某一张表（vehicle\_track）。

关联的数据表信息如下。

-   实例名称：cap1
-   数据表名称：vehicle\_track
-   主键信息：vid\(int\)，gt\(int\)
-   访问域名：`https://cap1.cn-hangzhou.ots-internal.aliyuncs.com`

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

参数说明如下：

|标号|参数|说明|
|:-|:-|:-|
|（1）|com.aliyun.odps.TableStoreStorageHandler|MaxCompute内置的处理Tablestore数据的StorageHandler，定义了MaxCompute和Tablestore的交互，相关逻辑由MaxCompute实现。|
|（2）|SERDEPROPERITES|可以理解为提供参数选项的接口，在使用TableStoreStorageHandler时，有两个必须指定的选项，分别是tablestore.columns.mapping和tablestore.table.name。|
|（3）|tablestore.columns.mapping|必填选项。MaxCompute将要访问的Tablestore表的列，包括主键和属性列。其中，带`:`的表示Tablestore主键，例如本示例中的`:vid与``:gt`，其他均为属性列。在指定映射的时候，用户必须提供指定Tablestore表的所有主键，属性列无需全部提供，可以只提供需要通过MaxCompute来访问的属性列。|
|（4）|tablestore.table.name|需要访问的Tablestore表名。 如果指定的Tablestore表名错误（不存在），则会报错。MaxCompute不会主动创建Tablestore表。|
|（5）|LOCATION|指定访问的Tablestore的实例信息，包括实例名和endpoint等。|

## 步骤三：通过外部表访问Tablestore数据

创建外部表后，Tablestore的数据便引入到了MaxCompute生态中，您可通过MaxCompute SQL命令来访问Tablestore数据。

```
//统计编号4以下的车辆在时间戳1469171387以前的平均速度和平均油耗。
select vid,count(*),avg(speed),avg(oil_consumption) from ots_vehicle_track where vid <4 and gt<1469171387  group by vid;
```

返回类似如下结果：

![odps1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6205309951/p11960.jpeg)

