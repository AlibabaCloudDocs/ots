# Preface

This topic describes how to install and use Tablestore SDK for Python.

## Prerequisites

-   Tablestore is activated. For more information, see [Activate Tablestore](/intl.en-US/Quick Start/Activate Tablestore.md).
-   An AccessKey pair is created. For more information, see [Create AccessKey pairs]().

## Download Tablestore SDK for Python

-   Download the package of Tablestore SDK for python. For more information, see [SDK package](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/python/aliyun-tablestore-python-sdk-5.1.0.tar.gz).
-   Download Tablestore SDK for Python from GitHub. For more information, visit [GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/archive/v5.2.1.tar.gz).

For more information about the versions of Tablestore SDK for Python, see [Tablestore SDK for Python](/intl.en-US/SDK Reference/Download SDK/Tablestore SDK for Python.md).

## Compatibility

-   Tablestore SDK for Python V5.x.x: compatible. Tablestore SDK for Python V5.2.1 is incompatible with Tablestore SDK for Python V5.1.0 in the following conditions:
    -   The type of the result returned by the Search operation is different between the two versions.

        By default, Tablestore SDK for Python V5.1.0 and earlier return results of the Tuple type. By default, Tablestore SDK for Python V5.2.0 and later returns SearchResponse objects for Search requests. SearchResponse supports the \_\_iter\_\_ method to traverse data. You can use the SearchResponse.v1\_response\(\) method to return results of the Tuple type.

    -   The ParallelScan operation is added.

        By default, the results returned for ParallelScan requests are ParallelScanResponse objects. You can use the SearchResponse.v1\_response\(\) method to return results of the Tuple type.

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

Latest version: 5.2.1.

