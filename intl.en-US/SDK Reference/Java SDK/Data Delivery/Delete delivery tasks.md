Delete delivery tasks 
==========================================

You can call the DeleteDeliveryTask operation to delete a delivery task.

Parameters 
-------------------------------



| Parameter |          Description           |
|-----------|--------------------------------|
| tableName | The name of the table.         |
| taskName  | The name of the delivery task. |



Examples 
-----------------------------

    private static void deleteDeliveryTask(SyncClient client) {
        String tableName = "sampleTable";
        String taskName = "sampledeliverytask";
        DeleteDeliveryTaskRequest request = new DeleteDeliveryTaskRequest(tableName, taskName);
        DeleteDeliveryTaskResponse response = client.deleteDeliveryTask(request);
        System.out.println("resquestID: "+ response.getRequestId());
        System.out.println("traceID: " + response.getTraceId());
        System.out.println("delete task delivery success");
    }









