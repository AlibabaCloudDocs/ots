# Tablestore HBase Client

In addition to SDKs and RESTful operations, you can also use Tablestore HBase Client to access Tablestore. Java applications that support open source HBase operations can use Tablestore HBase Client to access Tablestore.

Based on Tablestore SDKs for Java V4.2.x and later, Tablestore HBase Client supports open source operations for HBase V1.x.x and later.

You can obtain Tablestore HBase Client by using one of the following methods:

-   GitHub: [tablestore-hbase-client project](https://github.com/aliyun/aliyun-tablestore-hbase-client)
-   [Compressed package](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/50125/cn_zh/1486705375325/tablestore-hbase-client-1.2.0.zip)
-   Maven

    ```
     <dependencies>
            <dependency>
                <groupId>com.aliyun.openservices</groupId>
                <artifactId>tablestore-hbase-client</artifactId>
                <version>1.2.0</version>
            </dependency>
        </dependencies>
    				
    ```


Tablestore is a fully managed NoSQL database service. When you use Tablestore HBase Client, you can ignore HBase Server. Instead, you need only to perform table or data operations by using operations provided by Client.

Compared with self-built HBase services, Tablestore has the following advantages:

|Item|Tablestore|Self-built HBase cluster|
|:---|:---------|:-----------------------|
|Cost|Charges fees based on actual data volumes. Tablestore provides high performance and capacity instances to meet the requirements of different scenarios.|Allocates resources based on traffic peaks. Resources remain idle during off-peak periods, which results in high operation and maintenance costs.|
|Security|Integrates Alibaba Cloud RAM and supports multiple authentication and authorization mechanisms, VPC, and primary/RAM user management. Authorization granularity can be defined at both the table-level and operation-level.|Requires extra security mechanisms.|
|Reliability|Supports automatic redundant data backup and failover. Data availability is 99.9% or greater, and data reliability is 99.99999999%.|Requires extra mechanisms to ensure cluster reliability.|
|Scalability|Server Load Balancer \(SLB\) of Tablestore supports PB-level data transfer from a single table. Manual resizing is not needed even if millions of bytes of data is concurrently stored.|Complex online and offline processes are required if a cluster reaches high usage capacity, which impacts online services.|

