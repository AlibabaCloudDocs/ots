# SDK usage

You can use SDKs to use Tunnel Service. Before you use SDKs to use Tunnel Service, you must know the usage notes and API operations of Tunnel Service.

## Usage notes

When you use Tunnel Service, note the following items:

-   By default, TunnelWorkerConfig starts the thread pool to read and process data. A single server starts multiple TunnelWorkers. We recommend that TunnelWorkers share the same TunnelWorkerConfig.
-   When you create a differential tunnel to consume full and incremental data, the tunnel retains incremental logs for a maximum of seven days. The specific expiration time of incremental logs is the same as that of incremental data for a table. If the tunnel does not consume full data within seven days, the OTSTunnelExpired error occurs when the tunnel starts to consume incremental data. As a result, the tunnel cannot consume incremental data. If you estimate that the tunnel cannot consume full data within seven days, submit a ticket or join DingTalk group 23307953 to request technical support.
-   TunnelWorker requires warm-up time during initialization. The heartbeatIntervalInSec parameter in TunnelWorkerConfig determines the warm-up time. You can use the setHeartbeatIntervalInSec method in TunnelWorkerConfig to set this parameter. The default value is 30s. The minimum value is 5s.
-   When the mode switches from the full channel to the incremental channel, the full channel is closed and the incremental channel is started. This process requires the time for initialization. The heartbeatIntervalInSec parameter determines the initialization time.
-   When the TunnelWorker client is abnormally shut down due to an exceptional exit or manual termination, TunnelWorker automatically recycles resources in the following ways: 1. Release the thread pool. 2. Automatically call the shutdown method that you have registered for the Channel class. 3. Shut down the tunnel.

## API operations

|API operation|Description|
|-------------|-----------|
|CreateTunnel|Creates a tunnel|
|ListTunnel|Lists the information about the tunnels in a table.|
|DescribeTunnel|Describes the information about the channels in a specified tunnel.|
|DeleteTunnel|Deletes a tunnel.|

## Usage

You can use the SDKs in the following programming languages to implement Tunnel Service:

-   [Java](/intl.en-US/SDK Reference/Java SDK/Tunnel service/Overview.md)
-   [Go](/intl.en-US/SDK Reference/Go SDK/Tunnel Service/Install Tunnel Service.md)

## Experience Tunnel Service

Tablestore SDK for Java allows you to use Tunnel Service.

1.  Initialize a TunnelClient instance.

    ```
    // Set endPoint to the endpoint of the Tablestore instance. Example: https://instance.cn-hangzhou.ots.aliyuncs.com.
    // Set accessKeyId and accessKeySecret to the AccessKey ID and AccessKey secret used to access Tablestore.
    // Set instanceName to the name of the instance.
    final String endPoint = "";
    final String accessKeyId = "";
    final String accessKeySecret = "";
    final String instanceName = "";
    TunnelClient tunnelClient = new TunnelClient(endPoint, accessKeyId, accessKeySecret, instanceName);
    ```

