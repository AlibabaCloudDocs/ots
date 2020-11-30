# 使用SDK时出现Validate PK name fail异常

介绍使用SDK时出现Validate PK name fail异常的现象、原因和解决方案。

## 现象

使用SDK查询数据时出现如下异常：

```
Caused by: [ErrorCode]:OTSInvalidPK, [Message]:Validate PK name fail
```

## 原因

设置的主键名称和数据表的主键名称不一致，或者设置的主键顺序和数据表的主键顺序不一致。

## 解决方案

设置的主键名称与主键顺序必须和数据表的主键名称与主键顺序一致。

