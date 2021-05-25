# SDK usage

Tablestore SDKs allow you to use Tunnel Service. Before you use Tunnel Service, you must familiarize yourself with the usage notes and API operations of Tunnel Service.

## Usage notes

-   By default, the system starts a thread pool to read and process data based on TunnelWorkerConfig. If you want to start multiple TunnelWorkers on a single server, we recommend that you configure the TunnelWorkers to share the same TunnelWorkerConfig.
-   If you create a differential tunnel to consume full and incremental data, the incremental data logs of the tunnel are retained for a maximum of seven days. The specific expiration time of incremental logs is consistent with that of logs in streams for a data table. If the tunnel does not consume full data within seven days, an OTSTunnelExpired error occurs when the tunnel starts to consume incremental data. As a result, the tunnel cannot consume incremental data. If you estimate that the tunnel cannot consume full data within seven days, submit a ticket or join DingTalk group 23307953 to contact technical support.
-   TunnelWorker requires time to warm up for initialization. The heartbeatIntervalInSec parameter in TunnelWorkerConfig specifies the time that is required for TunnelWorker to warm up. You can use the setHeartbeatIntervalInSec method in TunnelWorkerConfig to set this parameter. The default value is 30s. The minimum value is 5s.
-   When the mode switches from the full channel to the incremental channel, the full channel is closed and the incremental channel is started. This process requires a period of time for initialization. The heartbeatIntervalInSec parameter specifies the initialization time.
-   When the TunnelWorker client is shut down due to an unexpected exit or manual termination, TunnelWorker uses one of the following methods to automatically recycle resources: Release the thread pool. Automatically call the shutdown method that you have registered for the Channel class, and shut down the tunnel.

## Operations

|Operation|Description|
|---------|-----------|
|CreateTunnel|Creates a tunnel|
|ListTunnel|Queries the information about the tunnels in the specified table.|
|DescribeTunnel|Queries the information about the channels in the specified tunnel.|
|DeleteTunnel|Deletes a tunnel.|

## Use Tablestore SDKs

You can use Tablestore SDKs in the following programming languages to implement Tunnel Service:

-   [Java SDK](/intl.en-US/SDK Reference/Java SDK/Tunnel service/Overview.md)
-   [Go SDK](/intl.en-US/SDK Reference/Go SDK/Tunnel Service/Install Tunnel Service.md)

## Use Tunnel Service

Tablestore SDK for Java allows you to use Tunnel Service.

1.  Initialize a TunnelClient instance.

    ```
    // Set endPoint to the endpoint of the Tablestore instance. Example: https://instance.cn-hangzhou.ots.aliyuncs.com. 
    // Set accessKeyId to the AccessKey ID and accessKeySecret to the AccessKey secret that you use to access Tablestore. 
    // Set instanceName to the name of the instance. 
    final String endPoint = "";
    final String accessKeyId = "";
    final String accessKeySecret = "";
    final String instanceName = "";
    TunnelClient tunnelClient = new TunnelClient(endPoint, accessKeyId, accessKeySecret, instanceName);
    ```

2.  Create a tunnel.

    Create a table for testing or prepare an existing table before you create a tunnel. To create a table for testing, you can use the createTable method in the SyncClient class or go to the Tablestore console.

    ```
    // You can create three types of tunnels by using TunnelType.BaseData, TunnelType.Stream, and TunnelType.BaseAndStream. 
    // The following code provides an example on how to create a differential tunnel. To create a tunnel of another type, set TunnelType in CreateTunnelRequest to the required type. 
    final String tableName = "testTable";
    final String tunnelName = "testTunnel";
    CreateTunnelRequest request = new CreateTunnelRequest(tableName, tunnelName, TunnelType.BaseAndStream);
    CreateTunnelResponse resp = tunnelClient.createTunnel(request);
    // Use tunnelId to initialize TunnelWorker. You can call the ListTunnel or DescribeTunnel operation to obtain the tunnel ID. 
    String tunnelId = resp.getTunnelId(); 
    System.out.println("Create Tunnel, Id: " + tunnelId);
    ```

