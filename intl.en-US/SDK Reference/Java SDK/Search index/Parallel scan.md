# Parallel scan

In scenarios where you are not concerned with the order of the query results, you can use parallel scan to obtain query results in parallel.

## Background information

The Search operation of search indexes provides a full set of query and analysis capabilities such as sorting and aggregation to return query results in a specified order.

However, in some cases you may be more concerned with the query speed than with the order of the results, such as when you integrate Tablestore with the cluster computing environment Spark or Presto or when you query a specified group of objects. To address these needs, Tablestore provides the ParallelScan operation for search index.

**Note:** SDKs 5.6.0 and later support the parallel scan feature.

Compared with the Search operation, the ParallelScan operation supports all query features but does not provide analysis capabilities such as sorting and aggregation. This way, the query speed is improved by more than five times. You can call the ParallelScan operation to export hundreds of millions of data records within a minute. This capability to export data can be horizontally scaled without limits.

The maximum number of rows to return by sending a single parallel scan request is greater than that of a Search request. A single Search request can query up to 100 rows, whereas a single parallel scan request can query up to 2,000 rows. The parallel scan feature allows you to use multiple threads to initiate requests, which accelerates data exports.

## Scenarios

-   Use the Search operation if you want to sort or aggregate query results or if the query request is sent from an end user.
-   Use the ParallelScan operation if you do not need to sort query results and want to quickly obtain all matched results or if the data is queried by a computing environment such as Spark or Presto.

## Characteristics

Compared with the Search operation, the ParallelScan operation has the following characteristics:

-   Stable results

    Parallel scan tasks are subject to the data status. In a session, the result set of the scanned data is determined by the data status when the first request is initiated. If data is inserted or modified after the first request is sent, the result set is not affected.

-   Sessions

    ParallelScan-related operations use sessions. The session ID can be used to determine the result set of scanned data in the following process:

    1.  Use the ComputeSplits operation to query the maximum number of concurrent threads and the current session ID.
    2.  Initiate multiple concurrent parallel scan requests to read data. You must specify the current session ID and the concurrency ID in these requests.
    In scenarios where session IDs are difficult to obtain, you can also call ParallelScan to initiate a request without specifying a session ID. However, if you send a request without a session ID, the obtained result set may contain duplicate data.

    Tablestore returns the OTSSessionExpired error code when network exceptions, thread exceptions, dynamic modifications on schema, or index switchover occurs during the parallel scan process and the data scan stops. In this case, you must initiate another parallel scan task to scan data again.

-   Maximum number of concurrent threads

    The maximum number of concurrent threads supported by parallel scan is determined by the returned value of ComputeSplits. The larger volume of the data requires more concurrent threads.

    A single request is specified by one query statement. For example, if you use the Search operation to query results where the value of city is Hangzhou, all data that matches this condition is returned in the result. However, if you use the ParallelScan operation and the number of concurrent threads is 2, each query returns half of the results. The complete result set consists of the two concurrent result sets.

-   Rows to return for each request

    The default value of limit is 2000. The maximum value of limit is 2000. If the value you set for limit exceeds 2000, the performance remains unchanged.

-   Performance

    The query speed of a single parallel scan request is five times higher than that of a single Search request. If you increase the number of concurrent threads when you use parallel scan, the query speed increases linearly. For example, eight concurrent parallel scan threads can improve the query speed by four times.

-   Cost

    Parallel scan requests consume fewer resources and are offered at a lower price. If you need to export large amounts of data, we recommend that you use the ParallelScan operation.

-   Columns to return

    Only indexed columns can be queried in search indexes. The ReturnType parameter can be set to RETURN\_ALL\_INDEX or RETURN\_SPECIFIED, but cannot be set to RETURN\_ALL.

    Parallel scan can return formatting values of the ARRAY, GEOPOINT, and NEST columns. The formatting values may be different from the values in the table. For example, if the value written to the table is "\[1,2, 3, 4\]", the value exported by using the ParallelScan operation is "\[1,2,3,4\]".

