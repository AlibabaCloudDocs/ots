Query the descriptive information of delivery tasks 
========================================================================

You can call the DescribeDeliveryTask operation to query the descriptive information of a delivery task.

Request parameters 
---------------------------------------



| Parameter |          Description           |
|-----------|--------------------------------|
| tableName | The name of the table.         |
| taskName  | The name of the delivery task. |



Response parameters 
----------------------------------------



|  Parameter   |                     Description                     |
|--------------|-----------------------------------------------------|
| taskConfig   | The configuration information of the delivery task. |
| taskSyncStat | The synchronization status of the delivery task.    |
| taskType     | The type of the delivery task.                      |



Examples 
-----------------------------

    public static void describeDeliveryTask(SyncClient client) {
        String tableName = "sampleTable";
        String taskName  = "sampledeliverytask";
        DescribeDeliveryTaskRequest request = new DescribeDeliveryTaskRequest(tableName, taskName);
        DescribeDeliveryTaskResponse response = client.describeDeliveryTask(request);
        System.out.println("resquestID: "+ response.getRequestId());
        System.out.println("traceID: " + response.getTraceId());
        System.out.println("OSSconfig: " + response.getTaskConfig());
        System.out.println("TaskSyncStat: " + response.getTaskSyncStat());
        System.out.println("taskType: " + response.getTaskType());
    }





