List the names of delivery tasks 
=====================================================

You can call the ListDeliveryTask operation to list information about all deliver tasks for a table.

Prerequisites 
----------------------------------

* The TableStoreClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).

  

* The delivery task is created. For more information about how to use the console or the SDK, see [Quick start](/intl.en-US/Function Introduction/Data Delivery/Quick start.md) or [Create delivery tasks](/intl.en-US/SDK Reference/Go SDK/Data Delivery/Create delivery tasks.md).

  




Request parameters 
---------------------------------------



| Parameter |      Description       |
|-----------|------------------------|
| TableName | The name of the table. |



Response parameters 
----------------------------------------



| Parameter |                                                                                                                                                  Description                                                                                                                                                  |
|-----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TableName | The name of the table, which is the same as the name of the table in the request.                                                                                                                                                                                                                             |
| TaskName  | The name of the delivery task.                                                                                                                                                                                                                                                                                |
| TaskType  | The mode in which to deliver data. Valid values: * 0: the full data delivery mode   * 1: the incremental data delivery mode   * 2: the differential data delivery mode    |



Examples 
-----------------------------

    func ListTask(client *tablestore.TableStoreClient, tableName string) {
     resp, err := client.ListDeliveryTask(&tablestore.ListDeliveryTaskRequest{
      TableName: tableName,
     })
     if err != nil {
      log.Fatal("list delivery task failed ", err)
     }
     for _, task := range resp.Tasks {
      fmt.Println("task: ", task)
     }
     fmt.Println("list task finish")
    }


