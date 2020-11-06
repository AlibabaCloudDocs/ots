使用EMR 
==========================

使用EMR的JindoFS缓存模式连接OSS数据湖。

背景信息 
-------------------------

您可以使用EMR的JindoFS缓存模式或者JindoFS块模式连接OSS数据湖。

* 缓存模式（Cache）主要兼容原生OSS存储方式，文件以对象的形式存储在OSS上，每个文件根据实际访问情况会在本地进行缓存，提升EMR集群内访问OSS的效率，同时兼容了OSS原有文件形式，数据访问上能够与其他OSS客户端完全兼容。详情请参见[JindoFS缓存模式使用说明](/intl.zh-CN/SmartData/SmartData基础使用（EMR-3.29.x版本）/JindoFS缓存模式使用说明.md)。

  

* 块存储模式（Block）提供了最为高效的数据读写能力和元数据访问能力。数据以Block形式存储在后端存储OSS上，本地提供缓存加速，元数据则由本地Namespace服务维护，提供高效的元数据访问性能。详情请参见[JindoFS块存储模式使用说明](/intl.zh-CN/SmartData/SmartData基础使用（EMR-3.29.x版本）/JindoFS块存储模式使用说明.md)。

  




前提条件 
-------------------------

* 已创建EMR集群，详情请参见[创建集群](/intl.zh-CN/快速入门/创建集群.md)。

  创建集群时，请注意如下事项：
  * 创建EMR集群和OSS属于同一个阿里云账号，且建议EMR集群和OSS Bucket处于同一地域。

    
  
  * 创建集群时，请打开 **挂载公网** 和 **远程登录** 开关，将集群挂载到公网，用于Shell远程登录服务器。

    
  
  * bigboot与smartdata为后续配置相关服务，如果默认未选中，请选中bigboot与smartdata服务。

    
  

  

* 已创建数据投递任务，详情请参见[快速入门](/intl.zh-CN/功能介绍/数据湖投递/快速入门.md)。

  




操作步骤 
-------------------------

1. 使用EMR的jindoFS缓存模式连接OSS和启用缓存，详情请参见[JindoFS缓存模式使用说明](/intl.zh-CN/SmartData/SmartData基础使用（EMR-3.29.x版本）/JindoFS缓存模式使用说明.md)。

   启用缓存会利用本地磁盘对访问的热数据块进行缓存，默认状态为禁用，即所有OSS读取都直接访问OSS上的数据。缓存启用后，Jindo服务会自动管理本地缓存备份，通过水位清理本地缓存，请根据需求配置一定的比例用于缓存。
   

2. 启动spark SQL。

   1. 通过远程登录工具（例如PuTTY）登录EMR Header服务器。

      
   
   2. 执行如下命令运行Spark SQL。

          spark-sql --master yarn --num-executors 5 --executor-memory 1g --executor-cores 2

      
   

   

3. 使用SQL语句创建指向OSS数据目录的外表。

   请使用通过表格存储控制台获取的SQL语句，如下SQL语句示例仅供参考。

       CREATE EXTERNAL TABLE  lineitem (l_orderkey bigint,l_linenumber bigint,l_receiptdate string,l_returnflag string,l_tax double,l_shipmode string,l_suppkey bigint,l_shipdate string,l_commitdate string,l_partkey bigint,l_quantity double,l_comment string,l_linestatus string,l_extendedprice double,l_discount double,l_shipinstruct string) PARTITIONED BY (`year` int, `month` int) STORED AS PARQUET  LOCATION  'jfs://test/' ;

   

   ![fig001](//static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4001190061/p169744.png)

   通过表格存储控制台获取SQL语句的方法如下：

   在实例的 **数据湖投递** 页面，单击投递任务 **操作** 列的 **建表语句** ，可以查看和复制SQL语句，如下图所示。

   ![pic002](//static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4001190061/p169745.png)
   

4. 执行如下SQL语句，加载OSS数据源中实际的数据分区。

   其中lineitem为创建的外表名称。

       msck repair table lineitem;

   

   ![load](//static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4001190061/p169641.png)
   

5. 查询数据。

       select * from lineitem limit 1;

   

   ![pic003](//static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4001190061/p169746.png)
   



