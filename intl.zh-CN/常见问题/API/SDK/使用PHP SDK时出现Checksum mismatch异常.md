# 使用PHP SDK时出现Checksum mismatch异常

介绍使用PHP SDK时出现Checksum mismatch异常的现象、原因和解决方案。

## 现象

在Windows系统中通过PHP 5.6版本使用表格存储PHP SDK时出现如下异常：

```
Fatal error: Uncaught exception 'AliyunOTSOTSClientException' with message 'Checksum mismatch. expected:120,actual:-48'
```

## 原因

表格存储的整型是64位的，而32位PHP只能用string表示64位的整型，所以暂不支持32位PHP；且Windows系统中PHP 7之前版本的整型不是真正的64位。

## 解决方案

在Windows系统中使用表格存储PHP SDK时，PHP版本必须使用PHP 7及PHP 7以上的64位版本，强烈建议使用PHP 7以获得最佳性能。

通过phpinfo\(\)查看PHP配置信息中的Architecture类型，可以判断PHP版本是否满足使用要求。

-   当Architecture为X86时，表示PHP版本是32位，需要升级PHP版本到PHP 7及PHP 7以上的64位版本。
-   当Architecture为X64时，表示PHP版本是64位，满足使用要求。

