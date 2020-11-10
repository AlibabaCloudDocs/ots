# Preface

This topic describes how to use Tablestore SDK for Python. The content in this topic applies to Tablestore SDK for Python V4.x.x.

## Prerequisites

-   Tablestore is activated. For more information, see [Activate Tablestore](/intl.en-US/Quick Start/Activate Tablestore.md).
-   An AccessKey pair is created. For more information, see [Create an AccessKey pair]().

## Download Tablestore SDK for Python

-   Download Tablestore SDK for Python by using the SDK package. For more information, see [SDK package](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/python/aliyun-tablestore-python-sdk-5.1.0.tar.gz).
-   Download Tablestore SDK for Python from GitHub. For more information, visit [GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk).

For the versions of Tablestore SDK for Python, see [SDK for Python](/intl.en-US/SDK Reference/Download SDK/SDK for Python.md).

## Compatibility

-   Tablestore SDK for Python V5.x.x: compatible.
-   Tablestore SDK for Python V4.x.x: compatible.
-   Tablestore SDK for Python V2.x.x: incompatible. Tablestore SDK for Python V2.x.x supports primary key columns in random order, which are no longer supported in Tablestore SDK for Python V4.0.0 and later. Tablestore SDK for Python V4.x.x and Tablestore SDK for Python V2.x.x are incompatible in the following respects:
    -   The package name is changed from ots2 to tablestore.
    -   The TableOptions parameter is added for the Client.create\_table operation.
    -   The type of the primary\_key parameter is changed from dict to list for the put\_row, get\_row, and update\_row operations to ensure the sequence of primary keys.
    -   The type of the attribute\_columns parameter is changed from dict to list for the put\_row and update\_row operations.
    -   The timestamp parameter is added to the attribute\_columns parameter for the put\_row and update\_row operations.
    -   The max\_version and time\_range parameters are added for the get\_row and get\_range operations. You must specify at least one of max\_version and time\_range.
    -   The return\_type parameter is added for the put\_row, update\_row, and delete\_row operations. Only RT\_PK is supported to return the primary key value of the current row.
    -   The return\_row parameter is added for the put\_row, update\_row, and delete\_row operations. If you set return\_type to RT\_PK in the request, the primary key value of the row is returned in return\_row.

## Version

Latest version: 5.1.0.

