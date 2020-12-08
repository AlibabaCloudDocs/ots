Use SDKs 
=============================

Before you use delivery service by using SDKs to deliver data, you must understand the precautions and operations. You can create a delivery task in the Tablestore console to deliver data from Tablestore to an OSS bucket.

Usage notes 
--------------------------------

* Data delivery is available in the China (Hangzhou), China (Shanghai), China (Beijing), and China (Zhangjiakou) regions.

  

* The delete operation on Tablestore data is ignored when the data is delivered. Tablestore data which has delivered to OSS is not deleted when you perform a delete operation on the data.

  

* It takes at most one minute for initialization when you create a delivery task.

  

* Latencies are within three minutes when data is written at a steady rate. P99 is within 10 minutes when data is synchronized.

  **Note**

  P99 indicates the average latency of the slowest 1% of requests over the last 10 seconds.
  




API operations 
-----------------------------------




|    API operation     |                       Description                       |
|----------------------|---------------------------------------------------------|
| CreateDeliveryTask   | Creates a delivery task.                                |
| ListDeliveryTask     | Lists all delivery task information in a table.         |
| DescribeDeliveryTask | Queries the descriptive information of a delivery task. |
| DeleteDeliveryTask   | Deletes a delivery task.                                |



Usage notes 
--------------------------------

You can use the following Tablestore SDKs to implement delivery service:

* [Java SDK]()

  

* [Go SDK]()

  




Parameters 
-------------------------------



| Parameter  |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| tableName  | The name of the table.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| taskName   | The name of the delivery task.  The name must be 3 to 16 characters in length and can contain only lowercase letters, digits, and hyphens (-). It must start and end with a lowercase letter or digit.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| taskConfig | The configurations of the delivery task. Valid values: * ossPrefix: the prefix of the folder in the bucket. Data is delivered from Tablestore to the folder. The path of the destination folder supports the following time variables: $yyyy, $MM, $dd, $HH, and $mm. * When the path uses time variables for delivery, OSS folders are dynamically generated based on the time when data is written. This way, data is partitioned based on the naming conventions that are followed when Hive partitions data. Objects in OSS are organized, partitioned, and distributed based on time.   * When the path does not use time variables, all files are delivered to an OSS folder whose name contains a specific prefix.     * ossBucket: the name of the OSS bucket.   * ossEndpoint: the service address of the region where an OSS bucket is deployed.   * ossStsRole: the Alibaba Cloud Resource Name (ARN) of the Tablestore service linked role.   * format: the format of the delivered data. The delivered data is stored in the Parquet format. By default, delivery service uses PLAIN to encode data of any type.   * eventTimeColumn: the event time column. This parameter specifies that data is partitioned based on the time of a column. If you do not specify this parameter, data is partitioned based on the time when the data is written to Tablestore.   * parquetSchema: specifies the column you want to delivery. You must configure the source fields, destination fields, and destination field types to delivery. The order in which fields are sorted in Tablestore can be different from the order of fields in the schema. Parquet data stored in OSS is distributed based on the order of fields in the schema. **Notice** The data types must be consistent between the source and destination fields. If the data types between the fields are not consistent, the fields are discarded as dirty data. For more information about field type mappings, see the "Data type mappings" section of the [Quick start](/intl.en-US/Function Introduction/Data Delivery/Quick start.md) topic.    |
| taskType   | The mode in which to deliver data. Default value: BASE_INC. Valid values:  * INC: the incremental data delivery mode. Only incremental data is synchronized.   * BASE: the full data delivery mode. All data in tables is scanned and synchronized.   * BASE_INC: the differential data delivery mode. After the full data is synchronized, Tablestore synchronizes the incremental data. When you synchronize data in incremental mode, you can view the time when data is last delivered and the status of the current delivery task.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |



Examples 
-----------------------------

    import com.alicloud.openservices.tablestore.ClientException;
    import com.alicloud.openservices.tablestore.SyncClient;
    import com.alicloud.openservices.tablestore.TableStoreException;
    import com.alicloud.openservices.tablestore.model.delivery.*;
    public class DeliveryTask {
    
            public static void main(String[] args) {
                final String endPoint = "https://yourinstancename.cn-hangzhou.ots.aliyuncs.com";
    
                final String accessKeyId = "LT********************g5";
    
                final String accessKeySecret = "Er**************************Yc";
    
                final String instanceName = "yourinstancename";
    
                SyncClient client = new SyncClient(endPoint, accessKeyId, accessKeySecret, instanceName);
                try {
                    createDeliveryTask(client);
                    System.out.println("end");
                } catch (TableStoreException e) {
                    System.err.println("The operation failed. Details:" + e.getMessage() + e.getErrorCode() + e.toString());
                    System.err.println("Request ID:" + e.getRequestId());
                } catch (ClientException e) {
                    System.err.println("The request failed. Details:" + e.getMessage());
                } finally {
                    client.shutdown();
                }
            }
    
            private static void createDeliveryTask(SyncClient client){
                String tableName = "sampleTable";
                String taskName = "sampledeliverytask";
                OSSTaskConfig taskConfig = new OSSTaskConfig();
                taskConfig.setOssPrefix("sampledeliverytask/year=$yyyy/month=$MM");
                taskConfig.setOssBucket("datadeliverytest");
                taskConfig.setOssEndpoint("oss-cn-hangzhou.aliyuncs.com");
                taskConfig.setOssStsRole("acs:ram::17************45:role/aliyunserviceroleforotsdatadelivery");
                // eventColumn is optional. This parameter specifies that data is partitioned based on the time of a column. If you do not specify this parameter, data is partitioned based on the time when the data is written to Tablestore.
                EventColumn eventColumn = new EventColumn("Col1", EventTimeFormat.RFC1123);
                taskConfig.setEventTimeColumn(eventColumn);
                taskConfig.addParquetSchema(new ParquetSchema("PK1", "PK1", DataType.UTF8));
                taskConfig.addParquetSchema(new ParquetSchema("PK2", "PK2", DataType.BOOL));
                taskConfig.addParquetSchema(new ParquetSchema("Col1", "Col1", DataType.UTF8));
                CreateDeliveryTaskRequest request = new CreateDeliveryTaskRequest();
                request.setTableName(tableName);
                request.setTaskName(taskName);
                request.setTaskConfig(taskConfig);
                request.setTaskType(DeliveryTaskType.BASE_INC);
                CreateDeliveryTaskResponse response = client.createDeliveryTask(request);
                System.out.println("resquestID: "+ response.getRequestId());
                System.out.println("traceID: " + response.getTraceId());
                System.out.println("create delivery task success");
            }
    }



