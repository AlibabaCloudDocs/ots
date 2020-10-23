# What do I do if Checksum mismatch is reported when I use Tablestore SDK for PHP?

This topic describes the problem description, cause, and solution of the Checksum mismatch exception when you use Tablestore SDK for PHP.

## Problem description

The following exception occurs when you use Tablestore SDK for PHP by using PHP 5.6 in Windows:

```
Fatal error: Uncaught exception 'AliyunOTSOTSClientException' with message 'Checksum mismatch. expected:120,actual:-48'
```

## Cause

Tablestore uses 64-bit integers. However, PHP versions earlier than PHP 7 in Windows do not fully support 64-bit integers and can only process 64 bit integers as strings. Therefore, Tablestore SDK for PHP does not support PHP versions earlier than PHP 7 in Windows.

## Solution

When you use Tablestore SDK for PHP in Windows, ensure that the PHP version is PHP 7 or later. We recommend that you use PHP 7 to obtain the optimal performance.

You can use phpinfo\(\) to check the value of Architecture in PHP configuration information to determine whether your PHP version supports 64-bit integers.

-   If the value of Architecture is X86, the current PHP version supports only 32-bit integers. You must upgrade your PHP version to PHP 7 or later.
-   If the value of Architecture is X64, the current PHP version supports 64-bit integers and can meet your requirement.