-   Limits

    The maximum number of concurrent parallel scan tasks is 10. This limit will be adjusted to meet your requirements. Concurrent tasks that have the same session ID and the same ScanQuery parameter value are considered as one task. A parallel scan task starts when the parallel scan request is sent for the first time, and ends when all data is scanned or the token expires.


## Operations

Concurrent data export is implemented by using the following API operations:

-   ComputeSplits: You can call this operation to query the maximum number of concurrent tasks for a single ParallelScan request.
-   ParallelScan: You can call this operation to export data.

## Parameters

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table.|
|indexName|The name of the search index.|
|scanQuery|query|The query statement for the search index. The operation supports term-level query, fuzzy query, range query, geo query, and nested query, which are similar to those of the Search operation.|
|limit|The maximum number of rows that can be returned at a time.|
|maxParallel|The maximum number of concurrent requests. The maximum number of concurrent threads varies based on the data volume. The larger the volume of data is, the more concurrent threads are supported. You can use the ComputeSplits operation to query the maximum number of concurrent threads.|
|currentParallelId|The ID of the concurrent request. Valid values: \[0, Value of maxParallel\)|
|token|The token used for pagination. The result of the parallel scan request contains the token for the next page. You can use the token to query data for the following page.|
|aliveTime|The validity period of the current ParallelScan task. It is also the validity period of the token. We recommend that you use the default value. Default value: 60. Unit: seconds. If the next request is not initiated within the validity period, more data cannot be queried. The validity period of the token is refreshed each time you send a request.|
|columnsToGet|You can use parallel scan to scan only data in search indexes. To use parallel scan for a search index, you must set store to true when you create the search index.|
|sessionId|The session ID of the parallel scan task. You can call the ComputeSplits operation to create a session and query the maximum number of concurrent threads that are supported by the task.|

## Examples

The following examples describe how to scan data by sending a single request or by using multiple threads at a time:

