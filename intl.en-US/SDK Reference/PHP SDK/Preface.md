# Preface

This topic describes how to use Tablestore SDK for PHP. This topic applies to Tablestore SDK for PHP 4.0.0 and later. This topic assumes that you have activated Tablestore and created an AccessKey ID and an AccessKey secret.

-   If you have not activated Tablestore or want to learn more about Tablestore, see the [Tablestore product page](https://www.alibabacloud.com/product/table-store).

-   If you do not have an AccessKey ID and an AccessKey secret, create an AccessKey pair in the [Alibaba Cloud Management console](https://ak-console.aliyun.com/#/accesskey).


## Precautions

Tablestore SDK for PHP 4.0.0 and later support max versions and time to live \(TTL\). However, these SDK versions are not compatible with Tablestore SDK for PHP 2.x.x. For more information, see the following topics:

-   [Max versions and TTL](/intl.en-US/Function Introduction/Wide Column model/Max versions and TTL.md)

-   [Max versions and TTL](/intl.en-US/Function Introduction/Wide Column model/Max versions and TTL.md)

-   [Configure an auto-increment primary key column](/intl.en-US/SDK Reference/PHP SDK/Table operations/Configure an auto-increment primary key column.md)


## Download Tablestore SDK for PHP

-   [SDK source code package](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/php/aliyun-tablestore-php-sdk-5.0.0.tar.gz)
-   [GitHub](https://github.com/aliyun/aliyun-tablestore-php-sdk)

For the versions of Tablestore SDK for PHP, see [SDK for PHP](/intl.en-US/SDK Reference/Download SDK/SDK for PHP.md).

## Versions

Latest version: 5.0.0.

Compatibility

-   Tablestore SDK for PHP 3.x.x: compatible.
-   Tablestore SDK for PHP 2.x.x: incompatible.

Change records

-   5.0.0
    -   New features: Local transactions are supported.
    -   New features: Search indexes are supported.
    -   New features: Global secondary indexes are supported.
-   4.1.0

    Basic Stream-based operations are supported.

-   4.0.0
    -   PHP 5.5 and later are supported, including 5.5, 5.6, 7.0, 7.1, and 7.2. Only 64-bit PHP systems are supported. We recommend that you use PHP 7.
    -   New features: TTL settings are supported. The table\_options parameter is added to CreateTable and UpdateTable.
    -   New features: Max versions is supported. Timestamp settings are supported for PutRow, UpdateRow, DeleteRow, and BatchGetRow. The max\_versions parameter is added to GetRow, GetRange, and BatchGet.
    -   New features: The auto increment feature is supported for primary key columns. The return\_type parameter is added to API operations. The primary\_key response parameter is added for the corresponding operation to return the primary key.
    -   Changes: The underlying Protobuf is upgraded to the Google protobuf-php library.
    -   Changes: The value of the primary\_key parameter for each API operation is changed to the LIST type to ensure the order of primary key columns.
    -   Changes: The value of the attribute\_columns parameter for each API operation is changed to the LIST type to support max versions.

