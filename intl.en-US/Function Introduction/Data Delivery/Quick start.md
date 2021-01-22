Quick start 
================================

You can create a delivery task in the Tablestore console to deliver data from Tablestore to an OSS bucket.

Prerequisites 
----------------------------------

OSS is activated. A bucket is created in the region where a Tablestore instance is located. For more information, see [Activate OSS](/intl.en-US/Console User Guide/Sign up for OSS.md).
**Note**

Data delivery allows you to deliver data from a Tablestore instance to an OSS bucket within the same region. To deliver data to another warehouse such as MaxCompute,[submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd).

Usage notes 
--------------------------------

* Data delivery is available in the China (Hangzhou), China (Shanghai), China (Beijing), and China (Zhangjiakou) regions.

  

* The delete operation on Tablestore data is ignored when the data is delivered. Tablestore data delivered to OSS is not deleted when you perform a delete operation on the data.

  

* It takes at most one minute for initialization when you create a delivery task.

  

* Latencies are within 3 minutes when data is written at a steady rate. P99 is within 10 minutes when data is synchronized.

  **Note**

  P99 indicates the average latency of the slowest 1% of requests over the last 10 seconds.
  




Create delivery tasks 
------------------------------------------

1. Log on to the [Tablestore console](https://otsnext.console.aliyun.com/).

   

2. Select a region. Click an instance name or click **Manage Instance** in the **Actions** column corresponding to the instance.

   

3. On the **Manage Instance** page, click the **Deliver Data to OSS** tab.

   

4. (Optional) Create the AliyunServiceRoleForOTSDataDelivery role.

   When you configure data delivery for the first time, you must create the AliyunServiceRoleForOTSDataDelivery role that is used to authorize Tablestore to write data to an OSS bucket. For more information about specific operations, see [AliyunServiceRoleForOTSDataDelivery role](/intl.en-US/Function Introduction/Authorization management/AliyunServiceRoleForOTSDataDelivery role.md).
   **Note**

   For more information about service-linked roles, see [AliyunServiceRoleForOTSDataDelivery role](/intl.en-US/RAM Role Management/Service-linked roles.md).
   1. On the **Deliver Data to OSS** tab, click **Role for Delivery Service** .

      
   
   2. In the **Role Details** message, view related information. Click **OK** .

      
   

   

5. Create a delivery task.

   1. On the **Deliver Data to OSS** tab, click **Create Task** .

      
   
   2. In the **Create Task** dialog box, configure the following parameters.

      

      |         Parameter         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
      |---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
      | Task Name                 | The name of the task. The name must be 3 to 16 characters in length can contain only lowercase letters, digits, and hyphens (-). It must start and end with a lowercase letter or digit.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
      | Destination Region        | The region where the Tablestore instance and OSS bucket are located.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      | Source Table              | The name of the Tablestore table.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
      | Destination Bucket        | The name of the OSS bucket. **Notice** The region where the bucket is located must be the same as that of the Tablestore instance.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      | Destination Prefix        | The prefix of the folder in the bucket. Data is delivered from Tablestore to the folder. The path of the destination folder supports the following time variables: $yyyy, $MM, $dd, $HH, and $mm. For more information, see [Partition data by time](#section-k5v-k1t-9rm). * When the path uses time variables for delivery, OSS folders are dynamically generated based on the time when data is written. This way, data is partitioned based on the naming conventions followed when Hive partitions data. Objects in OSS are organized, partitioned, and distributed based on time.   * When the path does not use time variables, all files are delivered to an OSS folder whose name contains a specific prefix.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
      | Synchronization Mode      | The mode in which to deliver data. The following options are available: * Incremental: Only incremental data is synchronized.   * Full: All data in tables is scanned and synchronized.   * Differential: Full data is synchronized before incremental data is synchronized.    When you synchronize data in incremental mode, you can view the time when data is last delivered and the status of the current delivery task.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
      | Destination Object Format | The delivered data is stored in the Parquet format. By default, data delivery uses PLAIN for encoding. PLAIN can be used to encode data of any type.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      | Schema Generation Type    | Specify the columns for delivery. The order in which fields are sorted in the Tablestore table can be different from the order of fields in the schema. Parquet data stored in OSS is distributed based on the order of fields in the schema. Select a schema generation type. * If **Schema Generation Type:** is set to **Manual** , you must configure the source fields, destination field names, and destination field types for delivery.   * If **Schema Generation Type:** is set to **Auto Generate** , the system identifies and matches the fields for delivery.    **Notice** The data types must be consistent between the source and destination fields. Otherwise, the fields are discarded as dirty data. For more information about field type mappings, see [data format mapping](#section-gsg-8to-irz). When you configure the schema, you can perform the following operations: * Click **+Add Field** to add fields for delivery.   * Click the e![down001](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7523464061/p179830.png)or![up0001](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7523464061/p179831.png) icon in the **Actions** column corresponding to a field to adjust the order of the field.   * Click the ![delete](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7523464061/p169073.png) icon in the **Actions** column corresponding to a field to delete the field.    |
      | Schema Configurations     | Specify the columns for delivery. The order in which fields are sorted in the Tablestore table can be different from the order of fields in the schema. Parquet data stored in OSS is distributed based on the order of fields in the schema. Select a schema generation type. * If **Schema Generation Type:** is set to **Manual** , you must configure the source fields, destination field names, and destination field types for delivery.   * If **Schema Generation Type:** is set to **Auto Generate** , the system identifies and matches the fields for delivery.    **Notice** The data types must be consistent between the source and destination fields. Otherwise, the fields are discarded as dirty data. For more information about field type mappings, see [data format mapping](#section-gsg-8to-irz). When you configure the schema, you can perform the following operations: * Click **+Add Field** to add fields for delivery.   * Click the e![down001](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7523464061/p179830.png)or![up0001](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7523464061/p179831.png) icon in the **Actions** column corresponding to a field to adjust the order of the field.   * Click the ![delete](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7523464061/p169073.png) icon in the **Actions** column corresponding to a field to delete the field.    |

      
   

   

6. Click **OK** .

   In the **View Statement to Create Table** message, you can view the statement that is used to create an external table for Data Lake Analytics (DLA) and E-MapReduce (EMR). You can also copy the statement to create an external table for DLA and EMR to access data in OSS.

   After the delivery task is created, you can perform the following operations:
   * View the details of the delivery task, such as the task name, table name, destination bucket, destination prefix, status, and the time when data is last synchronized.

     
   
   * View or copy the statement to create a table.

     Click **View Statement to Create Table** in the **Actions** column. You can view or copy the statement to create an external table by using computing engines such as EMR. For more information about specific operations, see [Use EMR](/intl.en-US/Function Introduction/Data Delivery/Data lake-based computing and analysis/Use EMR.md).
     
   
   * View the error message returned after the delivery.

     If the configurations for the OSS bucket and delivery permissions are incorrect, data delivery cannot be complete. On the status page of the delivery task, you can view related error messages. For more information about exception handling, see [Exception handling](#section-xpm-pae-455).
     
   
   * Delete the delivery task.

     Click **Delete** in the **Actions** column corresponding to the delivery task. You can delete the delivery task. The system returns an error if the delivery task is in the initialization process. You can delete the task later.
     
   

   




View OSS data 
----------------------------------

After the delivery task is initialized and data is delivered, you can view the data delivered to OSS by using the OSS console, API, SDK, or computing engine EMR. For more information, see [Overview](/intl.en-US/Console User Guide/Upload, download, and manage objects/Overview.md).

Example of an OSS object URL:

    oss://BucketName/TaskPrefix/TaskName_ConcurrentID_TaskPrefix__SequenceID



In the example, BucketName indicates the name of the bucket. The first TaskPrefix indicates the prefix of the destination folder. The second TaskPrefix indicates the prefix information of the task. TaskName indicates the name of the task. ConcurrentID indicates the number for concurrency determined by the system. The number starts from 0 and increases when the throughput increases. SequenceID indicates the sequence ID of the delivered file and increases from 1.

Partition data by time 
-------------------------------------------

Data delivery allows the system to obtain the time when data is written to Tablestore. The time consists of the following variables: $yyyy (four-digit year), $MM (two-digit month), $dd (two-digit day), $HH (two-digit hour), and $mm (two-digit minute). The time can be used as the prefix of the destination bucket after conversion.
**Note**

We recommend that the size of an OSS object be at least 4 MB. When computing engines are used to load OSS data, the larger number of partitions results in longer time to load partitions. Therefore, in most real-time data writing scenarios, we recommend that the time based on which to partition data is at the granularity of day or hour, rather than minute.

The delivery task by which data was written to Tablestore at 16:03 on August 31, 2020 is used in the example. The following table describes the URLs of the first object generated in OSS based on different destination prefix configurations.


| OSS Bucket |               TaskName                |          Destination prefix           |                                                    OSS object URL                                                     |
|------------|---------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| myBucket   | testTask                              | myPrefix                              | oss://myBucket/myPrefix/testTask_0_myPrefix__1                                                                        |
| myBucket   | testTaskTimeParitioned                | myPrefix/$yyyy/$MM/$dd/$HH/$mm        | oss://myBucket/myPrefix/2020/08/31/16/03/testTaskTimeParitioned_0_myPrefix_2020_08_31_16_03__1                        |
| myBucket   | testTaskTimeParitionedHiveNamingStyle | myPrefix/year=$yyyy/month=$MM/day=$dd | oss://myBucket/myPrefix/year=2020/month=08/day=31/testTaskTimeParitionedHiveNamingStyle_0_myPrefix_year=2020_month=08 |
| myBucket   | testTaskDs                            | ds=$yyyy$MM$dd                        | oss://myBucket/ds=20200831/testTaskDs_0_ds=20200831__0                                                                |



Data type mappings 
---------------------------------------



| Parquet Logical Type | Data type in Tablestore |
|----------------------|-------------------------|
| Boolean              | Boolean                 |
| Int64                | Int64                   |
| Double               | Double                  |
| UTF8                 | String                  |



Exception handling 
---------------------------------------



|    Error code    |                        Cause                         |                                                                                                                                                                   Solution                                                                                                                                                                   |
|------------------|------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| UnAuthorized     | Tablestore is not authorized to deliver data to OSS. | Confirm whether the AliyunServiceRoleForOTSDataDelivery role exists. If the role does not exist, you must create a delivery task to trigger Tablestore to create the role.                                                                                                                                                   |
| InvalidOssBucket | The destination OSS bucket does not exist.           | * Confirm whether the OSS bucket and Tablestore instance are located within the same region.   * Confirm whether the OSS bucket exists.    When the OSS bucket is created, all data is written to the OSS bucket again. The delivery progress is updated. |


