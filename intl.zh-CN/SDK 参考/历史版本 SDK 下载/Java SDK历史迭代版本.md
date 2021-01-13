# Java SDK历史迭代版本

本文介绍表格存储Java SDK的历史迭代版本。

**说明：** 4.0.0以上版本的SDK支持数据多版本和生命周期，且不兼容2.x.x系列的SDK。

## 版本号：5.10.3

发布时间：2020-10-26

下载地址：[5.10.3](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/java/tablestore-5.10.3-release.zip)

更新日志：

-   新增数据湖投递功能。通过控制台或SDK的具体操作，请分别参见[快速入门](/intl.zh-CN/功能介绍/数据湖投递/快速入门.md)或者[使用SDK](/intl.zh-CN/功能介绍/数据湖投递/使用SDK.md)。
-   多元索引新增获取统计聚合分组内的行功能。具体操作，请参见[统计聚合](/intl.zh-CN/功能介绍/多元索引/使用多元索引/统计聚合.md)。
-   新增对配置参数的格式检查。
-   SDK内部优化与计算引擎的对接。

## 版本号：5.7.0

发布时间：2020-04-07

下载地址：[5.7.0](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/java/tablestore-5.7.0-release.zip)

更新日志：

-   多元索引新增并发导出数据功能。具体操作，请参见[并发导出数据](/intl.zh-CN/功能介绍/多元索引/使用多元索引/并发导出数据.md)。
-   优化TableStoreWriter。

## 版本号：5.4.0

发布时间：2019-11-18

下载地址：[5.4.0](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/java/tablestore-5.4.0-release.zip)

更新日志：

-   支持已有的表新增或删除预定义列。
-   通道功能进行了SDK内部优化。

## 版本号：5.3.0

发布时间：2019-09-10

下载地址：[5.3.0](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/java/tablestore-5.3.0-release.zip?spm=a2c4g.11186623.2.28.192d2d66COWK0Y&file=tablestore-5.3.0-release.zip)

更新日志：多元索引新增统计聚合功能。

## 版本号：5.1.0

发布时间：2019-07-01

下载地址：[5.1.0](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/java/tablestore-5.1.0-release.zip)

更新日志：多元索引新增更多分词类型。

## 版本号：5.0.0

发布时间：2019-06-10

下载地址：[5.0.0](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/java/tablestore-5.0.0-release.zip)

更新日志：

-   为了各SDK版本号的统一，版本号提升到5.x，与4.x版本SDK接口兼容。
-   多元索引新增exists query。

## 版本号：4.12.1

发布时间：2019-05-08

下载地址：[4.12.1](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/java/tablestore-4.12.1-release.zip)

更新日志：新增TimelineV2模型。

## 版本号：4.10.2

发布时间：2019-03-11

下载地址：[tablestore-4.10.2](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/108096/cn_zh/1552274752739/tablestore-4.10.2-release.zip)

更新日志：

-   Remove log4j2 implement and log4j2.xml.
-   TunnelWorker: fix auto retry logic when network condition is too bad.

## 版本号：4.7.4

发布时间：2018-09-27

下载地址：[tablestore-4.7.4-release.zip](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/86690/cn_zh/1540433142084/tablestore-4.7.4-release.zip)

更新日志：

-   新增多元索引功能。
    -   多字段检索
    -   范围查询
    -   通配符查询
    -   嵌套查询
    -   全文检索
    -   排序
-   新增全局二级索引。

## 版本号：4.0.0

发布时间：2016-08-01

下载地址：[aliyun\_tablestore\_java\_sdk\_4.0.0.zip](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/43005/cn_zh/1470024227102/aliyun_tablestore_java_sdk_4.0.0.zip)

更新日志：

-   新增[数据生命周期TTL](/intl.zh-CN/功能介绍/Wide column模型/数据版本和生命周期.mdsection_cv2_kyf_cfb)。
-   新增[数据多版本](/intl.zh-CN/功能介绍/Wide column模型/数据版本和生命周期.md)。

## 版本号：2.2.4

发布时间：2016-05-12

下载地址：[aliyun\_tablestore\_java\_sdk\_2.2.4.zip](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/31713/cn_zh/1463131862644/aliyun-ots-java-sdk-2.2.4.zip)

更新日志：

-   新增condition update。
-   新增filter。

## 版本号：2.1.0

发布时间：2015-11-12

下载地址：[aliyun-ots-java-sdk-2.1.0.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-ots-java-sdk-2.1.zip)

更新日志：

-   网络传输异步化及一系列性能调优：同等CPU使用情况下QPS提升数倍。
-   提供灵活易用的异步接口：支持传入callback，同时返回future。
-   解除与OSS SDK的绑定：新的SDK只包含表格存储SDK相关代码，目录结构有细微调整。
-   重试逻辑优化：优化默认的重试逻辑；支持batch操作中单行错误单独重试；提供更清晰的重试逻辑自定义方式。
-   日志优化：支持请求发送到接收的各个环节的日志记录；支持记录慢请求日志；通过TraceId打通SDK与后端服务的全链条日志。
-   提供支持批量数据导入的OTSWriter接口：旨在提供易用、高效的数据导入体验。
-   其他优化：丰富各种数据类型的工具函数；提供计算数据大小的接口等。

**重要提示**：

2.1.0与2.0.4版本有如下细微不兼容之处：

-   替换新的SDK后需要更改少数几个类的import路径。因为有几个类的package有调整，例如ClientConfiguration的package由com.aliyun.openservices调整为com.aliyun.openservices.ots。调整package的主要原因是表格存储的SDK已经与OSS SDK分离，之前公用的几个类放在ots的package下更为合理。
-   当您不再使用OTSClient实例时（例如程序结束前），需要主动调用OTSClient的shutdown方法，释放OTSClient对象占有的线程和连接资源。
-   ClientConfiguration中部分配置项的名称有调整，例如在配置项名称中加入了时间单位。
-   新SDK的包依赖有变化，例如使用了HttpAsyncClient和Jodatime等，如果您在运行中有遇到问题，需要考虑是否引入了冲突的依赖。

## 版本号：2.0.4

发布时间：2015-09-25

下载地址：[aliyun-ots-java-sdk-2.0.4.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-openservices-OTS-2.0.4.zip)

