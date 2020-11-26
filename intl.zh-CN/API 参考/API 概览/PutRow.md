# PutRow

您可以使用PutRow接口插入数据到指定的行。

**说明：**

-   如果指定行不存在，则新增一行；若指定行存在，则覆盖原有行。
-   返回结果中如没有出现报错表示本次操作成功。

## 请求消息结构

```
message PutRowRequest {
    required string table_name = 1;
    required bytes row = 2; // Plainbuffer编码为二进制
    required Condition condition = 3;
    optional ReturnContent return_content = 4; 
}           
```

|名称|类型|是否必选|描述|
|--|--|----|--|
|table\_name|string|是|请求写入数据的表名。 |
|row|bytes|是|写入的行数据， 包括主键和属性列，Plainbuffer格式，编码详见[Plainbuffer](/intl.zh-CN/API 参考/DataType/PlainBuffer.md)编码。 |
|condition|[Condition](/intl.zh-CN/API 参考/DataType/Condition.md)|是|在数据写入前是否进行行存在性检查，可以取下面三个值：

 -   IGNORE 表示不做行存在性检查。

-   EXPECT\_EXIST 表示期望行存在。

-   EXPECT\_NOT\_EXIST 表示期望行不存在。 |
|return\_content|[ReturnContent](/intl.zh-CN/API 参考/DataType/ReturnContent.md)|否|写入成功后返回的数据类型，目前仅支持返回主键，主要用于主键列自增功能中。 |

## 响应消息结构

```
message PutRowResponse {
    required ConsumedCapacity consumed = 1;
    optional bytes row = 2;
}         
```

|名称|类型|描述|
|--|--|--|
|consumed|[ConsumedCapacity](/intl.zh-CN/API 参考/DataType/ConsumedCapacity.md)|本次操作[消耗的服务能力单元](#title_xtj_bc1_h21)。 |
|row|bytes|-   当设置了return\_content后，返回的数据。

-   如果没设置return\_content或者没返回数据，此处为NULL。

-   Plainbuffer格式，编码详见[Plainbuffer](/intl.zh-CN/API 参考/DataType/PlainBuffer.md)编码。 |

## 服务能力单元消耗

-   如果该行不存在：

    -   若指定条件检查为 IGNORE，消耗写服务能力单元的数值为本行的主键数据大小与要插入属性列数据大小之和除以 4 KB 向上取整。

    -   若指定条件检查为 EXPECT\_NOT\_EXIST，除了消耗本行的主键数据大小与要插入属性列数据大小之和除以 4 KB 向上取整的写CU，还需消耗该行主键数据大小除以 4 KB 向上取整的读 CU。

    -   若指定条件检查为 EXPECT\_EXIST，本次插入失败并且消耗 1 单位写 CU 和 1 单位读 CU。

-   如果该行存在：

    -   若指定条件检查为 IGNORE，消耗写服务能力单元的数值为本行的主键数据大小与要插入属性列数据大小之和除以 4 KB 向上取整。

    -   若指定条件检查为 EXPECT\_EXIST，除了消耗本行的主键数据大小与要插入属性列数据大小之和除以 4 KB 向上取整的写 CU，还需消耗该行主键数据大小除以 4 KB 向上取整的读 CU。

    -   若指定条件检查为 EXPECT\_NOT\_EXIST，本次插入失败并且消耗 1 单位写 CU 和 1 单位读 CU。

-   使用条件更新（Conditional Update）：

    -   操作成功，按照上述消耗服务能力单元方式进行计算。

    -   操作失败，则消耗 1 单位写 CU 和 1 单位读 CU。

    关于数据大小的计算请参见[产品定价](/intl.zh-CN/产品定价/计费概述.md)。

-   如果返回内部错误（HTTP 状态码：5XX），则此次操作不消耗服务能力单元；其他错误情况消耗 1 个写服务能力单元。

-   如果请求超时，结果未定义，服务能力单元有可能被消耗，也可能未被消耗。


