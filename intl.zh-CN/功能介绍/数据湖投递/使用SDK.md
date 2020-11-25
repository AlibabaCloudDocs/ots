使用SDK 
==========================

使用SDK进行数据投递前，您需要了解使用数据湖投递功能的注意事项、接口等信息。创建投递任务后，表格存储数据表中的数据会自动投递到OSS Bucket中存储。

注意事项 
-------------------------

* 目前支持使用数据湖投递功能的地域有华东1（杭州）、华东2（上海）、华北2（北京）和华北3（张家口）。

  

* 数据湖投递不支持同步删除操作，表格存储中的删除操作在数据投递时会被忽略，已投递到OSS中的数据不会被删除。

  

* 新建数据投递任务时存在最多1分钟的初始化时间。

  

* 数据同步存在延迟，写入速率稳定时，延迟在3分钟内。数据同步的P99延迟在10分钟内。

  **说明**

  P99延迟表示过去10秒内最慢的1%的请求的平均延迟。
  




接口 
-----------------------




|          接口          |        说明         |
|----------------------|-------------------|
| CreateDeliveryTask   | 创建一个投递任务。         |
| ListDeliveryTask     | 列出一个数据表所有的投递任务信息。 |
| DescribeDeliveryTask | 查询投递任务描述信息。       |
| DeleteDeliveryTask   | 删除一个投递任务。         |



使用 
-----------------------

您可以使用如下语言的SDK实现数据湖投递功能。

* [Java SDK]()

  

* [Go SDK]()

  




参数 
-----------------------



|     参数     |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
|------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| tableName  | 数据表名称。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| taskName   | 投递任务名称。  名称只能包含英文小写字母（a\~z）、数字和短横线（-），开头和结尾必须为英文小写字母或数字，且长度为3\~16字符。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| taskConfig | 投递任务配置，包括如下选项： * ossPrefix：OSS Bucket中的目录前缀，将表格存储的数据投递到该OSS Bucket目录中。投递路径中支持引用$yyyy、$MM、$dd、$HH、$mm五种时间变量。 * 当投递路径中引用时间变量时，可以按数据的写入时间动态生成OSS目录，实现hive partition naming style的数据时间分区，从而按照时间分区组织OSS中的文件分布。   * 当投递路径中不引用时间变量时，所有文件会被投递到固定的OSS前缀目录中。     * ossBucket：OSS Bucket名称。   * ossEndpoint：OSS Bucket所在地域的服务地址。   * ossStsRole：表格存储服务关联角色的ARN信息。   * format：投递的数据的存储以Parquet列存格式存储，数据湖投递默认使用PLAIN编码方式，PLAIN编码方式支持任意类型数据。   * eventTimeColumn：事件时间列，用于指定按某一列数据的时间进行分区。如果不设置此参数，则按数据写入表格存储的时间进行分区。   * parquetSchema：指定需要投递的数据列，必须手动配置投递字段的源表字段、目标字段和目标字段类型。 您可以选择任意字段以任意顺序、名称写入列存文件，OSS的列存数据会按Schema数组中的数据列先后顺序分布。 **注意** 投递数据的字段类型必须与数据源的字段类型匹配，否则会作为脏数据丢弃。字段类型映射详情请参见[数据格式映射](/intl.zh-CN/功能介绍/数据湖投递/快速入门.md)。    |
| taskType   | 投递任务的类型，包括如下选项： * INC：表示增量数据投递模式，只同步增量数据。   * BASE：表示全量数据投递模式，一次性全表扫描数据同步。   * BASE_INC（默认）：表示全量\&增量数据投递模式，全量数据同步完成后，再同步增量数据。 其中增量数据同步时可以获取最新投递时间和了解当前投递状态。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |



示例 
-----------------------

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
                    System.err.println("操作失败，详情：" + e.getMessage() + e.getErrorCode() + e.toString());
                    System.err.println("Request ID:" + e.getRequestId());
                } catch (ClientException e) {
                    System.err.println("请求失败，详情：" + e.getMessage());
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
                //eventColumn为可选配置，指定按某一列数据的时间进行分区。如果不设置此参数，则按数据写入表格存储的时间进行分区。
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



