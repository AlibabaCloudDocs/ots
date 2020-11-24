# Quick start

Tablestore SDK for Go allows you to use Tunnel Service. Before you use Tunnel Service, you must understand the usage notes.

## Usage notes

-   By default, TunnelWorkerConfig starts the thread pool to read and process data. A single server starts multiple TunnelWorkers. We recommend that TunnelWorkers share the same TunnelWorkerConfig.
-   When you create a differential tunnel to consume full and incremental data, the tunnel retains incremental logs for a maximum of seven days. The specific expiration time of incremental logs is the same as that of incremental data for a table. If the tunnel does not consume full data within seven days, the OTSTunnelExpired error occurs when the tunnel starts to consume incremental data. As a result, the tunnel cannot consume incremental data. If you estimate that the tunnel cannot consume full data within seven days, submit a ticket or join DingTalk group 23307953 to request technical support.
-   TunnelWorker requires warm-up time during initialization. The HeartbeatInterval parameter in TunnelWorkerConfig determines the warm-up time. You can use the time method in TunnelWorkerConfig to set this parameter. The default value is 30s. The minimum value is 5s.
-   When the mode switches from the full channel to the incremental channel, the full channel is closed and the incremental channel is started. This process requires the time for initialization. The HeartbeatInterval parameter determines the initialization time.
-   When the TunnelWorker client is abnormally shut down due to an exceptional exit or manual termination, TunnelWorker automatically recycles resources in the following ways: 1. Release the thread pool. 2. Automatically call the shutdown method that you have registered for the Channel class. 3. Shut down the tunnel.

## Experience Tunnel Service

1.  Initialize a Tunnel client instance.

    ```
    // Set endpoint to the endpoint of the Tablestore instance. Example: https://instance.cn-hangzhou.ots.aliyun.com.
    // Set the name of the instance.
    // Set accessKeyId and accessKeySecret to the AccessKey ID and AccessKey secret used to access Tablestore.
    tunnelClient := tunnel.NewTunnelClient(endpoint, instance,
       accessKeyId, accessKeySecret)                    
    ```

2.  Create a tunnel.

    ```
    req := &tunnel.CreateTunnelRequest{
       TableName:  "testTable",
       TunnelName: "testTunnel",
       Type:       tunnel.TunnelTypeBaseStream, // Create the BaseStream tunnel.
    }
    resp, err := tunnelClient.CreateTunnel(req)
    if err != nil {
       log.Fatal("create test tunnel failed", err)
    }
    log.Println("tunnel id is", resp.TunnelId)
    ```

3.  Customize the data consumption callback function to start automatic data consumption.

    ```
    // Customize the data consumption callback function.
    func exampleConsumeFunction(ctx *tunnel.ChannelContext, records []*tunnel.Record) error {
        fmt.Println("user-defined information", ctx.CustomValue)
        for _, rec := range records {
            fmt.Println("tunnel record detail:", rec.String())
        }
        fmt.Println("a round of records consumption finished")
        return nil
    }
    
    // Configure the callback function. Information about the callback function is passed to the SimpleProcessFactory operation. Configure the TunnelWorkerConfig operation.
    workConfig := &tunnel.TunnelWorkerConfig{
       ProcessorFactory: &tunnel.SimpleProcessFactory{
          CustomValue: "user custom interface{} value",
          ProcessFunc: exampleConsumeFunction,
       },
    }
    
    // Use TunnelDaemon to continuously consume the specified tunnel.
    daemon := tunnel.NewTunnelDaemon(tunnelClient, tunnelId, workConfig)
    log.Fatal(daemon.Run())
    ```


