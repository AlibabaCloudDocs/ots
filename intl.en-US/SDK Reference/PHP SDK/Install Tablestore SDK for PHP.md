# Install Tablestore SDK for PHP

This topic describes how to install and use Tablestore SDK for PHP.

## Prerequisites

-   64-bit PHP 5.5 or later is available.

    You can run the php -v command to view the current PHP version.

    Tablestore uses 64-bit integers. However, in 32-bit PHP, only the STRING type can be used to represent 64-bit INTEGER values. Therefore, Tablestore does not support 32-bit PHP. In Windows, PHP versions earlier than PHP 7 are not actually 64-bit. When you run PHP in Windows, you must upgrade PHP to PHP 7 or modify the environment yourself. We recommend that you use PHP 7 to obtain the optimal performance.

-   cURL extension \(optional\)

    You can run the php -m command to check whether the cURL extension is installed.

    **Note:**

    -   In Ubuntu, you can run the sudo apt-get install php-curl command to install the cURL extension of PHP by using apt-get.
    -   In CentOS, you can run the sudo yum install php-curl command to install the cURL extension of PHP by using YUM.
-   OpenSSL extension \(optional\)

    You must install the OpenSSL PHP extension if you use HTTPS.


## Installation methods

-   Composer

    To install Tablestore SDK for PHP by using Composer, perform the following operations:

    1.  Run the `composer require aliyun/aliyun-tablestore-sdk-php` command in the root directory of the project or declare the dependency on Tablestore SDK for PHP in the composer.json file.

        ```
        "require": {
            "aliyun/aliyun-tablestore-sdk-php": "~5.0"
        }
        ```

    2.  Run the composer install command to install the dependency. After the dependency is installed, check whether your directory structure complies with the following structure:

        ```
         .
         ├── app.php
         ├── composer.json
         ├── composer.lock
         └── vendor
        								
        ```

        In the preceding directory structure, app.php indicates your application. The vendor directory contains the dependent library. You must import the dependency into app.php.

        ```
        require_once __DIR__ . '/vendor/autoload.php';
        								
        ```

        **Note:**

        -   If your project already has autoload.php imported, you do not need to import autoload.php again after you add the SDK dependency.
        -   If a network error occurs when you use Composer, you can enter the following command on the command line to use Packagist that is available to China: `composer config -g repo.packagist composer https://developer.aliyun.com/composer`. For more information about Packagist, see [Images](https://developer.aliyun.com/composer).
-   Source code package

    You can download the source code package by using the following methods:

    -   Select the required version and download the ZIP package at [GitHub](https://github.com/aliyun/aliyun-tablestore-php-sdk/releases).
    -   Click [here](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/php/aliyun-tablestore-php-sdk-5.0.0.tar.gz) to download the source code package.

## Sample programs

Tablestore SDK for PHP provides a variety of sample programs for your reference or use.

You can obtain the sample programs by using one of the following methods:

-   Download and decompress Tablestore SDK for PHP. Find the sample programs in the examples directory.
-   Access the GitHub project for Tablestore SDK for PHP at [aliyun-tablestore-php-sdk](https://github.com/aliyun/aliyun-tablestore-php-sdk/tree/master/examples).

To run a sample program, perform the following operations:

1.  Decompress the downloaded SDK package.
2.  Modify the ExampleConfig.php file in the examples directory.

    ```
    EXAMPLE_END_POINT: the domain name that you use to access the region where your Tablestore instance is located. Example: https://sun.cn-hangzhou.ots.aliyuncs.com.
    EXAMPLE_ACCESS_KEY_ID: the AccessKey ID that you obtained from Alibaba Cloud.
    EXAMPLE_ACCESS_KEY_ID: the AccessKey secret that you obtained from Alibaba Cloud.
    EXAMPLE_INSTANCE_NAME: the instance that you use to run the sample program. The sample program is operated in this instance.
    ```

3.  Run a sample file in the examples directory separately.

The following table lists the sample files.

|File|Description|
|:---|:----------|
|[NewClient.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/NewClient.php)|Demonstrates how to configure the default client.|
|[NewClient2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/NewClient2.php)|Demonstrates how to customize the client.|
|[NewClientLogClosed.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/NewClientLogClosed.php)|Demonstrates how to disable the log display of the client.|
|[NewClientLogDefined.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/NewClientLogDefined.php)|Demonstrates how to customize the log display of the client.|
|[CreateTable.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/CreateTable.php)|Demonstrates how to use CreateTable.|
|[DeleteTable.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/DeleteTable.php)|Demonstrates how to use DeleteTable.|
|[DescribeTable.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/DescribeTable.php)|Demonstrates how to use DescribeTable.|
|[ListTable.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/ListTable.php)|Demonstrates how to use ListTable.|
|[UpdateTable.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/UpdateTable.php)|Demonstrates how to use UpdateTable.|
|[ComputeSplitPointsBySize.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/ComputeSplitPointsBySize.php)|Demonstrates how to use ComputeSplitPointsBySize.|
|[PutRow.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/PutRow.php)|Demonstrates how to use PutRow.|
|[PutRowWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/PutRowWithColumnFilter.php)|Demonstrates how to use PutRow with conditional update.|
|[UpdateRow1.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/UpdateRow1.php)|Demonstrates how to use PUT in UpdateRow.|
|[UpdateRow2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/UpdateRow2.php)|Demonstrates how to use DELETE\_ALL in UpdateRow.|
|[UpdateRow3.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/UpdateRow3.php)|Demonstrates how to use DELETE in UpdateRow.|
|[UpdateRowWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/UpdateRowWithColumnFilter.php)|Demonstrates how to use UpdateRow with conditional update.|
|[GetRow.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRow.php)|Demonstrates how to use GetRow.|
|[GetRow2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRow2.php)|Demonstrates how to set column\_to\_get in GetRow.|
|[GetRowWithSingleColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRowWithSingleColumnFilter.php)|Demonstrates how to use GetRow with conditional filtering.|
|[GetRowWithMultipleColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRowWithMultipleColumnFilter.php)|Demonstrates how to use GetRow with complex conditional filtering.|
|[DeleteRow.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/DeleteRow.php)|Demonstrates how to use DeleteRow.|
|[DeleteRowWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/DeleteRowWithColumnFilter.php)|Demonstrates how to use DeleteRow with conditional filtering.|
|[PKAutoIncrment.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/PKAutoIncrment.php)|Demonstrates how to use auto-increment primary key columns.|
|[BatchGetRow1.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRow1.php)|Demonstrates how to use BatchGetRow to read multiple rows from a table.|
|[BatchGetRow2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRow2.php)|Demonstrates how to use BatchGetRow to read multiple rows from multiple tables.|
|[BatchGetRow3.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRow3.php)|Demonstrates how to use BatchGetRow to read specified columns for multiple rows from a table.|
|[BatchGetRow4.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRow4.php)|Demonstrates how to use BatchGetRow to process returned results.|
|[BatchGetRowWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRowWithColumnFilter.php)|Demonstrates how to use BatchGetRow with conditional filtering.|
|[BatchWriteRow1.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRow1.php)|Demonstrates how to perform multiple PUT operations in BatchWriteRow.|
|[BatchWriteRow2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRow2.php)|Demonstrates how to perform multiple UPDATE operations in BatchWriteRow.|
|[BatchWriteRow3.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRow3.php)|Demonstrates how to perform multiple DELETE operations in BatchWriteRow.|
|[BatchWriteRow4.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRow4.php)|Demonstrates how to perform the UPDATE, PUT, and DELETE operations in BatchWriteRow.|
|[BatchWriteRowWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRowWithColumnFilter.php)|Demonstrates how to use BatchWriteRow with conditional update.|
|[GetRange1.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRange1.php)|Demonstrates how to use GetRange.|
|[GetRange2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRange2.php)|Demonstrates how to use GetRange to obtain the specified columns.|
|[GetRange3.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRange3.php)|Demonstrates how to use GetRange to obtain the specified number of rows.|
|[GetRangeWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRangeWithColumnFilter.php)|Demonstrates how to use GetRange with conditional filtering.|