-   Scan data by sending a single request

    When you use parallel scan, the code for a single request is simpler than that for a request that uses multiple threads. The currentParallelId and maxParallel parameters are not required for a single request.

    ```
    import java.util.ArrayList;
    import java.util.Arrays;
    import java.util.List;
    
    import com.alicloud.openservices.tablestore.SyncClient;
    import com.alicloud.openservices.tablestore.model.ComputeSplitsRequest;
    import com.alicloud.openservices.tablestore.model.ComputeSplitsResponse;
    import com.alicloud.openservices.tablestore.model.Row;
    import com.alicloud.openservices.tablestore.model.SearchIndexSplitsOptions;
    import com.alicloud.openservices.tablestore.model.iterator.RowIterator;
    import com.alicloud.openservices.tablestore.model.search.ParallelScanRequest;
    import com.alicloud.openservices.tablestore.model.search.ParallelScanResponse;
    import com.alicloud.openservices.tablestore.model.search.ScanQuery;
    import com.alicloud.openservices.tablestore.model.search.SearchRequest.ColumnsToGet;
    import com.alicloud.openservices.tablestore.model.search.query.MatchAllQuery;
    import com.alicloud.openservices.tablestore.model.search.query.Query;
    import com.alicloud.openservices.tablestore.model.search.query.QueryBuilders;
    
    public class Test {
    
        public static List<Row> scanQuery(final SyncClient client) {
            String tableName = "<TableName>";
            String indexName = "<IndexName>";
            // Query the session ID and the maximum number of concurrent threads supported by this request.
            ComputeSplitsRequest computeSplitsRequest = new ComputeSplitsRequest();
            computeSplitsRequest.setTableName(tableName);
            computeSplitsRequest.setSplitsOptions(new SearchIndexSplitsOptions(indexName));
            ComputeSplitsResponse computeSplitsResponse = client.computeSplits(computeSplitsRequest);
            byte[] sessionId = computeSplitsResponse.getSessionId();
            int splitsSize = computeSplitsResponse.getSplitsSize();
            /*
             * Create a parallel scan request.
             */
            ParallelScanRequest parallelScanRequest = new ParallelScanRequest();
            parallelScanRequest.setTableName(tableName);
            parallelScanRequest.setIndexName(indexName);
            ScanQuery scanQuery = new ScanQuery();
            // This query determines the range of data to scan. You can create a nested and complex query.
            Query query = new MatchAllQuery();
            scanQuery.setQuery(query);
    
            // Set the number of rows to return for a single request.
            scanQuery.setLimit(2000);
            parallelScanRequest.setScanQuery(scanQuery);
            ColumnsToGet columnsToGet = new ColumnsToGet();
            columnsToGet.setColumns(Arrays.asList("col_1", "col_2"));
            parallelScanRequest.setColumnsToGet(columnsToGet);
            parallelScanRequest.setSessionId(sessionId);
    
            /*
             * Use builder to create a parallel scan request that has the same features as the preceding request.
             */
            ParallelScanRequest parallelScanRequestByBuilder = ParallelScanRequest.newBuilder()
                .tableName(tableName)
                .indexName(indexName)
                .scanQuery(ScanQuery.newBuilder()
                    .query(QueryBuilders.matchAll())
                    .limit(2000)
                    .build())
                .addColumnsToGet("col_1", "col_2")
                .sessionId(sessionId)
                .build();
            List<Row> result = new ArrayList<>();
    
            /*
             * Use the native API operations to scan data.
             */
            {
                ParallelScanResponse parallelScanResponse = client.parallelScan(parallelScanRequest);
                // Query the token of ScanQuery for the next request.
                byte[] nextToken = parallelScanResponse.getNextToken();
                // Obtain the data.
                List<Row> rows = parallelScanResponse.getRows();
                result.addAll(rows);
                while (nextToken ! = null) {
                    // Configure the token.
                    parallelScanRequest.getScanQuery().setToken(nextToken);
                    // Continue to scan data.
                    parallelScanResponse = client.parallelScan(parallelScanRequest);
                    // Obtain the data.
                    rows = parallelScanResponse.getRows();
                    result.addAll(rows);
                    nextToken = parallelScanResponse.getNextToken();
                }
            }
    
            /*
             * Recommended method.
             * Use an iterator to scan all matched data. This method has the same query speed but is easier to use than the previous method.
             */
            {
                RowIterator iterator = client.createParallelScanIterator(parallelScanRequestByBuilder);
                while (iterator.hasNext()) {
                    Row row = iterator.next();
                    result.add(row);
                    // Obtain the specific values.
                    String col_1 = row.getLatestColumn("col_1").getValue().asString();
                    long col_2 = row.getLatestColumn("col_2").getValue().asLong();
                }
            }
    
            /*
             * Retry if the operation fails. If the caller of this function has a retry mechanism or you do not want to retry if the operation fails, you can ignore this part.
             * To ensure availability, we recommend that you restart parallel scan tasks when exceptions occur. The parallel scan task starts from the beginning.
             * The following exceptions may occur when you send a parallel scan request:
             * 1. Exceptions of the session occur on the server side. The error code is OTSSessionExpired.
             * 2. Exceptions such as a network exception occur on the client side.
             */
            try {
                // Execute the processing logic.
                {
                    RowIterator iterator = client.createParallelScanIterator(parallelScanRequestByBuilder);
                    while (iterator.hasNext()) {
                        Row row = iterator.next();
                        // Query the rows. If you have sufficient memory, you can add the rows to a list.
                        result.add(row);
                    }
                }
            } catch (Exception ex) {
                // Retry a parallel scan task.
                {
                    result.clear();
                    RowIterator iterator = client.createParallelScanIterator(parallelScanRequestByBuilder);
                    while (iterator.hasNext()) {
                        Row row = iterator.next();
                        // Query the rows. If you have sufficient memory, you can add the rows to a list.
                        result.add(row);
                    }
                }
            }
            return result;
        }
    }
    ```

