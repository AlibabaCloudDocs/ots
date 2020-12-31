# Test result \(capacity instance\)

This topic describes the test results of Tablestore capacity instances.

Test analysis

-   Within the same partition, if the change to the latency of random write operations is insignificant when the stress increases, the resources in this partition are relatively adequate. When the stress increases, the queries-per-second \(QPS\) becomes greater.

-   Within the same partition, if the latency of random read operations becomes longer when stress increases, a queue of requests appears on the server where the partition is located. When the stress increases, the QPS may not linearly increase.

-   The performance of the random read and random range read test cases is significantly affected by the cache hit rate. To prevent the cache hit rate from affecting the test results, the hit rate is set to an extremely low level. In the use of capacity instances, the actual performance quite likely exceeds the results of this test.

-   This performance test is not a limit test of the service performance. The test does not trigger throttling measures on the Tablestore server. The automatic load balancing feature of Tablestore guarantees horizontal scale-up of the service capabilities provided by a single table.


Random write performance

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/20498/153976685112033_en-US.png)

Random read performance

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/20498/153976685112034_en-US.png)

Random batch write performance

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/20498/153976685112035_en-US.png)

Random range read performance

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/20498/153976685112036_en-US.png)