3.  Customize the data consumption callback to start automatic data consumption.

    ```
    // Customize the data consumption callback to implement the IChannelProcessor operation. Specify the process and shutdown methods. 
    private static class SimpleProcessor implements IChannelProcessor {
        @Override
        public void process(ProcessRecordsInput input) {
            // ProcessRecordsInput includes the data that you have obtained. 
            System.out.println("Default record processor, would print records count");
            System.out.println(
                // NextToken is used to paginate the data of TunnelClient. 
                String.format("Process %d records, NextToken: %s", input.getRecords().size(), input.getNextToken()));
            try {
                // Simulate the processing of data consumption. 
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        @Override
        public void shutdown() {
            System.out.println("Mock shutdown");
        }
    }
    
    // By default, the system starts a thread pool to read and process data based on TunnelWorkerConfig. 
    // If you want to start multiple TunnelWorkers on a single server, we recommend that you configure the TunnelWorkers to share the same TunnelWorkerConfig. TunnelWorkerConfig contains the larger number of advanced parameters. 
    TunnelWorkerConfig config = new TunnelWorkerConfig(new SimpleProcessor());
    // Configure TunnelWorker and start automatic data processing. 
    TunnelWorker worker = new TunnelWorker(tunnelId, tunnelClient, config);
    try {
        worker.connectAndWorking();
    } catch (Exception e) {
        e.printStackTrace();
        worker.shutdown();
        tunnelClient.shutdown();
    }
    ```


## Configure TunnelWorkerConfig

TunnelWorkerConfig allows you to customize parameters for a TunnelClient instance based on your requirements. The following table describes the parameters.

|Item|Parameter|Description|
|----|---------|-----------|
|Configure the interval to detect heartbeats and the timeout period to receive heartbeats|heartbeatTimeoutInSec|The timeout period to receive heartbeats. Default value: 300. Unit: seconds.

When a heartbeat timeout occurs, the tunnel server considers that the current TunnelClient instance is unavailable. The tunnel client must try to reconnect to the tunnel server. |
|heartbeatIntervalInSec|The interval to detect heartbeats. You can detect heartbeats to detect active channels, update the status of channels, and automatically initialize data processing tasks.

Default value: 30. Minimum value: 5. Unit: seconds. |
|Interval between checkpoints|checkpointIntervalInMillis|The interval between checkpoints when data is consumed. The interval is recorded on the tunnel server. Default value: 5000. Unit: ms.

**Note:**

-   Data to read is stored in different servers. Various errors may occur when you run processes. For example, the server may restart due to environmental factors. The tunnel server regularly records checkpoints after data is processed. A task processes data from the previous checkpoint after the task is restarted. In exceptional conditions, Tunnel Service may sequentially synchronize data once or multiple times. If some data is reprocessed, check the business processing logic.
-   To prevent data being reprocessed when errors occur, record more checkpoints. However, an excessive number of checkpoints may compromise the system throughput. We recommend that you record the checkpoints based on your business requirements. |
|The client tag|clientTag|The custom client tag that is used to generate a tunnel client ID. You can customize this parameter to uniquely identify TunnelWorkers.|
|The custom callback to process data|channelProcessor|The callback that you register to process data, including the process and shutdown methods.|
|The configuration of the thread pool to read and process data|readRecordsExecutor|The thread pool to read data. If you do not have special requirements, use the default configuration.|
|processRecordsExecutor|The thread pool to process data. If you do not have special requirements, use the default configuration. **Note:**

-   When you customize the thread pool, we recommend that you set the number of threads to the number of the channels in the tunnel. This way, each channel can be quickly allocated with computing resources such as CPU.
-   Tablestore performs the following operations for the default configurations of the pool to ensure throughput:
    -   Allocate 32 core threads in advance to guarantee real-time throughput when a small amount of data or a small number of channels exists.
    -   Reduce the queue length when a large amount of data must be processed or when a large number of channels exist. This way, the policy is triggered to create a thread in the pool and quickly allocate more computing resources.
    -   We recommend that you set the thread keep-alive time to 60s. If the amount of data that must be processed is reduced, you can recycle thread resources. |
