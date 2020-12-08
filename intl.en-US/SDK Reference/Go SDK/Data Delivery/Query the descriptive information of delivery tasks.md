Query the descriptive information of delivery tasks 
========================================================================

You can call the DescribeDeliveryTask operation to query the descriptive information of a delivery task.

Prerequisites 
----------------------------------

* The TableStoreClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).

  

* The delivery task is created. For more information about how to use the console or the SDK, see [Quick start](/intl.en-US/Function Introduction/Data Delivery/Quick start.md) or [Create delivery tasks](/intl.en-US/SDK Reference/Go SDK/Data Delivery/Create delivery tasks.md).

  




Request parameters 
---------------------------------------



| Parameter |          Description           |
|-----------|--------------------------------|
| TableName | The name of the table.         |
| TaskName  | The name of the delivery task. |



Response parameters 
----------------------------------------



|  Parameter   |                                                                                                                                                  Description                                                                                                                                                  |
|--------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TaskConfig   | The configuration information of the delivery task.                                                                                                                                                                                                                                                           |
| TaskSyncStat | The synchronization status of the delivery task.                                                                                                                                                                                                                                                              |
| TaskType     | The mode in which to deliver data. Valid values: * 0: the full data delivery mode   * 1: the incremental data delivery mode   * 2: the differential data delivery mode    |



Examples 
-----------------------------

    func DescribeTaskSample(client *tablestore.TableStoreClient, tableName, taskName string) {
     req := &tablestore.DescribeDeliveryTaskRequest{
      TableName: tableName,
      TaskName: taskName,
     }
     resp, err := client.DescribeDeliveryTask(req)
     if err != nil {
      log.Fatal("describe delivery task failed ", err)
     }
     fmt.Println("TaskConfig: ", *resp.TaskConfig)
     fmt.Println("TaskSyncStat: ", *resp.TaskSyncStat)
     fmt.Println("TaskType: ", resp.TaskType)
     return
    }







