# SingleColumnValueFilter

单个条件，例如column\_a\>5等，适用于ConditionUpdate（条件更新）和Filter（过滤器）功能。

## 数据结构

```
message SingleColumnValueFilter {
    required ComparatorType comparator = 1;
    required string column_name = 2;
    required bytes column_value = 3;
    required bool filter_if_missing = 4;
    required bool latest_version_only = 5; 
    optional ValueTransferRule value_transfer_rule =6;
     
}                
```

|名称|类型|是否必选|描述|
|--|--|----|--|
|comparator|[ComparatorType](/intl.zh-CN/API 参考/DataType/ComparatorType.md)|是|关系运算符。|
|column\_name|string|是|列名称。|
|column\_value|bytes|是|列值经过[Plainbuffer](/intl.zh-CN/API 参考/DataType/PlainBuffer.md)编码后的值。|
|filter\_if\_missing|bool|是|当某行的该列不存在时，设置条件是否过滤。取值范围如下：-   true（默认）：该行的条件判断通过。
-   false：该行的条件判断不通过。

例如过滤条件为column\_a\>0，如果设置filter\_if\_missing为true，则当某一行没有column\_a列时，该行的条件判断会通过。 |
|latest\_version\_only|bool|是|是否只对最新版本有效。取值范围如下：-   true（默认）：只检测最新版本的值是否满足条件。
-   false：检测所有版本的值是否满足条件。 |
|value\_transfer\_rule|[ValueTransferRule]()|否|使用正则表达式匹配到字符串后，将字符串转换为String、Integer或者Double类型。当某些列中存储了自定义格式数据（例如JSON格式字符串）时，如果用户希望通过某个子字段值来过滤查询该列数据，则需要设置此参数。 |

## 相关操作

-   条件更新

    [PutRow](/intl.zh-CN/API 参考/API 概览/PutRow.md)

    [UpdateRow](/intl.zh-CN/API 参考/API 概览/UpdateRow.md)

    [DeleteRow](/intl.zh-CN/API 参考/API 概览/DeleteRow.md)

    [BatchWriteRow](/intl.zh-CN/API 参考/API 概览/BatchWriteRow.md)

-   过滤器

    [GetRow](/intl.zh-CN/API 参考/API 概览/GetRow.md)

    [GetRange](/intl.zh-CN/API 参考/API 概览/GetRange.md)

    [BatchGetRow](/intl.zh-CN/API 参考/API 概览/BatchGetRow.md)


