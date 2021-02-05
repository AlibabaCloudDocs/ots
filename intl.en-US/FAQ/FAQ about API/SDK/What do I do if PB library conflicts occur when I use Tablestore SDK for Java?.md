# What do I do if PB library conflicts occur when I use Tablestore SDK for Java?

This topic describes the problem description, cause, and solution of PB library conflicts that occur when you use Tablestore SDK for Java.

## Problem description

A similar output is displayed when I use Tablestore SDK for Java:

```
Caused by: java.lang.UnsupportedOperationException: This is supposed to be overridden by subclassed
```

## Cause

Tablestore SDK for Java V2.4.1 depends on Protobuf library 2.4.1 and httpasyncclient 4.0.2, which may conflict with the same built-in library of your application.

## Solution

Add the following dependencies to the pom.xml file in the Maven project:

**Note:** classifier is jar-with-dependencies, which packages the HttpClient and Protobuf dependencies by using rename package to remove the dependency on HttpClient and Protobuf.

```
<dependency>
        <groupId>com.aliyun.openservices</groupId>
        <artifactId>tablestore</artifactId>
        <version>Your current version</version>
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