-   Use concurrent threads to scan data

    ```
    import java.util.ArrayList;
    import java.util.List;
    
    import com.alicloud.openservices.tablestore.SyncClient;
    import com.alicloud.openservices.tablestore.model.ComputeSplitsRequest;
    import com.alicloud.openservices.tablestore.model.ComputeSplitsResponse;
    import com.alicloud.openservices.tablestore.model.Row;
    import com.alicloud.openservices.tablestore.model.SearchIndexSplitsOptions;
    import com.alicloud.openservices.tablestore.model.iterator.RowIterator;
    import com.alicloud.openservices.tablestore.model.search.ParallelScanRequest;
    import com.alicloud.openservices.tablestore.model.search.ScanQuery;
    import com.alicloud.openservices.tablestore.model.search.query.QueryBuilders;
    
    public class Test2 {
    
        public static void scanQueryWithMultiThread(final SyncClient client) throws InterruptedException {
            final String tableName = "yourTableName";
            final String indexName = "yourIndexName";
    
            // Query the session ID and the maximum number of concurrent threads supported by this request.
            ComputeSplitsRequest computeSplitsRequest = new ComputeSplitsRequest();
            computeSplitsRequest.setTableName(tableName);
            computeSplitsRequest.setSplitsOptions(new SearchIndexSplitsOptions(indexName));
            ComputeSplitsResponse computeSplitsResponse = client.computeSplits(computeSplitsRequest);
            final byte[] sessionId = computeSplitsResponse.getSessionId();
            final int maxParallel = computeSplitsResponse.getSplitsSize();
    
            /*
             * If you want to implement multithreading by using a function, you can build an internal class to inherit the thread.
             * You can also build an external class to make the code more organized.
             */
            final class ThreadForScanQuery extends Thread {
                private int currentParallelId;
    
                private ThreadForScanQuery(int currentParallelId) {
                    this.currentParallelId = currentParallelId;
                    this.setName("ThreadForScanQuery:" + maxParallel + "-" + currentParallelId);  // Set the name of the thread.
                }
    
                @Override
                public void run() {
                    try {
                        // Execute the processing logic.
                        {
                            ParallelScanRequest parallelScanRequest = ParallelScanRequest.newBuilder()
                                .tableName(tableName)
                                .indexName(indexName)
                                .scanQuery(ScanQuery.newBuilder()
                                    .query(QueryBuilders.range("col_long").lessThan(123)) // Specify what data is queried.
                                    .limit(2000)
                                    .currentParallelId(currentParallelId)
                                    .maxParallel(maxParallel)
                                    .build())
                                .addColumnsToGet("col_1", "col_2", "col_3")  // Only columns in the search index can be queried. Alternatively, you can set returnAllColumnsFromIndex to true to obtain data in all indexes.
                                //.returnAllColumnsFromIndex(true)
                                .sessionId(sessionId)
                                .build();
                            // Use an iterator to query all the data.
                            RowIterator ltr = client.createParallelScanIterator(parallelScanRequest);
                            System.out.println("thread name:" + this.getName());
                            long count = 0;
                            while (ltr.hasNext()) {
                                Row row = ltr.next();
                                // Add custom processing logic or create a list containing the rows to query in all threads.
                                count++;
                            }
                            System.out.println("thread name:" + this.getName() + ", total data records obtained by the thread:" + count);
                        }
                    } catch (Exception ex) {
                        // If exceptions occur, you can restart the parallel scan task.
                    }
                }
            }
    
            // Execute threads concurrently. Valid values of currentParallelId: [0, Value of maxParallel).
            List<ThreadForScanQuery> threadList = new ArrayList<ThreadForScanQuery>();
            for (int i = 0; i < maxParallel; i++) {
                ThreadForScanQuery thread = new ThreadForScanQuery(i);
                threadList.add(thread);
            }
    
            // Initiate all the threads simultaneously.
            for (ThreadForScanQuery thread : threadList) {
                thread.start();
            }
    
            // The main thread is blocked until all threads are completed.
            for (ThreadForScanQuery thread : threadList) {
                thread.join();
            }
            System.out.println("all thread done!") ;
        }
    }
    ```


