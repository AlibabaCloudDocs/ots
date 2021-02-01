Create delivery tasks 
==========================================

You can call the CreateDeliveryTask operation to create a delivery task. You can create a delivery task to deliver data from Tablestore to an OSS bucket.
**Notice**

Make sure that you have installed Tablestore SDK for Go that supports delivery service.

Prerequisites 
----------------------------------

* OSS is activated. A bucket is created in the region where a Tablestore instance is deployed. For more information, see [Activate OSS](/intl.en-US/Console User Guide/Sign up for OSS.md).

  

* The Tablestore service linked role (AliyunServiceRoleForOTSDataDelivery) is created in the console. The Alibaba Cloud Resource Name (ARN) of the role is recorded. For more information, see the "Create delivery tasks" section of the [Quick start](/intl.en-US/Function Introduction/Data Delivery/Quick start.md) topic.

  You can obtain the ARN of the Tablestore service linked role (AliyunServiceRoleForOTSDataDelivery) in the RAM console. Perform the following operations:

  On the **RAM Roles** page, search AliyunServiceRoleForOTSDataDelivery. Then, click the RAM role name. On the role details page, you can view and copy the ARN information of the role.

  ![fig_en_arn](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8415512161/p237199.png)
  

* TableStoreClient is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialize a Tablestore client.md).

  

* A table is created. Data is written to the table.

  




Parameters 
-------------------------------



| Parameter  |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TableName  | The name of the table.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| TaskName   | The name of the delivery task.  The name must be 3 to 16 characters in length and can contain only lowercase letters, digits, and hyphens (-). It must start and end with a lowercase letter or digit.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| TaskConfig | The configurations of the delivery task. Valid values: * OssPrefix: the prefix of the folder in the bucket. Data is delivered from Tablestore to the folder. The path of the destination folder supports the following time variables: $yyyy, $MM, $dd, $HH, and $mm. * When the path uses time variables for delivery, OSS folders are dynamically generated based on the time when data is written. This way, data is partitioned based on the naming conventions that are followed when Hive partitions data. Objects in OSS are organized, partitioned, and distributed based on time.   * When the path does not use time variables, all files are delivered to an OSS folder whose name contains a specific prefix.     * OssBucket: the name of the OSS bucket.   * OssEndpoint: the endpoint of the region where an OSS bucket is deployed.   * OssRoleName: the ARN of the Tablestore service linked role.   * Format: the format of the delivered data. The delivered data is stored in the Parquet format. By default, delivery service uses PLAIN to encode data of any type.   * EventTimeColumn: the event time column. This parameter specifies that data is partitioned based on the time of a column. If you do not specify this parameter, data is partitioned based on the time when the data is written to Tablestore.   * Schema: specifies the column you want to deliver. You must configure the source fields, destination fields, and destination field types to deliver the column. The order in which fields are sorted in Tablestore can be different from the order of fields in the schema. Parquet data stored in OSS is distributed based on the order of fields in the schema. **Notice** The data types must be consistent between the source and destination fields. If the data types between the fields are not consistent, the fields are discarded as dirty data. For more information about field type mappings, see the "Data type mappings" section of the [Quick start](/intl.en-US/Function Introduction/Data Delivery/Quick start.md) topic.    |
| TaskType   | The mode in which to deliver data. Default value: BaseIncTask. Valid values: * IncTask: the incremental data delivery mode. Only incremental data is synchronized.   * BaseTask: the full data delivery mode. All data in the table is scanned and synchronized.   * BaseIncTask: the differential data delivery mode. After the full data is synchronized, Tablestore synchronizes the incremental data. When you synchronize data in incremental mode, you can view the time when data is last delivered and the status of the current delivery task.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |



Examples 
-----------------------------

    func CreateTaskSample(client *tablestore.TableStoreClient) {
     createTask := &tablestore.CreateDeliveryTaskRequest{
      TableName: "sampleTable",
      TaskName: "sampledeliverytask",
      TaskType: tablestore.BaseIncTask,
      TaskConfig: &tablestore.OSSTaskConfig{
       OssPrefix:   "sample/year=$yyyy/month=$MM",
       OssBucket:      "datadeliverytest",
       OssEndpoint:    "oss-cn-hangzhou.aliyuncs.com",
       OssRoleName:    "acs:ram::17************45:role/aliyunserviceroleforotsdatadelivery",
       Schema: []*tablestore.TaskSchema{
        {
         ColumnName: "PK1",
         OssColumnName: "PK1",
         Type: tablestore.ParquetInt64,
        },
        {
         ColumnName: "PK2",
         OssColumnName: "PK2",
         Type: tablestore.ParquetUtf8,
        },
        {
         ColumnName: "Col1",
         OssColumnName: "Col1",
         Type: tablestore.ParquetDouble,
        },
    
    
       },
      },
     }
     createResp, err := client.CreateDeliveryTask(createTask)
     if err ! = nil {
      log.Fatal("create delivery task failed ", err)
     }
     fmt.Println("create delivery task success ", createResp.RequestId)
    }