|Memory control|maxChannelParallel|The concurrency level of channels to read and process data for memory control. The default value is -1, which indicates that the concurrency level is unlimited.

**Note:** This configuration applies only to Tablestore SDK for Java V5.10.0 and later. |
|Maximum backoff time|maxRetryIntervalInMillis|The reference value to calculate the maximum backoff time for the tunnel. The maximum backoff time is set to a random value around the reference value. The range of valid values for the maximum backoff time is computed by using the formula: 0.75 × maxRetryIntervalInMillis to 1.25 × maxRetryIntervalInMillis. Default value: 2000 ms. Minimum value: 200 ms.

**Note:**

-   This configuration applies only to Tablestore SDK for Java V5.4.0 and later.
-   If the amount of data that must be processed is smaller than 900 KB or 500 pieces for each export, the tunnel client uses exponential backoff for the tunnel until the maximum backoff time is reached. |

## Appendix: complete code

```
import com.alicloud.openservices.tablestore.TunnelClient;
import com.alicloud.openservices.tablestore.model.tunnel.CreateTunnelRequest;
import com.alicloud.openservices.tablestore.model.tunnel.CreateTunnelResponse;
import com.alicloud.openservices.tablestore.model.tunnel.TunnelType;
import com.alicloud.openservices.tablestore.tunnel.worker.IChannelProcessor;
import com.alicloud.openservices.tablestore.tunnel.worker.ProcessRecordsInput;
import com.alicloud.openservices.tablestore.tunnel.worker.TunnelWorker;
import com.alicloud.openservices.tablestore.tunnel.worker.TunnelWorkerConfig;
public class TunnelQuickStart {
    private static class SimpleProcessor implements IChannelProcessor {
        @Override
        public void process(ProcessRecordsInput input) {
            System.out.println("Default record processor, would print records count");
            System.out.println(
                // NextToken is used to paginate the data of TunnelClient. 
                String.format("Process %d records, NextToken: %s", input.getRecords().size(), input.getNextToken()));
            try {
                // Simulate the processing of data consumption. 
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        @Override
        public void shutdown() {
            System.out.println("Mock shutdown");
        }
    }
    public static void main(String[] args) throws Exception {
        //1.Initialize a TunnelClient instance. 
        final String endPoint = "";
        final String accessKeyId = "";
        final String accessKeySecret = "";
        final String instanceName = "";
        TunnelClient tunnelClient = new TunnelClient(endPoint, accessKeyId, accessKeySecret, instanceName);
        //2.Create a tunnel. You must create a table before you create a tunnel. To create a table, you must use the createTable method in SyncClient or go to the Tablestore console. 
        final String tableName = "testTable";
        final String tunnelName = "testTunnel";
        CreateTunnelRequest request = new CreateTunnelRequest(tableName, tunnelName, TunnelType.BaseAndStream);
        CreateTunnelResponse resp = tunnelClient.createTunnel(request);
        // Use tunnelId to initialize TunnelWorker. You can call the ListTunnel or DescribeTunnel operation to obtain the tunnel ID. 
        String tunnelId = resp.getTunnelId();
        System.out.println("Create Tunnel, Id: " + tunnelId);
        //3.Customize the data consumption callback to start automatic data consumption. 
        // TunnelWorkerConfig contains the larger number of advanced parameters. 
        TunnelWorkerConfig config = new TunnelWorkerConfig(new SimpleProcessor());
        TunnelWorker worker = new TunnelWorker(tunnelId, tunnelClient, config);
        try {
            worker.connectAndWorking();
        } catch (Exception e) {
            e.printStackTrace();
            worker.shutdown();
            tunnelClient.shutdown();
        }
    }
}
```

