# ValueTransferRule

使用正则表达式匹配到字符串后，将字符串转换为String、Integer或者Double类型。

## 数据结构

```
message ValueTransferRule {
    required string regex = 1;
    optional VariantType cast_type = 2;
}
```

|名称|类型|是否必选|描述|
|--|--|----|--|
|regex|string|是|正则表达式，用于匹配字符串。正则表达式必须满足以下条件：-   长度不能超过256个字节。
-   支持perl regular语法。
-   支持单字节正则表达式。
-   不支持中文的正则匹配。
-   支持正则表达式的全匹配模式和部分匹配模式。

部分匹配的正则表达式在模式中由一对括号（…）分隔。

如果正则表达式为全匹配模式，则返回第一个匹配结果；如果正则表达式中包含部分匹配语法，则返回第一个满足的子匹配结果。例如列值为1aaa51bbb5，如果正则表达式为1\[a-z\]+5，则返回值为1aaa5；如果正则表达式为1\(\[a-z\]+\)5，则返回值为aaa。 |
|cast\_type|[VariantType](/intl.zh-CN/API 参考/DataType/VariantType.md)|是|将字符串转换为String、Integer或者Double类型，用于后续的关系运算。|

