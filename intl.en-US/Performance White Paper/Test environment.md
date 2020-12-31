# Test environment

This topic describes the test environment used for the Tablestore performance test.

## Tablestore instances

This test uses two Tablestore instances:

-   High-performance instances: China \(Hangzhou\)
-   Capacity instance: China \(Hangzhou\)

**Note:**

-   The high-performance instance and capacity instance use different disk categories. The high-performance instance uses SSDs and the capacity instance uses a combination of SSD and SATA disks. The high-performance instance provides better performance in reading data from the disks. The unit prices of the capacity instance is more cost-effective with the performance comparable to the high-performance instance in writing data to the disks. The drawback of the capacity instance is the low disk data reading performance. The two instances provide similar performance when data is read from the cache.
-   Tablestore serves as a pay-as-you-go service. Tablestore charges you based on the resources you actually use per hour. Tablestore supports extremely high read/write concurrency. A large-scale performance test may result in high fees. If you plan to perform a large-scale performance test, [submit a ticket](https://selfservice.console.aliyun.com/ticket/createIndex) to obtain the test result while cost-effectiveness is ensured.

## Runners

All tests are completed by using the same group of runners.

-   Quantity: 5
-   Type: Alibaba Cloud ECS
-   Region: Hangzhou Zone F
-   Model: shared computing
-   Configurations:
    -   CPU: eight cores
    -   Memory: 8 GB
    -   Instance type: I/O optimized
    -   Network type: classic network
    -   Operating system: Ubuntu 16.04 64-bit

**Note:** The quality of public network environments cannot be guaranteed. Tablestore operations are sensitive to the network latency. Therefore, ECS instances in the same region as the Tablestore instances are used and an internal network address of Tablestore is used to avoid interference from unpredictable network factors.

