# Synchronize data from one table to another table

This topic describes how to synchronize data from one table to another table by using Tunnel Service, DataWorks, or DataX.

## Prerequisites

The destination table is created. For more information, see [Create tables](/intl.en-US/Quick Start/Create tables.md).

**Note:** The destination table must contain the columns you want to synchronize from the source table.

## Use Tunnel Service to synchronize data

After the tunnel of the source table is created, you can use Tablestore SDK to synchronize data from the source table to the destination table. You can customize logic to process data for the business during synchronization.

1.  Create the tunnel of the source table in the Tablestore console or by using Tablestore SDK. Record the tunnel ID. For more information, see [Quick start](/intl.en-US/Function Introduction/Tunnel service/Quick start.md) or [SDK usage](/intl.en-US/Function Introduction/Tunnel service/SDK usage.md).
2.  Synchronize data by using Tablestore SDK.

    The following code provides an example on how to synchronize data by using Tablestore SDK:

    ```
    public class TunnelTest {
    
        public static void main(String[] args){
           TunnelClient tunnelClient = new TunnelClient("endpoint",
                   "accessKeyId","accessKeySecret","instanceName");
    
            TunnelWorkerConfig config = new TunnelWorkerConfig(new SimpleProcessor());
    
            // You can view the tunnel ID on the Tunnels tab of the console or query the tunnel ID by calling describeTunnelRequest.
            TunnelWorker worker = new TunnelWorker("tunnelId", tunnelClient, config);
            try {
                worker.connectAndWorking();
            } catch (Exception e) {
                e.printStackTrace();
                worker.shutdown();
                tunnelClient.shutdown();
            }
        }
    
        public static class SimpleProcessor implements IChannelProcessor{
        
           // Connect the tunnel to the destination table.
           TunnelClient tunnelClient = new TunnelClient("endpoint",
                   "accessKeyId","accessKeySecret","instanceName");
                   
           @Override
            public void process(ProcessRecordsInput processRecordsInput) {
            
                // Incremental or full data is returned in ProcessRecordsInput.
                List<StreamRecord> list = processRecordsInput.getRecords();
                for(StreamRecord streamRecord : list){
                    switch (streamRecord.getRecordType()){
                        case PUT:
                            // Customize the logic to process data for the business.
                            //putRow
                            break;
                        case UPDATE:
                            //updateRow
                            break;
                        case DELETE:
                            //deleteRow
                            break;
                    }
    
                    System.out.println(streamRecord.toString());
                }
            }
    
            @Override
            public void shutdown() {
                
            }
        }
    }
    ```


## Use DataWorks or DataX to synchronize data

You can use DataWorks or DataX to synchronize data from the source table to the destination table. This section describes how to synchronize data by using DataWorks.

1.  Add data sources of Tablestore

    Add the Tablestore instances of the source table and the destination table as data sources. For more information, see [Add a connection]().

2.  Create a synchronization task node.
    1.  Log on to the [DataWorks console](https://workbench.data.aliyun.com/console) as the project administrator.

        **Note:** Only the project administrator role can add data sources. Members who assume other roles can only view data sources.

    2.  Select a region. In the left-side navigation pane, click Workspaces.
    3.  On the Workspaces page, find the workspace and click **Data Analytics** in the Actions column.
    4.  On the Data Analytics page of the DataStudio console, click **Business Flow** and select a business flow.

        For more information about how to create a business flow, see [Create a workflow]().

    5.  Right-click **Data Integration** and choose **Create** \> **Batch synchronization**.
    6.  In the Create Node dialog box, enter a node name in the **Node Name** field.
3.  Configure the data source.
    1.  Click **Data Integration**. Double-click the name of the node for the data synchronization task.
    2.  On the editing page for the node of data synchronization, find the Connections section and set the Source connection and the Target connection.

        Set **Connection type** to **OTS** for both the **Source** and **Target** connections. Then, select connections for Source and Target. Click the ![script](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5815446061/p127620.png) icon or the **Switch to Code Editor** icon to configure a script.

        **Note:** Tableastore supports only the script mode. For more information about how to configure scripts, see [Configure Tableastore Reader]() and [Configure Tableastore Writer]().

    3.  Click the ![save](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5815446061/p127623.png) icon to save the data source configurations.
4.  Run the synchronization task.
    1.  Click the ![start](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5815446061/p127635.png) icon.
    2.  In the Arguments dialog box, select the resource group for scheduling.
    3.  Click **OK** to run the task.

        After the task is completed, you can check whether the task is successful and view the number of exported rows on the Runtime Log tab.

5.  \(Optional\) Execute the synchronization task at the scheduled time. For more information, see [Configure recurrence and dependencies for a node]().

