---
keyword: [HBase全量同步到表格存储, HBase全量导入到表格存储]
---

# 将HBase数据同步到表格存储

使用DataX将HBase数据库中的全量数据同步到表格存储（Tablestore）中。

## 准备工作

-   开通表格存储服务，并创建实例和数据表，详情请参见[快速入门](/cn.zh-CN/快速入门/概述.md)。

    **说明：** 创建数据表时建议使用HBase原主键或唯一索引作为表格存储数据表的主键。

-   获取AccessKey，AccessKey包括AccessKey ID和AccessKey Secret），用来进行签名认证，详情请参见[创建RAM用户](/cn.zh-CN/用户管理/创建RAM用户.md)。

## 步骤一：下载DataX

[DataX](https://github.com/alibaba/DataX/blob/master/introduction.md)​是一个异构数据源离线同步工具，本示例中使用DataX将HBase中的数据同步到表格存储。

您可以选择下载DataX的源代码（开源）进行本地编译或者直接下载编译好的压缩包。

-   下载DataX的源代码
    -   GitHub下载

        安装Git工具，然后执行以下操作：

        ```
        git clone https://github.com/alibaba/DataX.git
        ```

    -   [本地下载](http://datax-opensource.oss-cn-hangzhou.aliyuncs.com/datax.tar.gz)
-   下载编译好的压缩包
    -   DataX的GitHub地址中获取下载链接：

        ```
        https://github.com/alibaba/DataX
        ```

    -   [本地下载](http://datax-opensource.oss-cn-hangzhou.aliyuncs.com/datax.tar.gz?spm=a2c4e.11153940.0.0.318c29fbVnjt3M&file=datax.tar.gz)

## 步骤二：Maven打包

如果您下载的是已编译好的DataX压缩包，请跳过此步骤。

**说明：** 此步骤会在本地编译各种数据源的Writer和Reader，会花费较长的时间，请耐心等待。

1.  进入到下载的源代码目录，并执行如下命令编译源代码。

    ```
    mvn -U clean package assembly:assembly -Dmaven.test.skip=true
    ```

2.  编译完成后，进入/target/datax/datax目录，其中的目录说明请参见下表。

    |目录|说明|
    |--|--|
    |bin|存放着可执行的datax.py文件，是整个DataX工具的入口。|
    |plugin|存放支持各种类型数据源的Reader和Writer。|
    |conf|存放core.json文件，文件中定义了一些缺省参数值如channle流控、buffer大小等参数，一般无需修改。|


## 步骤三：准备全量导出的JSON文件

DataX提供了HbaseReader插件从HBase中读取数据。在底层实现上，HbaseReader通过HBase的Java客户端连接远程HBase服务，并通过Scan方式读取指定rowkey范围内的数据，然后将读取的数据使用DataX自定义的数据类型拼装为抽象的数据集，并传递给下游Writer处理。

您可以根据HBase版本选择相应的Reader插件导出JSON文件。

**说明：** DataX仅提供HBase0.94、HBase1.1以及HBase2.0的导出插件，如需导出其他版本的HBase数据，请参考HBase API实现导出工具。

-   [Hbase0.94 XReader](https://github.com/alibaba/DataX/blob/master/hbase094xreader/doc/hbase094xreader.md)
-   [Hbase1.1 XReader](https://github.com/alibaba/DataX/blob/master/hbase11xreader/doc/hbase11xreader.md)
-   [Hbase2.0 XReader](https://github.com/alibaba/DataX/blob/master/hbase20xsqlreader/doc/hbase20xsqlreader.md)Hbase2.0XReader插件从Phoenix读取数据。

配置样例

使用Hbase1.1 XReader配置一个从HBase抽取数据到本地的作业（normal 模式）：

```
{
    "job": {
        "setting": {
            "speed": {
                "channel": 1
            }
        },
        "content": [
            {
                "reader": {
                    "name": "hbase11xreader",
                    "parameter": {
                        "hbaseConfig": {
                            "hbase.zookeeper.quorum": "xxxf"
                        },
                        "table": "users",
                        "encoding": "utf-8",
                        "mode": "normal",
                        "column": [
                            {
                                "name": "rowkey",
                                "type": "string"
                            },
                            {
                                "name": "info: age",
                                "type": "string"
                            },
                            {
                                "name": "info: birthday",
                                "type": "date",
                                "format":"yyyy-MM-dd"
                            },
                            {
                                "name": "info: company",
                                "type": "string"
                            },
                            {
                                "name": "address: contry",
                                "type": "string"
                            },
                            {
                                "name": "address: province",
                                "type": "string"
                            },
                            {
                                "name": "address: city",
                                "type": "string"
                            }
                        ],
                        "range": {
                            "startRowkey": "",
                            "endRowkey": "",
                            "isBinaryRowkey": true
                        }
                    }
                },
                "writer": {
                    "name": "txtfilewriter",
                    "parameter": {
                        "path": "/Users/shf/workplace/datax_test/hbase11xreader/result",
                        "fileName": "qiran",
                        "writeMode": "truncate"
                    }
                }
            }
        ]
    }
}
```

## 步骤四：执行同步命令

执行如下命令同步数据。

```
python datax.py  -j"-Xms4g -Xmx4g" hbase_to_ots.json
```

其中`-j"-Xms4g -Xmx4g"`可以限制占用JVM内存的大小；如果不指定，将会使用`conf/core.json`中的配置，默认为1 GB。

## 相关操作

HBase数据迁移到表格存储后，您可以使用Tablestore SDK或者Tablestore HBase Client读取表格存储的数据，详情请参见[从HBase Client迁移到Tablestore HBase Client](/cn.zh-CN/功能介绍/HBase 支持/从HBase Client迁移到Tablestore HBase Client.md)。

