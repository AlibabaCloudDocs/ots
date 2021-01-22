# Incremental synchronization performance white paper

This topic describes the test on the performance of incremental synchronization of Tunnel Service, including the test environment, tools, plan, indicators, results, and summary.

## Test environment

-   Tablestore instance
    -   Type: high-performance instance
    -   Region: China \(Hangzhou\)
    -   Address: a private IP address, which prevents interferences caused by unknown network issues.
-   Test server
    -   Type: Alibaba Cloud ECS
    -   Region: China \(Hangzhou\)
    -   Model: the ecs.mn4.4xlarge shared balanced instance type
    -   Configuration:
        -   CPU: 16 cores
        -   Memory: 64 GB
        -   NIC: VirtIO network device of Red Hat, Inc.
        -   Operating system: CentOS 7u2

## Test tools

-   Stress testing tool

    The stress testing tool of Tablestore can write data to multiple rows simultaneously by using Tablestore SDK for Java to call the BatchWriteRow operation.

-   Prepartitioning tool

    The stress testing tool of Tablestore can automatically create and prepartition tables based on the configured table names and the number of partitions.

-   Rate statistics tool

    Tablestore SDK for Java can collect statistics for the consumption rate of incremental data and the total number of consumed rows in real time. You can add the logic demonstrated in the following example to the callback to collect rate statistics.

    Examples

    ```
    private static final Gson GSON = new Gson();
        private static final int CAL_INTERVAL_MILLIS = 5000;
        static class PerfProcessor implements IChannelProcessor {
            private static final AtomicLong counter = new AtomicLong(0);
            private static final AtomicLong latestTs = new AtomicLong(0);
            private static final AtomicLong allCount = new AtomicLong(0);
    
            @Override
            public void process(ProcessRecordsInput input) {
                counter.addAndGet(input.getRecords().size());
                allCount.addAndGet(input.getRecords().size());
                if (System.currentTimeMillis() - latestTs.get() > CAL_INTERVAL_MILLIS) {
                    synchronized (PerfProcessor.class) {
                        if (System.currentTimeMillis() - latestTs.get() > CAL_INTERVAL_MILLIS) {
                            long seconds = TimeUnit.MILLISECONDS.toSeconds(System.currentTimeMillis() - latestTs.get());
                            PerfElement element = new PerfElement(System.currentTimeMillis(), counter.get() / seconds, allCount.get());
                            System.out.println(GSON.toJson(element));
                            counter.set(0);
                            latestTs.set(System.currentTimeMillis());
                        }
                    }
                }
            }
    
            @Override
            public void shutdown() {
                System.out.println("Mock shutdown");
            }
        }
    ```


## Test plan

When Tunnel Service is used for data synchronization, the system sequentially synchronizes data within a single channel to maintain the order of data, and synchronizes data within different channels in parallel. For incremental data, the number of channels is equal to the number of partitions in a table. The performance test focuses on how the number of partitions \(channels\) affects the incremental synchronization rate because the overall performance of Tunnel Service strongly correlates to the number of partitions.

**Note:**

-   The number of partitions increases with the data volume. To create partitions in advance, contact Tablestore technical support.
-   Data processing tasks are automatically implemented in Tunnel Service. The tunnel client can be started by using the same tunnel ID. For more information, see [Tunnel clients](/intl.en-US/Function Introduction/Tunnel service/Tunnel clients.md).

-   Test scenarios

    The test is conducted in the following scenarios:

    -   Single-server single-partition synchronization
    -   Single-server 4-partition synchronization
    -   Single-server 8-partition synchronization
    -   Single-server 32-partition synchronization
    -   Single-server 64-partition synchronization
    -   Double-server 64-partition synchronization
    -   Double-server 128-partition synchronization
    **Note:** The test in the preceding scenarios is not an extreme test of the service performance, and therefore does not impose much pressure on the Tablestore instance.

-   Test procedure
    1.  Create and pre-split a table for each test scenario.
    2.  Create a tunnel for incremental synchronization.
    3.  Use the stress testing tool to write incremental data.
    4.  Use the rate statistics tool to measure the queries per second \(QPS\) in real time, and check the consumption of system resources such as CPU and memory.
    5.  Check the total bandwidth consumed during incremental synchronization.
-   Test data description

    As shown in the following figure, sample data includes four primary key columns and one or two attribute columns. The size of each row is approximately 220 bytes. The first primary key \(partition key\) is a 4-byte hash value, which ensures that stress testing data is evenly written to each partition.


## Test indicators

The following indicators are used in the test:

-   QPS \(row\): the number of rows synchronized per second.
-   Average latency \(ms per 1,000 rows\): the amount of time in milliseconds required to synchronize 1,000 rows.
-   CPU \(cores\): the total number of single-core CPUs used to synchronize data.
-   Memory \(GB\): the total physical memory used to synchronize data.
-   Bandwidth \(Mbit/s\): the total bandwidth used to synchronize data.

**Note:** The performance test is based on user experience, rather than extreme testing.

## Test results

This section describes the test results for each scenario. For more information, see the test details.

-   QPS and latency

    The following figure shows the number of rows synchronized per second and the amount of time required to synchronize 1,000 rows in each scenario. As shown in the figure, the QPS increases linearly with the number of partitions.

    In the single-server 64-partition synchronization scenario, the gigabit NIC works at its full capacity, which results in QPS of only 570,000. For more information, see the test details. The average QPS in the double-server 64-partition synchronization scenario reaches 780,000 rows, almost twice as much as the 420,000 QPS achieved in the single-server 32-partition synchronization scenario. In the double-server 128-partition synchronization scenario, the QPS reaches 1,000,000 rows.

