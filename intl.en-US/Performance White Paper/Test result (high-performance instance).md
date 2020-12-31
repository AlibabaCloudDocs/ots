# Test result \(high-performance instance\)

This topic describes the test results of Tablestore high-performance instances.

Test analysis

-   Within the same partition, if the change to the latency of random write operations is insignificant when the stress increases, the resources in this partition are relatively adequate. When the stress increases, the queries-per-second \(QPS\) becomes greater.

-   Within the same partition, if the latency of random write operations becomes longer when stress increases, a queue of requests appears on the server where the partition is located. When the stress increases, the QPS may not linearly increase.

-   This performance test is not a limit test of the service performance. The test does not trigger throttling measures on the Tablestore server. The automatic load balancing feature of Tablestore guarantees horizontal scale-up of the service capabilities provided by a single table.


Random write performance

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/20497/153976680012029_en-US.png)

Random read performance

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/20497/153976680112030_en-US.png)

Random batch write performance![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/20497/153976680112031_en-US.png)

Random range read performance

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/20497/153976680112032_en-US.png)

