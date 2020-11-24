# Instance

An instance is a logical entity used in Tablestore to manage tables. Each instance is equivalent to a database. Tablestore implements application access control and resource measurement at the instance level. After Tablestore is activated, you can create an instance in the Tablestore console and then create and manage tables within this instance.

Tablestore allows you to create up to 10 instances in each Alibaba Cloud account and up to 64 tables within each instance. If you want to raise this quota, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd).

![Instance](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2818958951/p11543.jpg)

## Instance types

Tablestore supports two instance types: high-performance instances and capacity instances. Both instances types support petabytes of data for a single table, but differ in costs and applicable scenarios. The following table compares the two instance types.

**Note:** After an instance is created, you cannot change its instance type. Exercise caution when you select the instance type.

|Instance type|Scenario|Billing method|Read performance|Write performance|Concurrency|
|-------------|--------|--------------|----------------|-----------------|-----------|
|High-performance instance|This instance type is applicable to scenarios that require both high read and write performance and high concurrency, such as gaming, financial risk control, social networking applications, and recommendation systems.|-   Reserved read/write throughput
-   Additional read/write throughput

|High|High|High|
|Capacity instance|This instance type is applicable to services that are not sensitive to the read performance but are sensitive to costs, such as services that involve log monitoring data, Internet of Vehicles Data, device data, time sequence data, logistics data, and public opinion monitoring data.|Additional read/write throughput|Medium|High|Medium|

## Instance naming conventions

The name of an instance must be unique within a region. The name can be the same across different regions. The naming conventions:

-   The name can contain letters, digits, and hyphens \(-\).
-   The name must start with a letter.
-   The name cannot end with a hyphen \(-\).
-   The name is case-insensitive.
-   The name must be 3 to 16 bytes in length.
-   The name cannot contain ali, ay, ots, taobao, or admin.

## Instance types supported in each region

|Region|High-performance instance|Capacity instance|
|:-----|:------------------------|:----------------|
|China \(Hangzhou\)|Supported|Supported|
|East China 1 Finance|Supported|Not supported|
|China \(Shanghai\)|Supported|Supported|
|China East 2 Finance|Not supported|Supported|
|China \(Qingdao\)|Not supported|Supported|
|China \(Beijing\)|Supported|Supported|
|China \(Zhangjiakou\)|Not supported|Supported|
|China \(Hohhot\)|Not supported|Supported|
|China \(Shenzhen\)|Supported|Supported|
|China \(Chengdu\)|Supported|Supported|
|China \(Hong Kong\)|Supported|Supported|
|Singapore|Supported|Not supported|
|Australia \(Sydney\)|Not supported|Supported|
|Malaysia \(Kuala Lumpur\)|Supported|Supported|
|Indonesia \(Jakarta\)|Not supported|Supported|
|Japan \(Tokyo\)|Not supported|Supported|
|Germany \(Frankfurt\)|Not supported|Supported|
|UK \(London\)|Supported|Supported|
|US \(Silicon Valley\)|Supported|Not supported|
|US \(Virginia\)|Supported|Supported|
|India \(Mumbai\)|Supported|Supported|
|UAE \(Dubai\)|Not supported|Supported|

