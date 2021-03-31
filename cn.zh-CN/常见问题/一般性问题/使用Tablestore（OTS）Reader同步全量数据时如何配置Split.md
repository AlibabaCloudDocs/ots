# 使用Tablestore（OTS）Reader同步全量数据时如何配置Split

当使用Tablestore（OTS）Reader同步全量数据过程中出现同步速度较慢问题时的现象、原因和解决方案。

## 现象

使用Tablestore（OTS）Reader同步全量数据过程中出现同步速度较慢问题。同步脚本的配置如下：

```
"reader": {
  "plugin": "ots",
  "parameter": {
    "datasource": "",
    "table": "",
    "column": [],
    "range": {
      "begin": [
        {
          "type": "INF_MIN"
        }
      ],
      "end": [
        {
          "type": "INF_MAX"
        }
      ]
    }
  }
}
```

## 原因

全量数据非常大且未在同步脚本中配置Split，此时同步任务为单并发拉取数据，会影响数据的同步速度。

## 解决方案

当全量数据非常大时，请在同步脚本中配置Split。具体操作如下：

1.  通过如下任意方式获取Split位点，请根据实际选择。

    -   使用SDK调用[ComputeSplitPointsBySize](/cn.zh-CN/API 参考/API 概览/ComputeSplitPointsBySize.md)接口。具体操作，请参见[指定大小计算分片](/cn.zh-CN/SDK 参考/Java SDK/表/指定大小计算分片.md)。
    -   下载Tablestore CLI工具后，使用points -s splitSize -t tablename命令获取。具体操作，请参见[表格存储命令行工具Tablestore CLI](/cn.zh-CN/开发工具/表格存储命令行工具Tablestore CLI.md)。
    **说明：** splitSize以100 MB为单位。当数据量较少时，无需配置Split位点；当数据量较多时，建议根据同步环境所能支持的最大并发度合理配置splitSize。

2.  在同步脚本中配置Split位点。同步脚本示例如下：

    ```
    "range": {
          "begin": [
            {
              "type": "INF_MIN"
            }
          ],
          "end": [
            {
              "type": "INF_MAX"
            }
          ],
          "split": [
            {
              "type": "STRING",
              "value": "splitPoint1"
            },
            {
              "type": "STRING",
              "value": "splitPoint2"
            },
            {
              "type": "STRING",
              "value": "splitPoint3"
            }
          ]
    }
    ```


配置Split位点后，如果同步速度仍未提升，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)或者加入钉钉群23307953（表格存储技术交流群-2）联系表格存储技术支持进行处理。

