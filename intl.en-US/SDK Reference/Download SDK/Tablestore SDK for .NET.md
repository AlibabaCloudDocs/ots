# Tablestore SDK for .NET

This topic describes the version history of Tablestore SDK for .NET.

## Version: 4.1.4

Release date: June 24, 2019

Download URL: [https://www.nuget.org/packages/Aliyun.TableStore.SDK/4.1.4](https://www.nuget.org/packages/Aliyun.TableStore.SDK/4.1.4)

Change records: The problem that single-word tokenization is performed on the keyword by default in search index is fixed.

## Version: 4.1.2

Release date: May 29, 2019

Download URL: [https://www.nuget.org/packages/Aliyun.TableStore.SDK/4.1.2](https://www.nuget.org/packages/Aliyun.TableStore.SDK/4.1.2)

Change records: The problem that tokenization can be performed on the keyword when the column used for the match query is not a TEXT column is fixed.

## Version: 4.1.1

Release date: March 6, 2019

Download URL: [https://www.nuget.org/packages/Aliyun.TableStore.SDK/4.1.1](https://www.nuget.org/packages/Aliyun.TableStore.SDK/4.1.1)

Change records: The problem that garbled characters are displayed in some scenarios in which search index is used is fixed.

## Version: 4.1.0

Release date: December 17, 2018

Download URL: [https://www.nuget.org/packages/Aliyun.TableStore.SDK/4.1.0](https://www.nuget.org/packages/Aliyun.TableStore.SDK/4.1.0)

Change records:

-   Atomic counter is supported. For more information, see [Configure atomic counter](/intl.en-US/SDK Reference/.NET SDK/Table/Configure atomic counter.md).
-   Global secondary index is supported. For more information, see [Global secondary index](/intl.en-US/SDK Reference/.NET SDK/Global secondary index.md).
-   Search index is supported. For more information, see [Create search indexes](/intl.en-US/SDK Reference/.NET SDK/Search Index/Create search indexes.md).

## Version: 4.0.0

Release date: September 18, 2018

Download URL: [https://www.nuget.org/packages/Aliyun.TableStore.SDK/4.0.0](https://www.nuget.org/packages/Aliyun.TableStore.SDK/4.0.0)

Change records:

-   Max versions and TTL are supported. For more information, see [Max versions and TTL](/intl.en-US/Function Introduction/Wide Column model/Max versions and TTL.md).
-   Auto-increment primary key columns are supported. For more information, see [Configure an auto-increment primary key column](/intl.en-US/SDK Reference/.NET SDK/Table/Configure an auto-increment primary key column.md).

## Version: 3.0.0

Release date: May 5, 2016

Download URL: [aliyun\_table\_store\_dotnet\_sdk\_3.0.0.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-dotnet-sdk-3.0.0.zip)

Change records:

-   The filter feature is supported.
-   The warnings generated during the compilation process are cleared.
-   Unnecessary dependent packages and code are deleted.
-   The code used to call templates is simplified.
-   The check for invalid parameters is added.
-   Spaces are removed from both ends of user-defined parameters by using the trim\(\) function.
-   The UserAgent header is added.
-   The Condition.IGNORE, Condition, EXPECT\_EXIST, and Condition.EXPECT\_NOT\_EXIST parameters are deleted.
-   The DLL file name is changed from Aliyun.dll to Aliyun.TableStore.dll.
-   The open source code is released to GitHub.

## Version: 2.2.1

Release date: April 14, 2016

Download URL: [aliyun-ots-dotnet-sdk-2.2.1.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-ots-dotnet-sdk-2.2.1.zip)

Change records: Response headers are case-insensitive when they are verified by the SDK.

## Version: 2.2.0

Release date: April 5, 2016

Download URL: [aliyun-ots-dotnet-sdk-2.2.0.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-ots-dotnet-sdk-2.2.0.zip)

Change records:

-   The default number of connections in a connection pool is increased from 50 to 300.
-   Conditional update is supported.

## Version: 2.1.0

Release date: December 30, 2015

Download URL: [aliyun-ots-dotnet-sdk-2.1.0.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-ots-dotnet-sdk-2.1.0.zip)

Change records:

-   Reserved capacity unit \(CU\) settings in the code example are modified based on the pay-as-you-go billing method.
-   More test cases for BatchGetRow and BatchWriteRow are provided.

