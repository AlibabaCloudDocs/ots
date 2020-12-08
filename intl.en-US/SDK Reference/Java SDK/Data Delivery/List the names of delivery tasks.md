List the names of delivery tasks 
=====================================================

You can call the ListDeliveryTask operation to list information about all deliver tasks for a table.

Prerequisites 
----------------------------------

* The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialization.md).

  

* The delivery task is created. For more information about how to use the console or the SDK, see [Quick start](/intl.en-US/Function Introduction/Data Delivery/Quick start.md) or [Create delivery tasks](/intl.en-US/SDK Reference/Java SDK/Data Delivery/Create delivery tasks.md).

  




Request parameters 
---------------------------------------



| Parameter |      Description       |
|-----------|------------------------|
| tableName | The name of the table. |



Response parameters 
----------------------------------------



| Parameter |                                    Description                                    |
|-----------|-----------------------------------------------------------------------------------|
| tableName | The name of the table, which is the same as the name of the table in the request. |
| taskName  | The name of the delivery task.                                                    |
| taskType  | The type of the delivery task.                                                    |





Examples 
-----------------------------

    public static void listDeliveryTask(SyncClient client) {
        String tableName = "sampleTable";
        ListDeliveryTaskRequest request = new ListDeliveryTaskRequest(tableName);
        ListDeliveryTaskResponse response = client.listDeliveryTask(request);
        System.out.println("resquestID: "+ response.getRequestId());
        System.out.println("traceID: " + response.getTraceId());
        for(DeliveryTaskInfo taskInfo: response.getTaskInfos()) {
            System.out.println("tableName: " + taskInfo.getTableName());
            System.out.println("taskName: " + taskInfo.getTaskName());
            System.out.println("taskType: " + taskInfo.getTaskType().toString());
        }
    }





