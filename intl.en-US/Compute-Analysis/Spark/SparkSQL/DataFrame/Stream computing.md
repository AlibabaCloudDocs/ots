Stream computing 
=====================================

You can use Spark DataFrame to access Tablestore and perform debugging on the local computer and cluster.

Prerequisites 
----------------------------------

* You have understood the dependent packages that are used for Spark to access Tablestore. These dependent packages have been imported to the project by using maven.

  * Spark related dependent packages: spark-core, spark-sql, and spark-hive

    
  
  * Spark Tablestore connector: emr-tablestore-\<version\>.jar

    
  
  * Tablestore SDK for Java: tablestore-\<version\>-jar-with-dependencies.jar

    
  

  

  \<version\> indicates the version number of the corresponding dependent package. Use the actual version number.
  

* A Source table and a tunnel for the Source table are created at the Tablestore side. For more information, see [Overview](/intl.en-US/Quick Start/Overview.md).

  






Quick start 
--------------------------------

You can learn about how to use stream computing by using project samples.

1. Download the source code of the project sample from GitHub. For more information about the path to download the code, visit [TableStoreSparkDemo](https://github.com/aliyun/tablestore-examples/tree/master/feature/TableStoreSparkDemo).

   The project contains the complete dependencies and samples on how to use batch computing. For more information about the dependencies, see the pom file in the project.
   

2. Read the README document of the TableStoreSparkDemo project. Install Spark Tablestore connector and Tablestore SDK for Java of the latest versions to the local maven library.

   **Note**

   

3. Modify the sample code.

   StructuredTableStoreAggSQLSample is used in the example. Descriptions of the core sample code:
   * format("tablestore") indicates that the ServiceLoader method is used to load Spark Tablestore connector. For more information about configurations, see META-INF.services in the project.

     
   
   * instanceName indicates the name of the Tablestore instance. tableName indicates the name of the table. endpoint indicates the endpoint of the instance. accessKeyId indicates the AccessKey ID of the Alibaba Cloud account. accessKeySecret indicates the AccessKey secret of the Alibaba Cloud account.

     
   
   * catalog is a JSON string, which includes field names and types. In the following example, the table has the following fields: UserId (type: STRING), OrderId (type: STRING), price (type: DOUBLE), and timestamp (type: LONG).

     
   
   * maxoffsetsperchannel indicates the maximum data volume read by each channel of micro-batch operations. Default value: 10000.

             val ordersDF = sparkSession.readStream
               .format("tablestore")
               .option("instance.name", instanceName)
               .option("table.name", tableName)
               .option("tunnel.id", tunnelId)
               .option("endpoint", endpoint)
               .option("access.key.id", accessKeyId)
               .option("access.key.secret", accessKeySecret)
               .option("maxoffsetsperchannel", maxOffsetsPerChannel) // The default value is 10000.
               .option("catalog", dataCatalog)
               .load()
               .createTempView("order_source_stream_view")
         
           val dataCatalog: String =
             s"""
                |{"columns": {
                |    "UserId": {"type":"string"},
                |    "OrderId": {"type":"string"},
                |    "price": {"type":"double"},
                |    "timestamp": {"type":"long"}
                | }
                |}""".stripMargin

     
   

   






Debugging 
------------------------------

After the sample code is modified, you can perform debugging on the local computer or Spark cluster. StructuredTableStoreAggSQLSample is used in the example to describe the debugging process.

* Perform debugging on the local computer

  Intellij IDEA is used in the example.
  **Note**

  The environments used for testing are Spark 2.4.3, Scala 2.11.7, and Java SE Development Kit 8. If you encounter any problems when you debug the sample code, contact Tablestore technical support personnel.
  1. Configure system parameters such as the name of the instance, the name of the table, the endpoint of the instance, and AccessKey ID and AccessKey secret of the Alibaba Cloud account.

     You can also customize the method to load parameters.
     
  
  2. Select **include dependencies with "provided" scope,** . Click **OK** .

     
  
  3. Run the code sample.

     ![idea_002](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0944126061/p163831.png)
     
  

  

* Perform debugging on the Spark cluster

  The spark-submit method is used in the example. By default, master in the sample code is set to `local[*]`. You can remove this parameter when you run tasks on the Spark cluster. Then, import the spark-submit parameter.
  1. Run the **mvn -U clean package** command for packaging. The path of the package is `target/tablestore-spark-demo-1.0-SNAPSHOT-jar-with-dependencies.jar`.

     
  
  2. Upload the package to the Driver node of the Spark cluster. Use spark-submit to submit a task.

         spark-submit --class com.aliyun.tablestore.spark.demo.streaming.StructuredTableStoreAggSQLSample --master yarn tablestore-spark-demo-1.0-SNAPSHOT-jar-with-dependencies.jar <ots-instanceName> <ots-tableName> <ots-tunnelId> <access-key-id> <access-key-secret> <ots-endpoint> <max-offsets-per-channel>

     

     ![fig_dataframe_streaming_001](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0944126061/p163832.png)
     
  

  