2.  Create a tunnel.

    Create a table for testing or prepare an existing table before you create the tunnel. To create a table for testing, you can use the createTable method in the SyncClient class or go to the Tablestore console.

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
    // Customize the data consumption callback or call the IChannelProcessor operation. Specify the process and shutdown methods.
    private static class SimpleProcessor implements IChannelProcessor {
        @Override
        public void process(ProcessRecordsInput input) {
            // ProcessRecordsInput includes the data that you have obtained.
            System.out.println("Default record processor, would print records count");
            System.out.println(
                // NextToken is used to page the data of Tunnel Client.
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
    
    // By default, TunnelWorkerConfig starts the thread pool to read and process data. A single server starts multiple TunnelWorkers.
    // We recommend that you share the same TunnelWorkerConfig. TunnelWorkerConfig provides more advanced parameters.
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

TunnelWorkerConfig allows you to customize tunnel clients. The following table describes the parameters that you can configure. You can configure the parameters based on your requirements.

|Item|Parameter|Description|
|----|---------|-----------|
|Configure the interval to detect heartbeats and the timeout period to receive heartbeats.|heartbeatTimeoutInSec|The timeout period to receive heartbeats. Default value: 300. Unit: seconds.

When a heartbeat timeout occurs, the tunnel server considers that the current tunnel client is unavailable. The tunnel client must try to connect to the tunnel server again. |
|heartbeatIntervalInSec|The interval to detect heartbeats. You can detect heartbeats to detect active channels, update the status of channels, and automatically initialize data processing tasks.

Default value: 30. Minimum value: 5. Unit: seconds. |
|Interval between checkpoints|checkpointIntervalInMillis|The interval between checkpoints when data was consumed. The interval is recorded on the tunnel server. Default value: 5000. Unit: ms.

**Note:**

-   Data to read is stored in different servers. Various errors may occur when you run processes. For example, the server may restart due to environmental factors. The tunnel server regularly records checkpoints after data is processed. A task processes data from the last checkpoint after the task is restarted. In exceptional conditions, Tunnel Service may sequentially synchronize data once or multiple times. If some data is reprocessed, check the business processing logic.
-   To prevent data being reprocessed in the case of errors, record more checkpoints. However, too many checkpoints may compromise the system throughput. We recommend that you record the checkpoints as needed. |
|The client tag|clientTag|The custom client tag that is used to generate a tunnel client ID. You can customize this parameter to uniquely identify TunnelWorkers.|
|The custom callback to process data|channelProcessor|The callback that you register to process data, including the process and shutdown methods.|
|The configuration of the thread pool to read and process data|readRecordsExecutor|The thread pool to read data. Use the default configuration if there are no special requirements.|
|processRecordsExecutor|The thread pool to process data. Use the default configuration if there are no special requirements. **Note:**

-   When you customize the thread pool, we recommend that the number of threads in the pool and the number of the channels in the tunnel be the same. This way, each channel can be quickly allocated with compute resources such as CPU.
-   The following operations are performed for default configurations to ensure throughput:
    -   Allocate 32 core threads in advance to guarantee real-time throughput when there is a small amount of data or a small number of channels.
    -   Reduce the queue length when there is a large amount of data to process or when there is a large number of channels. This way, the policy is triggered to create a thread in the pool and quickly allocate more compute resources.
    -   Set the thread keep-alive time to 60s. When the data to process is reduced, thread resources can be recycled. |
|Memory control|maxChannelParallel|The concurrency level of channels to read and process data for memory control. The default value is -1, indicating that the concurrency level is not limited.

**Note:** This configuration applies to Tablestore SDK for Java V5.10.0 and later. |
|Maximum backoff time|maxRetryIntervalInMillis|The reference value to calculate the maximum backoff time for the tunnel. The maximum backoff time is random around the reference value. The valid value of the maximum backoff time is in the following range: 0.75 x maxRetryIntervalInMillis to 1.25 x maxRetryIntervalInMillis. Default value: 2000 ms. Minimum value: 200 ms.

**Note:**

-   This configuration applies to Tablestore SDK for Java V5.4.0 and later.
-   When the amount of data to process is smaller than 900 KB or 500 pieces for each export, the tunnel client uses exponential backoff for the tunnel until the maximum backoff time is exceeded. |

## Appendix: Complete code

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
                // NextToken is used to page the data of Tunnel Client.
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
        //1. Initialize a TunnelClient instance.
        final String endPoint = "";
        final String accessKeyId = "";
        final String accessKeySecret = "";
        final String instanceName = "";
        TunnelClient tunnelClient = new TunnelClient(endPoint, accessKeyId, accessKeySecret, instanceName);
        //2. Create a tunnel. You must use the createTable method in SyncClient or go to the Tablestore console to create a table for testing in advance.
        final String tableName = "testTable";
        final String tunnelName = "testTunnel";
        CreateTunnelRequest request = new CreateTunnelRequest(tableName, tunnelName, TunnelType.BaseAndStream);
        CreateTunnelResponse resp = tunnelClient.createTunnel(request);
        // Use tunnelId to initialize TunnelWorker. You can call the ListTunnel or DescribeTunnel operation to obtain the tunnel ID.
        String tunnelId = resp.getTunnelId();
        System.out.println("Create Tunnel, Id: " + tunnelId);
        //3. Customize the data consumption callback to start automatic data consumption.
        // TunnelWorkerConfig contains more advanced parameters.
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

