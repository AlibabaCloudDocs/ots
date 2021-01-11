# 使用通配符查询时出现length of field value is longer than 10 for the \[WILDCARD\_QUERY\] query异常

本文介绍使用通配符查询时出现length of field value is longer than 10 for the \[WILDCARD\_QUERY\] query异常的现象、原因和解决方案。

## 现象

使用通配符查询时出现如下异常：

```
length of field value is longer than 10 for the [WILDCARD_QUERY] query
```

## 原因

带有通配符的字符串长度超过10字节。

## 解决方案

使用通配符查询时，要匹配的值可以是一个带有通配符的字符串，带有通配符的字符串长度不能超过10字节。

