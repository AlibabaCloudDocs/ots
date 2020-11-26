# GetRow

您可以使用此接口根据指定的主键读取单行数据。

## 请求结构

```
message GetRowRequest {
    required string table_name = 1;
    required bytes primary_key = 2;   // Plainbuffer编码为二进制
    repeated string columns_to_get = 3; // 不指定则读出所有的列
    optional TimeRange time_range = 4;
    optional int32 max_versions = 5;
    optional bytes filter = 7;
    optional string start_column = 8;
    optional string end_column = 9;
    optional bytes token = 10;
}
```

|名称|类型|是否必选|描述|
|--|--|----|--|
|table\_name|string|是|要读取的数据所在的表名。|
|primary\_key|bytes|是|该行全部的主键列，包含主键名和主键值，由[Plainbuffer](/cn.zh-CN/API 参考/DataType/PlainBuffer.md)。|
|columns\_to\_get|repeated string|否|-   需要返回的全部列的列名。若为空，则返回该行的所有列。
-   如果指定的列不存在，则不会返回该列的数据。
-   如果给出了重复的列名，返回结果只会包含一次该列。
-   columns\_to\_get 中 string 的个数不应超过 128 个。 |
|time\_range|[TimeRange](/cn.zh-CN/API 参考/DataType/TimeRange.md)|和max\_versions必须至少存在一个|-   读取数据的版本时间戳范围。
-   时间戳的取值最小值为0，最大值为INT64.MAX。
-   若要查询一个范围，则指定start\_time和end\_time。
-   若要查询一个特定时间戳，则指定specific\_time。

 例子：如果指定的time\_range为（100，200），则返回的列数据的时间戳必须位于（100，200）范围内，前闭后开区间。 |
|max\_versions|int32|和time\_range只能存在一个|读取数据时，返回的最多版本个数。

 例子：如果指定max\_versions为2，则每一列最多返回2个版本的数据。 |
|filter|bytes|否|过滤条件表达式。

 [Filter](/cn.zh-CN/API 参考/DataType/Filter.md)经过protobuf序列化后的二进制数据。 |
|start\_column|string|否|-   指定读取时的起始列，主要用于宽行读。

-   返回的结果中包含当前起始列。

-   列的顺序按照列名的字典序排序。


 例子：如果一张表有a、b、c三列，读取时指定start\_column为b，则会从b列开始读，返回b、c两列。 |
|end\_column|string|否|-   指定读取时的结束列，主要用于宽行读。

-   返回的结果中不包含当前结束列。

-   列的顺序按照列名的字典序排序。


 例子：如果一张表有a、b、c三列，读取时指定end\_column为b，则读到b列时会结束，返回a列。 |

## 响应消息结构

```
message GetRowResponse {
    required ConsumedCapacity consumed = 1;
    required bytes row = 2; // Plainbuffer编码为二进制
}
```

|名称|类型|描述|
|--|--|--|
|consumed|[CapacityUnit](/cn.zh-CN/API 参考/DataType/CapacityUnit.md)|本次操作消耗的服务能力单元。 |
|row|bytes|读取到的数据，由[Plainbuffer](/cn.zh-CN/API 参考/DataType/PlainBuffer.md)编码。

 如果该行不存在，则数据为空。 |

## 服务能力单元消耗

-   如果请求的行不存在，消耗 1 读服务能力单元。

-   如果请求的行存在，消耗读服务能力单元的数值为这该行所有主键列的数据大小与实际读取的属性列数据大小之和除以 4 KB 向上取整。关于数据大小的计算请参见[产品定价](/cn.zh-CN/产品定价/计费概述.md)。

-   如果请求超时，结果未定义，服务能力单元有可能被消耗，也可能未被消耗。

-   如果返回内部错误（HTTP 状态码：5XX），则此次操作不消耗服务能力单元，其他错误情况消耗 1 读服务能力单元。


