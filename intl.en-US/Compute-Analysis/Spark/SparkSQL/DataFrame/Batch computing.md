Batch computing 
====================================

You can use Spark DataFrame to access Tablestore and perform debugging on the local computer and cluster.

Prerequisites 
----------------------------------

* You have understood the dependent packages that are used for Spark to access Tablestore. These dependent packages have been imported to the project by using maven.

  * Spark related dependent packages: spark-core, spark-sql, and spark-hive

    
  
  * Spark Tablestore connector: emr-tablestore-\<version\>.jar

    
  
  * Tablestore SDK for Java: tablestore-\<version\>-jar-with-dependencies.jar

    
  

  

  \<version\> indicates the version number of the corresponding dependent package. Use the actual version number.
  

* A table is created at the Tablestore side. For more information, see [Overview](/intl.en-US/Quick Start/Overview.md).

  






Quick start 
--------------------------------

The following section describes how to use batch computing by using project samples.

1. Download the source code of the project sample from GitHub. For more information about the path to download the code, visit [TableStoreSparkDemo](https://github.com/aliyun/tablestore-examples/tree/master/feature/TableStoreSparkDemo).

   The project contains the complete dependencies and samples on how to use batch computing. For more information about the dependencies, see the pom file in the project.
   

2. Read the README document of the TableStoreSparkDemo project. Install Spark Tablestore connector and Tablestore SDK for Java of the latest versions to the local maven library.

   

3. Modify the sample code.

   TableStoreBatchSample is used in the example. Descriptions of the core sample code:
   * format("tablestore") indicates that the ServiceLoader method is used to load Spark Tablestore connector. For more information about configurations, see META-INF.services in the project.

     
   
   * instanceName indicates the name of the Tablestore instance. tableName indicates the name of the table. endpoint indicates the endpoint of the instance. accessKeyId indicates the AccessKey ID of the Alibaba Cloud account. accessKeySecret indicates the AccessKey secret of the Alibaba Cloud account.

     
   
   * catalog is a JSON string, which includes field names and types. In the following example, the table has the following fields: salt (type: Long), UserId (type: String), OrderId (type: String), price (type: Double), and timestamp (type: Long).

     In the latest version, you can use the Schema method to replace the catalog configurations. Select a method based on the version.
     
   
   * split.size.mbs: optional. This parameter indicates the size of each split. Default value: 100. Unit: GB.

     A smaller value indicates more splits and tasks for Spark.

             val df = sparkSession.read
             .format("tablestore")
             .option("instance.name", instanceName)
             .option("table.name", tableName)
             .option("endpoint", endpoint)
             .option("access.key.id", accessKeyId)
             .option("access.key.secret", accessKeySecret)
             .option("split.size.mbs", 100)
             .option("catalog", dataCatalog)
             // The latest version allows you to replace catalog configurations with the Schema method.
             //.schema("salt LONG, UserId STRING, OrderId STRING, price DOUBLE, timestamp LONG")
             .load()
         
           val dataCatalog: String =
             s"""
                |{"columns": {
                |    "salt": {"type":"long"},
                |    "UserId": {"type":"string"},
                |    "OrderId": {"type":"string"},
                |    "price": {"type":"double"},
                |    "timestamp": {"type":"long"}
                | }
                |}""".stripMargin

     
   

   






Debugging 
------------------------------

After the sample code is modified, you can perform debugging on the local computer or Spark cluster. TableStoreBatchSample is used in the example to describe the debugging process.

* Perform debugging on the local computer

  IntelliJ IDEA is used in the example.
  **Note**

  The environments used for testing are Spark 2.4.3, Scala 2.11.7, and Java SE Development Kit 8. If you encounter any problems when you debug the sample code, contact Tablestore technical support personnel.
  1. Configure system parameters such as the name of the instance, the name of the table, the endpoint of the instance, and AccessKey ID and AccessKey secret of the Alibaba Cloud account.

     You can also customize the method to load parameters.
     
  
  2. Select **include dependencies with "provided" scope** . Click **OK** .

     
  
  3. Run the code sample.

     ![idea_001](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7744126061/p163830.png)
     
  

  

* Perform debugging on the Spark cluster

  The spark-submit method is used in the example. By default, master in the sample code is set to `local[*]`. You can remove this parameter when you run tasks on the Spark cluster. Then, import the spark-submit parameter.
  1. Run the **mvn-u clean package** command for packaging. The path of the package is `target/tablestore-spark-demo-1.0-SNAPSHOT-jar-with-dependencies.jar`.

     
  
  2. Upload the package to the Driver node of the Spark cluster. Use spark-submit to submit a task.

         spark-submit --class com.aliyun.tablestore.spark.demo.batch.TableStoreBatchSample --master yarn tablestore-spark-demo-1.0-SNAPSHOT-jar-with-dependencies.jar <ots-instanceName> <ots-tableName> <access-key-id> <access-key-secret> <ots-endpoint>

     

     ![fig_batch_dataframe001](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7744126061/p163818.png)
     
  

  




