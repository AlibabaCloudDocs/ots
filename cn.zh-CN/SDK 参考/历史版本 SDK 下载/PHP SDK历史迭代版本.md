# PHP SDK历史迭代版本

本文介绍表格存储PHP SDK历史迭代版本。

## 版本号5.0.0

发布时间：2019-05-30

下载地址：[aliyun\_tablestore\_php\_sdk\_5.0.0.tar.gz](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/php/aliyun-tablestore-php-sdk-5.0.0.tar.gz)

更新日志

-   新功能：支持局部事务。
-   新功能：支持多元索引。
-   新功能：支持二级索引。

## 版本号4.1.0

发布时间：2018-07-24

下载地址：[aliyun\_tablestore\_php\_sdk\_4.1.0.tar.gz](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/27353/cn_zh/1532416205696/aliyun-tablestore-php-sdk-4.1.0.tar.gz)

更新日志：

支持Stream基础接口。

## 版本号4.0.0

发布时间：2018-06-25

下载地址：[aliyun\_tablestore\_php\_sdk\_4.0.0.tar.gz](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/31755/cn_zh/1530158675136/aliyun-tablestore-php-sdk-4.0.0.tar.gz)

更新日志：

-   支持5.5以上PHP版本，包括5.5、5.6、7.0、7.1、7.2等版本，只支持64位的PHP系统，推荐使用PHP7。
-   新功能：支持TTL设置，createTable, updateTable新增table\_options参数。
-   新功能：支持多版本，putRow, updateRow, deleteRow, batchGetRow均支持timestamp设置，getRow, getRange, BatchGet等接口支持max\_versions过滤。
-   新功能：支持主键列自增功能，接口新增return\_type，返回新增primary\_key，返回对应操作的primary\_key。
-   变更：底层protobuf升级成谷歌官方版本protobuf-php库。
-   变更：各接口的primary\_key变更成list类型，保证顺序性。
-   变更：各接口的attribute\_columns变更成list类型，以支持多版本功能。

## 版本号2.1.1

发布时间：2017-01-14

下载地址：[aliyun-tablestore-php-sdk-2.1.1.zip](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/27353/cn_zh/1484275631465/aliyun-tablestore-php-sdk-2.1.1.zip)

更新日志：

支持32位操作系统。

## 版本号2.1.0

发布时间：2016-11-16

下载地址：[aliyun-ots-php-sdk-2.1.0.zip](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/27353/cn_zh/1479276463558/aliyun-tablestore-php-sdk-2.1.0.zip)

更新日志：

-   支持ConditionalUpdate和Filter功能。
-   新增了方便SDK使用的常量类。
-   兼容PHP 5.5以上版本。

## 版本号2.0.3

发布时间：2016-05-18

下载地址：[aliyun-ots-php-sdk-2.0.3.zip](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/27353/cn_zh/1463568726964/aliyun-ots-php-sdk-2.0.3.zip)

更新日志：

删除示例中循环删除表的代码。

## 版本号2.0.2

发布时间：2016-04-11

下载地址：[aliyun-ots-php-sdk-2.0.2.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-ots-php-sdk-2.0.2.zip)

更新日志：

修复了pb decode的时候，会将有符号的整数解释为无符号的整数导致无法写入负数的情况。

## 版本号2.0.1

发布时间：2015-12-30

下载地址：[aliyun-ots-php-sdk-2.0.1.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-ots-php-sdk-2.0.1.zip)

更新日志：

根据按量计费方式，重新调整了示例代码中的预留CU设置。

## 版本号2.0.0

发布时间：2015-09-22

下载地址：[aliyun-ots-php-sdk-2.0.0.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-ots-sdk-php-2.0.0.zip)

更新日志：

-   包含表格存储的所有接口。
-   兼容PHP 5.3、5.4、5.5和5.6版本。
-   包含标准的重试策略。
-   使用Guzzle Http Client作为网络库。
-   使用composer作为依赖管理和工程组织工具。
-   使用phpDocumentor 2生成HTML格式的编程文档。

