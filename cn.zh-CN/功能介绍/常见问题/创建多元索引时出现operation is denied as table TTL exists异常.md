# 创建多元索引时出现operation is denied as table TTL exists异常

介绍创建多元索引时出现operation is denied as table TTL exists异常的现象、原因和解决方案。

## 现象

创建多元索引时出现如下异常：

```
ErrorCode: OTSParameterInvalid, ErrorMessage: operation is denied as table TTL exists。
```

## 原因

数据表的数据生命周期（TTL）未设置为-1（数据永不过期），只有当数据表的数据生命周期为-1时，才能创建多元索引。

## 解决方案

通过控制台或者SDK修改数据表的数据生命周期为-1。

-   通过控制台修改

    在数据表的基本详情页签，单击**修改表属性**，修改数据表的数据生命周期为-1，单击**确定**。

-   通过SDK修改

    使用UpdateTable接口修改数据表的数据生命周期为-1，请根据实际选择相应的SDK进行操作。

    -   [Java SDK：更新表](/cn.zh-CN/SDK 参考/Java SDK/表/更新表.md)
    -   [Go SDK：更新表](/cn.zh-CN/SDK 参考/Go SDK/表/更新表.md)
    -   [Python SDK：更新表](/cn.zh-CN/SDK 参考/Python SDK/表/更新表.md)
    -   [Node.js SDK：更新表](/cn.zh-CN/SDK 参考/Node.js SDK/表/更新表.md)
    -   [.NET SDK：更新表](/cn.zh-CN/SDK 参考/.NET SDK/表/更新表.md)
    -   [PHP SDK：更新表](/cn.zh-CN/SDK 参考/PHP SDK/表操作/更新表.md)

