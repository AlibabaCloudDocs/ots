# Create tunnels

You can call the CreateTunnel operation to create one or more tunnels for a table. When you create a tunnel, you must specify the table name, tunnel name, and tunnel type.

## Request parameters

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table for which you want to create a tunnel.|
|tunnelName|The tunnel name.|
|tunnelType|The tunnel type. Valid values:-   BaseData: Only full data is consumed and processed.
-   Stream: Only incremental data is consumed and processed.
-   BaseAndStream: After full data is consumed and processed, incremental data is consumed and processed. |

## Response parameters

|Parameter|Description|
|---------|-----------|
|TunnelId|The tunnel ID.|
|ResponseInfo|Some other fields returned.|
|RequestId|The ID of the request.|

## Examples

-   Example 1

    The following code provides an example on how to create a tunnel of the BaseData type:

    ```
    // You can create three types of tunnels: TunnelType.BaseData, TunnelType.Stream, and TunnelType.BaseAndStream.
    // In this example, a BaseData tunnel is created. To create other types of tunnels, set TunnelType in CreateTunnelRequest to the required types.
    private static void createTunnel(TunnelClient client, String tunnelName) {
        CreateTunnelRequest request = new CreateTunnelRequest(TableName, tunnelName, TunnelType.BaseData);
        CreateTunnelResponse resp = client.createTunnel(request);
        System.out.println("RequestId: " + resp.getRequestId());
        System.out.println("TunnelId: " + resp.getTunnelId());
    }
    ```

-   Example 2

    The following code provides an example on how to create a tunnel of the Stream or BaseAndStream type and how to specify the time range to read incremental data:

    ```
    // Create a tunnel of the Stream or BaseAndStream type. Specify the start timestamp or end timestamp to read incremental data within a specified time range. The configurations specified by StreamTunnelConfig do not take effect for BaseData tunnels.
    private static void createStreamTunnelByOffset(TunnelClient client,String tableName,String tunnelName){
        CreateTunnelRequest createTunnelRequest = new CreateTunnelRequest(tableName,tunnelName, TunnelType.Stream);// Create a tunnel of the Stream type.
        //CreateTunnelRequest createTunnelRequest = new CreateTunnelRequest(tableName,tunnelName, TunnelType.BaseAndStream);// Create a tunnel of the BaseAndStream type.
        StreamTunnelConfig streamTunnelConfig = new StreamTunnelConfig();  
        /*
            Specify the start timestamp and end timestamp to read incremental data. Unit: milliseconds. Valid values: [CurrentSystemTime - StreamExpiration + 5 minute, CurrentSystemTime).
            CurrentSystemTime is a timestamp that indicates the current system time in milliseconds. StreamExpiration is a timestamp of the time when the incremental logs expire in milliseconds. The maximum validity period of incremental logs is seven days. You can set StreamExpiration in the attributes of the table.
            The value of endTime must be greater than that of startTime.
         */
        streamTunnelConfig.setStartOffset(startTime);
        streamTunnelConfig.setEndOffset(endTime);
        createTunnelRequest.setStreamTunnelConfig(streamTunnelConfig);
        CreateTunnelResponse resp = client.createTunnel(createTunnelRequest);
        System.out.println("RequestId: " + resp.getRequestId());
        System.out.println("TunnelId: " + resp.getTunnelId());
    }
    ```


