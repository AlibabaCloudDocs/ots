# 使用DLA服务 {#concept_87551_zh .concept}

开通服务后，可通过控制台、MySQL Client 以及 JDBC 这 3 种方式接入 DLA 服务并进行 SQL 查询。

## Table Store 和 DLA 元信息映射逻辑 {#section_f45_tpj_dfb .section}

-   库和表等概念映射

    |Table Store|DLA|
    |:----------|:--|
    |实例（instance）|schema 或 database|
    |表（table）|table|
    |主键列（pk）|column，isPrimaryKey=true，isNullable=false|
    |非主键列（column）|column，isPrimaryKey=false，isNullable=<用户的DDL定义\>|

-   字段的映射关系

    |Table Store|DLA|
    |:----------|:--|
    |INTEGER\(8bytes\)|bigint\(8bytes\)|
    |STRING|varchar|
    |BINARY|varbinary\(目前主键中不支持\)|
    |DOUBLE|double|
    |BOOLEAN|byte|


## 控制台访问 DLA { .section}

控制台访问 DLA 步骤如下：

1.  使用邮件中随附的该 region 的用户名和密码登录数据库。
2.  为 Table Store 中的实例表格数据建立映射。

假设您在上海 region 已创建一个名为 sh\_tpch 的实例，该实例包含表格 test001，表格内包含 2 行测试数据。该实例建立映射的步骤如下：

1.  将 Table Store 的实例映射成 DLA 的一个 DataBase 实例。建立 DLA 的 Database 映射前，首先需要在 Table Store 中创建实例，如创建一个名为 sh-tpch 的实例，对应的 endpoint 为 sh-tpch.cn-shanghai.ots.aliyuncs.com。

    完成测试实例创建后，执行下列语句建立 Database 映射：

    ```
    CREATE SCHEMA sh_tpch001 with DBPROPERTIES(LOCATION ='https://sh-tpch.cn-shanghai.ots.aliyuncs.com', catalog='ots', instance ='sh-tpch');
    
    ```

    **说明：** 使用 MySQL Client 时，可以使用 create database 或 create schema 语句创建 database 映射。但是控制台目前只支持 create schema 语句创建 database 映射。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20335/153803567612014_zh-CN.png)

2.  在 tp\_tpch001 的 Database 下建立表格的映射。在建立 DLA 的表格映射前，首先需要在 Table Store 中创建数据表。

    数据表创建完成后，执行下列语句建立表格映射：

    ```
    CREATE EXTERNAL TABLE test001 (pk0 int , primary key(pk0));
    
    ```

    **说明：** 建立 DLA 映射表时，指定的 Primary Key 必须与 Table Store 表格定义 Primary Key 列表一致。Primary Key 用于唯一确定一行的数据，一旦映射表的 Primary Key 列表与 Table Store 表格的 PK 不一致，可能导致 SQL 查询结果出现非预期错误。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20335/153803567712015_zh-CN.png)

    例如，您的 Table Store 实例 sh\_tpch 中包含 test001 表格，其中只有一列 pk0。使用 show 命令可查看该表已创建成功：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20335/153803567712016_zh-CN.png)

3.  使用select语句执行SQL查询：
    -   查出所有数据：

        ```
        select * from test001;
        
        ```

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20335/153803567712017_zh-CN.png)

    -   执行count统计：

        ```
        select count(*) from test001;
        
        ```

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20335/153803567712018_zh-CN.png)

    -   执行sum统计：

        ```
        select sum(pk0) from test001;
        
        ```

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20335/153803567712019_zh-CN.png)


执行SQL查询时，可以选择同步执行结果，返回满足条件的前 10,000 条记录；如要获取大结果集数据，请选择异步执行，并使用show query\_id的方式异步获取结果：

```
show query_task where id = '59a05af7_1531893489231';

```

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20335/153803567712020_zh-CN.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20335/153803567712021_zh-CN.png)

其他执行语句，请查看如下说明文档：

-    [create schema语句](https://help.aliyun.com/document_detail/72005.html) 
-    [create table语句](https://help.aliyun.com/document_detail/72006.html) 
-    [select语句](https://help.aliyun.com/document_detail/71044.html) 
-    [show语句](https://help.aliyun.com/document_detail/72011.html) 
-    [drop table语句](https://help.aliyun.com/document_detail/72008.htm) 
-    [drop schema语句](https://help.aliyun.com/document_detail/72007.html) 

## MySQL Client 访问 DLA { .section}

您可以使用标准的 MySQL Client 快速接入 DLA 的数据实例，其连接语句为：

```
mysql -h service.cn-shanghai.datalakeanalytics.aliyuncs.com -P 10000 -u <username> -p <password> -c -A

```

**说明：** 其他操作语句与控制台访问一致。

## JDBC 访问 DLA { .section}

您还可以使用标准的 Java API 访问 DLA，其连接语句为：

```
jdbc:mysql://service.cn-shanghai.datalakeanalytics.aliyuncs.com:10000/

```

**说明：** 其他操作语句与控制台访问一致。

