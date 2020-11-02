# 使用SDK时出现Validate PK size fail异常

介绍使用SDK时出现Validate PK size fail异常的现象、原因和解决方案。

## 现象

使用SDK更新数据时出现如下异常：

```
Caused by: [ErrorCode]:OTSInvalidPK, [Message]:Validate PK size fail
```

## 原因

设置的主键个数和数据表的主键个数不一致。

## 解决方案

设置的主键个数必须和数据表的主键个数一致。

