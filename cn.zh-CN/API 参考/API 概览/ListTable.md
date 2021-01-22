# ListTable

调用ListTable接口获取当前实例下已创建的所有表的表名。

## 注意事项

如果一个表刚刚创建成功，该表名会出现在ListTableResponse中，但此时该表不一定能够进行读写。

## 请求结构

```
message ListTableRequest {
}            
```

## 响应消息结构

```
message ListTableResponse {
    repeated string table_names = 1;
}            
```

|名称|类型|说明|
|--|--|--|
|table\_names|repeated String|当前实例下所有表的表名。|

## 使用SDK

您可以使用如下语言的SDK列出表名称。

-   [Java SDK：列出表名称](/cn.zh-CN/SDK 参考/Java SDK/表/列出表名称.md)
-   [Go SDK：列出表名称](/cn.zh-CN/SDK 参考/Go SDK/表/列出表名称.md)
-   [Python SDK：列出表名称](/cn.zh-CN/SDK 参考/Python SDK/表/列出表名称.md)
-   [Node.js SDK：列出表名称](/cn.zh-CN/SDK 参考/Node.js SDK/表/列出表名称.md)
-   [.NET SDK：列出表名称](/cn.zh-CN/SDK 参考/.NET SDK/表/列出表名称.md)
-   [PHP SDK：列出表名称](/cn.zh-CN/SDK 参考/PHP SDK/表操作/列出表名称.md)

