# 使用Java SDK时出现PB库冲突

本文介绍了使用Java SDK时出现PB库冲突的现象、原因和解决方案。

## 现象

使用Java SDK时出现如下异常：

```
Caused by: java.lang.UnsupportedOperationException: This is supposed to be overridden by subclassed
```

## 原因

Java SDK依赖2.4.1版本的Protobuf库和4.0.2版本的httpasyncclient，容易与您的应用程序中自带的相同库冲突。

## 解决方案

在Maven项目中的pom.xml中添加如下依赖即可。

**说明：** classifier为jar-with-dependencies，它将依赖的HttpClient和Protobuf库都通过rename package的方式打包进去，去除了对HttpClient和Protobuf的依赖。

```
<dependency>
        <groupId>com.aliyun.openservices</groupId>
        <artifactId>tablestore</artifactId>
        <version>替换为您当前使用的版本</version>
        <classifier>jar-with-dependencies</classifier>
        <exclusions>
            <exclusion>
                <groupId>com.google.protobuf</groupId>
                <artifactId>protobuf-java</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.apache.httpcomponents</groupId>
                <artifactId>httpasyncclient</artifactId>
            </exclusion>
        </exclusions>
    </dependency>

            
```