-   System resource consumption

    The following figure shows the CPU and memory usage in each scenario. The CPU usage increases linearly with the number of partitions.

    In the single-server single-partition synchronization scenario, 0.25 single-core CPUs are used. In the double-server 128-partition synchronization scenario, only 10.2 single-core CPUs are used when the QPS reaches 1,000,000 rows. The memory usage increases linearly with the number of partitions when the number of partitions is smaller than 32. When more partitions \(32 or 64 partitions\) need to be processed, the memory usage remains stable around 5.3 GB on each server.

-   Total bandwidth consumption

    The following figure shows the total bandwidth consumed during incremental synchronization. In this figure, the consumed bandwidth increases linearly with the number of partitions.

    In the single-server 64-partition synchronization scenario, a total bandwidth of 125 Mbit/s is consumed, which is the maximum rate supported by the gigabit NIC. In the double-server 64-partition synchronization scenario, a bandwidth of 169 Mbit/s is consumed, which is the actual bandwidth required for 64-partition synchronization. This is approximately twice the 86 Mbit/s bandwidth required in the single-server 32-partition synchronization scenario. When the QPS reaches 1,000,000 in the double-server 128-partition synchronization scenario, the total bandwidth consumed reaches 220 Mbit/s.


## Test details

-   Single-server single-channel: 19,000 QPS.
    -   Tested at: 17:40 on January 30, 2019.
    -   QPS: steady at approximately 19,000 rows per second, with a peak rate of 21,800 rows per second.
    -   Latency: approximately 50 ms per 1,000 rows.
    -   CPU utilization: approximately 25% of a single-core CPU.
    -   Memory usage: approximately 0.4% of the total physical memory, or 0.256 GB. \(Each test server provides 64 GB of physical memory.\)
    -   Bandwidth consumption: approximately 4,000 KB/s.
-   Single-server 4-partition synchronization: 70,000 QPS.
    -   Tested at: 20:00 on January 30, 2019.
    -   QPS: steady at approximately 70,000 rows per second, with a peak rate of 72,400 rows per second.
    -   Latency: approximately 14.28 ms per 1,000 rows.
    -   CPU utilizatuin: approximately 70% of a single-core CPU.
    -   Memory usage: approximately 1.9% of the total physical memory, or 1.1 GB. \(Each test server provides 64 GB of physical memory.\)
    -   Bandwidth consumption: approximately 13 Mbit/s.
-   Single-server 8-partition synchronization: 130,000 QPS.
    -   Tested at: 20:20 on January 30, 2019.
    -   QPS: steady at approximately 130,000 rows per second, with a peak rate of 141,644 rows per second.
    -   Latency: approximately 7.69 ms per 1,000 rows.
    -   CPU utilization: approximately 120% of a single-core CPU.
    -   Memory usage: approximately 4.1% of the total physical memory, or 2.62 GB. \(Each test server provides 64 GB of physical memory.\)
    -   Bandwidth consumption: approximately 27 Mbit/s.
-   Single-server 32-partition synchronization: 420,000 QPS.
    -   Tested at: 15:50 on January 31, 2019.
    -   QPS: steady at approximately 420,000 rows per second, with a peak rate of 447,600 rows per second.
    -   Latency: 2.38 ms per 1,000 rows.
    -   CPU utilzation: approximately 450% of a single-core CPU.
    -   Memory usage: approximately 8.2% of the total physical memory, or 5.25 GB. \(Each test server provides 64 GB of physical memory.\)
    -   Bandwidth consumption: approximately 86 Mbit/s.
-   Single-server 64-partition synchronization: 570,000 QPS, with the gigabit NIC working at its full capacity.
    -   Tested at: 22:10 on January 31, 2019.
    -   QPS: steady at approximately 570,000 rows per second, with a peak rate of 581,400 rows per second.
    -   Latency: approximately 1.75 ms per 1,000 rows.
    -   CPU utilization: approximately 640% of a single-core CPU.
    -   Memory usage: approximately 8.4% of the total physical memory, or 5.376 GB.
    -   Bandwidth consumption: approximately 125 Mbit/s, which is the maximum rate of the gigabit NIC.
-   Double-server 64-partition synchronization: 780,000 QPS.
    -   Tested at: 22:30 on January 31, 2019.
    -   QPS: steady at approximately 390,000 rows per second on each server and 780,000 rows per second on both servers.
    -   Latency: approximately 1.28 ms per 1,000 rows.
    -   CPU utilization: approximately 420% of a single-core CPU on each server and 840% of a single-core CPU on both servers.
    -   Memory usage: approximately 8.2% of the total physical memory, or 10.5 GB.
    -   Bandwidth consumption: approximately 169 Mbit/s. This indicates that bandwidth becomes a bottleneck when the number of partitions reaches 64 in single-server scenarios.
-   Double-server 128-partition synchronization: 1,000,000 QPS, when both gigabit NICs are working at their full capacities.
    -   Tested at: 23:20 on January 31, 2019.
    -   QPS: steady at approximately 500,000 rows per second on each server and 1,000,000 rows per second on both servers.
    -   Latency: approximately 1 ms per 1,000 rows.
    -   CPU usage: approximately 560% of a single-core CPU on each server and 1,020% of a single-core CPU on both servers.
    -   Memory usage: approximately 8.2% of the total physical memory for each server, or 10.5 GB.
    -   Bandwidth consumption: approximately 220 Mbit/s.

## Summary

Based on the performance test for incremental synchronization, the QPS for tables with a single or a few partitions is affected by the latency in data reading and only few resources on the server are consumed. As the number of partitions increases, the overall throughput of incremental synchronization of Tunnel Service increases linearly until a system bottleneck \(such as bandwidth in the test\) is encountered. When a resource on a single server is used up, this resource becomes the bottleneck. You can add more servers to increase the overall throughput. The test validates the excellent horizontal scaling performance of Tunnel Service.

