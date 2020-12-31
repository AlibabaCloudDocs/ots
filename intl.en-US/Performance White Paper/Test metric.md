# Test metric

This topic describes the test metrics used for the test.

The performance test uses the following four metrics:

-   Runner: the number of runners
-   Thread: the number of YCSB threads created by each runner
-   QPS \(row\): number of rows written or read per second
-   Avg Latency \(ms\): average latency calculated by runners, in milliseconds

All stress tests use synchronous requests. Therefore, the request queries-per-second \(QPS\) is related to the number of runners and average latency. The following content describes their relationship:

Total QPS = 1,000/ Average latency × 5 \(number of runners\) × Number of threads of each runner

For example, in a scenario with four partitions, five runners, five threads per runner, and an average latency of 4 ms, the theoretical QPS for a single thread is 250 \(1000/4\). The total QPS in this scenario is 6,250, which is calculated based on the formula: Total QPS = 250 × 5 \(number of runners\) × 5 \(number of threads per runner\).

**Note:** This performance test is not a limit test of the service performance. The test does not trigger throttling measures on the Tablestore server. The automatic load balancing feature of Tablestore guarantees horizontal scale-up of the service capabilities provided by a single table.

