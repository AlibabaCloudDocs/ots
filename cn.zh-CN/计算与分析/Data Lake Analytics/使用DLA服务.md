# 使用DLA服务

开通服务后，可通过控制台、MySQL Client以及JDBC这三种方式接入DLA服务并进行SQL查询。

## Tablestore和DLA元信息映射逻辑

-   库和表等概念映射

    |Tablestore|DLA|
    |:---------|:--|
    |实例（instance）|schema或database|
    |表（table）|table|
    |主键列（pk）|column，isPrimaryKey=true，isNullable=false|
    |非主键列（column）|column，isPrimaryKey=false，isNullable=<用户的DDL定义\>|

-   字段的映射关系

    |Tablestore|DLA|
    |:---------|:--|
    |INTEGER（8 Bytes）|Bigint（8 Bytes）|
    |STRING|Varchar|
    |BINARY|Varbinary（目前主键中不支持）|
    |DOUBLE|Double|
    |BOOLEAN|Byte|


## 控制台访问DLA

控制台访问DLA步骤如下：

1.  使用邮件中随附的该地域的用户名和密码登录数据库。

2.  为Tablestore中的实例表格数据建立映射。


假设您在上海地域已创建一个名为sh\_tpch的实例，该实例包含表格test001，表格内包含2行测试数据。该实例建立映射的步骤如下：

1.  将Tablestore的实例映射成DLA的一个DataBase实例。建立DLA的Database映射前，首先需要在Tablestore中创建实例，如创建一个名为sh-tpch的实例，对应的endpoint为sh-tpch.cn-shanghai.ots.aliyuncs.com。

    完成测试实例创建后，执行下列语句建立Database映射：

    ```
    CREATE SCHEMA sh_tpch001 with DBPROPERTIES(LOCATION ='https://sh-tpch.cn-shanghai.ots.aliyuncs.com', catalog='ots', instance ='sh-tpch');                   
    ```

    **说明：** 使用MySQL Client时，可以使用create database或create schema语句创建database映射。但是控制台目前只支持create schema语句创建database映射。

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/20335/156646314712014_zh-CN.png)

2.  在tp\_tpch001的Database下建立表格的映射。在建立DLA的表格映射前，首先需要在Tablestore中创建数据表。

    数据表创建完成后，执行下列语句建立表格映射：

    ```
    CREATE EXTERNAL TABLE test001 (pk0 int NOT NULL , primary key(pk0));                
    ```

    **说明：** 建立DLA映射表时，指定的Primary Key必须与Tablestore表格定义Primary Key列表一致。Primary Key用于唯一确定一行的数据，一旦映射表的Primary Key列表与Tablestore表格的PK不一致，可能导致SQL查询结果出现非预期错误。

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/20335/156646314712015_zh-CN.png)

    例如，您的Tablestore实例sh\_tpch中包含test001表格，其中只有一列pk0。使用show命令可查看该表已创建成功。

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/20335/156646314712016_zh-CN.png)

3.  使用select语句执行SQL查询。

    -   查出所有数据：

        ```
        select * from test001;                                
        ```

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/20335/156646314812017_zh-CN.png)

    -   执行count统计：

        ```
        select count(*) from test001;                               
        ```

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/20335/156646314812018_zh-CN.png)

    -   执行sum统计：

        ```
        select sum(pk0) from test001;                         
        ```

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/20335/156646314812019_zh-CN.png)


执行SQL查询时，可以选择同步执行结果，返回满足条件的前 10000 条记录；如要获取大结果集数据，请选择异步执行，并使用show query\_id的方式异步获取结果：

```
show query_task where id = '59a05af7_1531893489231';           
```

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/20335/156646314912020_zh-CN.png)

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/20335/156646314912021_zh-CN.png)

其他执行语句，请查看如下说明文档：

-   [CREATE SCHEMA](https://help.aliyun.com/document_detail/72005.html)
-   [t1916505.md\#]()
-   [SELECT](https://help.aliyun.com/document_detail/71044.html)
-   [SHOW]()
-   [DROP TABLE](https://help.aliyun.com/document_detail/72008.htm)
-   [DROP SCHEMA](https://help.aliyun.com/document_detail/72007.html)

## MySQL Client访问DLA

您可以使用标准的MySQL Client快速接入DLA的数据实例，其连接语句为：

```
mysql -h service.cn-shanghai.datalakeanalytics.aliyuncs.com -P 10000 -u <username> -p <password> -c -A         
```

**说明：** 其他操作语句与控制台访问一致。

## JDBC访问DLA

您还可以使用标准的Java API访问DLA，其连接语句为：

```
jdbc:mysql://service.cn-shanghai.datalakeanalytics.aliyuncs.com:10000/         
```

**说明：** 其他操作语句与控制台访问一致。

