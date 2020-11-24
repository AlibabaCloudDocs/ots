# Parallel scan

In scenarios where the order of query results does not matter, you can use parallel scan to perform a query using several parallel threads.

## Background information

The Search operation of search index provides a full set of query and analysis capabilities such as sorting and aggregation to return query results in a specified order.

However, in some cases you may be more concerned with the query speed than with the order of the results, such as when you integrate Tablestore with the Spark or Presto cluster computing environment or when you query a specified group of objects. To address these needs, Tablestore provides the ParallelScan operation for search index.

**Note:** SDKs 5.6.0 and later support the parallel scan feature.

Compared with the Search operation, the ParallelScan operation supports all query features but does not provide analysis capabilities such as sorting and aggregation. This way, the query speed is improved by more than five times. You can call the ParallelScan operation to export hundreds of millions of data records within a minute. This capability of exporting data can be horizontally expanded without limits.

The maximum number of rows to return by sending a single parallel scan request is greater than that of a Search request. A single Search request can query up to 100 rows, whereas a single parallel scan request can query up to 5,000 rows. The parallel scan feature allows you to use multiple threads to initiate requests, which accelerates data exports.

## Scenarios

-   Use the Search operation if you want to sort or aggregate query results or if the query request is sent from an end user.
-   Use the ParallelScan operation if you do not need to sort query results but want to obtain all matched results quickly or if you use a computing environment such as Spark or Presto to query data.

## Features

Compared with the Search operation, the ParallelScan operation has the following features:

-   Stable results

    Parallel scan tasks are subject to the data status. In a session, the result set of the scanned data is determined by the data status when the first request is initiated. If data is inserted or modified after the first request is sent, the result set is not affected.

-   Sessions

    ParallelScan-related operations use sessions. Session IDs can be used to determine the result sets of scanned data in the following process:

    1.  Use the ComputeSplits operation to query the maximum number of concurrent threads and the current session ID.
    2.  Initiate multiple concurrent parallel scan requests to read data. You must specify the current session ID and the concurrency ID in these requests.
    In scenarios where session IDs are difficult to obtain, you can call ParallelScan to initiate a request without specifying a session ID. However, if you send a request without a session ID, the obtained result set may contain duplicate data.

    Tablestore returns the OTSSessionExpired error code when network exceptions, thread exceptions, dynamic modifications on schema, or index switchovers occur during a parallel scan and stop the scan. In this case, you must initiate another parallel scan task to scan data again.

-   Maximum number of concurrent threads

    The maximum number of concurrent threads supported by parallel scan is determined by the returned value of ComputeSplits. The larger the volume of data is, the more concurrent threads are supported.

    A single request is specified by one query statement. For example, if you use the Search operation to query results where the value of city is Hangzhou, all data that matches this condition is returned in the result. However, if you use the ParallelScan operation and the number of concurrent threads is 2, each query returns half of the results. The complete result set consists of the two concurrent result sets.

-   Rows to return for each request

    The default value of limit is 2000. The maximum value of limit is 5000. If you set the value of limit to greater than 5000, the performance remains unchanged.

-   Performance

    The query speed of a single parallel scan request is five times higher than that of a single Search request. If you increase the number of concurrent threads when you use parallel scan, the query speed increases linearly. For example, eight concurrent parallel scan threads can improve the query speed by four times.

-   Cost

    Parallel scan requests consume fewer resources and are offered at a lower price. If you need to export large amounts of data, we recommend that you use the ParallelScan operation.

-   Columns to return

    Only indexed columns can be queried in search indexes. The ReturnType parameter can be set to RETURN\_ALL\_INDEX or RETURN\_SPECIFIED, but cannot be set to RETURN\_ALL.

    Arrays, geographical locations, and nested fields can be returned. However, the field values returned are formatted and may be different from those written to the table. For example, the value written to a table is "\[1,2, 3, 4\]", the value returned by the ParallelScan operation is "\[1,2,3,4\]".

-   Limits

    The maximum number of concurrent parallel scan tasks is 10. This limit will be adjusted in the future. Concurrent tasks that have the same session ID and the same ScanQuery parameter value are considered as one task. A parallel scan task starts when the parallel scan request is sent for the first time, and ends when all data is scanned or the token expires.


## API Operations

Concurrent data export is implemented by using the following API operations:

-   ComputeSplits: You can call this operation to query the maximum number of concurrent tasks for a single ParallelScan request.
-   ParallelScan: You can call this operation to export data.

## Parameters

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table.|
|indexName|The name of the search index.|
|scanQuery|query|The query statement for the search index. The operation supports exact query, fuzzy query, range query, geo query, and nested query. These features are similar to those of the Search operation.|
|limit|The maximum number of rows that can be returned at a time.|
|maxParallel|The maximum number of concurrent threads. The maximum number of concurrent threads varies with the data volume. The larger the volume of data is, the more concurrent threads are supported. You can use the ComputeSplits operation to query the maximum number of concurrent requests.|
|currentParallelId|The ID of the concurrent request. Valid values: \[0, Value of maxParallel\)|
|token|The token used for pagination. The result of the parallel scan request contains the token for the next page. You can use the token to query data for the following page.|
|aliveTime|The validity period of the current parallel scan task. It is also the validity period of the token. We recommend that you use the default value. If the next request is not initiated within the validity period, more data cannot be queried. The validity period of the token is refreshed each time you send a request.|
|columnsToGet|You can use parallel scan to query only indexed columns in search indexes. You must set store to true when you create a search index.|
|sessionId|The session ID of the parallel scan task. You can call the ComputeSplits operation to create a session and query the maximum number of concurrent requests that is supported by the task.|

## Examples

The following code provides examples on how to scan data by sending a single request or by using multiple threads at a time:

-   Send a single request to scan data

    When you use parallel scan, the code for a single request is simpler than that for a request using multithreading. The currentParallelId and maxParallel parameters are not required for a single request.

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
             * To ensure availability, we recommend that you restart parallel scan tasks when exceptions occur. The parallel scan task starts as a new one.
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
                        // Query the rows. If you have enough memory space, you can add the rows to a list.
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
                        // Query the rows. If you have enough memory space, you can add the rows to a list.
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
                            // Use an iterator to query all of the data.
                            RowIterator ltr = client.createParallelScanIterator(parallelScanRequest);
                            System.out.println("thread name:" + this.getName());
                            long count = 0;
                            while (ltr.hasNext()) {
                                Row row = ltr.next();
                                // Add custom processing logic or create a list that contains the rows to query in all threads.
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
    
            // The main thread is blocked until all threads are complete.
            for (ThreadForScanQuery thread : threadList) {
                thread.join();
            }
            System.out.println("all thread done!") ;
        }
    }
    ```


