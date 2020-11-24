Stream computing 
=====================================

You can use Spark SQL on the E-MapReduce (EMR) cluster to access Tablestore. Stream computing uses change data capture (CDC) based on Tunnel Service to complete streaming consumption and computing in micro-batch mode of Spark. Meanwhile, at-least-once semantics is provided.



Prerequisites 
----------------------------------

* A Hadoop cluster is created in EMR. For more information, see [Create a cluster](/intl.en-US/Quick Start/Create a cluster.md).

  When you create a cluster, ensure that **Assign Public IP Address** is enabled to access the cluster over the Internet and Remote Logon is enabled to log on to a remote server by using Shell.
  **Note**

  This topic uses Shell commands. For more information about how to use the graphical interfaces of EMR to implement data development, see [Overview]().

  ![fig_sparksql001](../images/p162026.png)
  

* The [emr-datasources_shaded_2.11-2.2.0-SNAPSHOT.jar](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-emr/emr-datasources_shaded_2.11-2.2.0-SNAPSHOT.jar) package is uploaded to the EMR Header server.

  




Quick start 
--------------------------------

1. Create a table and search index at the Tablestore side.

   1. Create a Source table and a Sink table. For more information, see [Overview](/intl.en-US/Quick Start/Overview.md).

      The name of the Source table is OrderSource. The primary key columns are UserId and OrderId. The attribute columns are price and timestamp. 

      

      The name of the Sink table is OrderStreamSink. The primary key columns are begin and end. The attribute columns are count and totalPrice. The start time and end time are in the format of yyyy-MM-dd HH:mm:ss. Example: 2019-11-27 14:54:00.
      
   
   2. Create a tunnel on the Source table. For more information, see [Quick start](/intl.en-US/Function Introduction/Tunnel service/Quick start.md).

      The list of tunnels displays the name, ID, and type of each tunnel. The tunnel ID is used to stream data.

      ![fig_tunnelid_source](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7444126061/p164611.png)
      
   

   

2. Create a spark external table at the EMR cluster side.

   1. Log on to the EMR Header server.

      
   
   2. Run the following command to start the command line of Spark SQL. You can use the command line to create an external table of Spark and perform SQL-related operations.

      The standard parameters to start Spark is in the `--num-executors 32 --executor-memory 2g --executor-cores 2` format. You can adjust the parameter values based on the specific cluster configurations. \<Version\> indicates the version information of the uploaded JAR package. Example: 2.1.0-SNAPSHOT.

          spark-sql --jars emr-datasources_shaded_2.11-<Version>.jar --master yarn --num-executors 32 --executor-memory 2g --executor-cores 2

      
   
   3. Create a Source external table which is named order_source, and corresponds to the OrderSource table in Tablestore.

      * Parameters

        

        |     Parameter     |                                           Description                                            |
        |-------------------|--------------------------------------------------------------------------------------------------|
        | endpoint          | The endpoint to access the Tablestore instance. A VPC address is used to access the EMR cluster. |
        | access.key.id     | The AccessKey ID of your Alibaba Cloud account.                                                  |
        | access.key.secret | The AccessKey secret of your Alibaba Cloud account.                                              |
        | instance.name     | The name of the instance.                                                                        |
        | table.name        | The name of the Tablestore table.                                                                |
        | catalog           | The schema of the table.                                                                         |

        
      
      * Examples

            DROP TABLE IF EXISTS order_source;
            CREATE TABLE order_source
            USING tablestore
            OPTIONS(
            endpoint="http://vehicle-test.cn-hangzhou.vpc.tablestore.aliyuncs.com",
            access.key.id="",
            access.key.secret="",
            instance.name="vehicle-test",
            table.name="OrderSource",
            catalog='{"columns": {"UserId": {"type": "string"}, "OrderId": {"type": "string"},"price": {"type": "double"}, "timestamp": {"type": "long"}}}'
            );    

        
      

      
   

   

3. Perform stream computing.

   Stream computing collects statistics for the number of orders and order amount within a time window in real time and writes aggregate results to a Tablestore table.
   1. Create a Sink external table which is named order_stream_sink of stream computing, and corresponds to the OrderStreamSink table in Tablestore.

      The parameter configurations for creating a Sink external table and Source external table differ only in the fields of catalog.
      
   
   2. Create a view on the order_source table.

      When you create the view, you must set the ID of the tunnel configured for the table.
      
   
   3. Run a Stream SQL job to perform aggregations in real time. The aggregate results are written to the OrderStreamSink table of Tablestore.

      
   

   

       // Create an external table, which is the Sink table named order_stream_sink. This table corresponds to the OrderStreamSink table in Tablestore.
       DROP TABLE IF EXISTS order_stream_sink;
       CREATE TABLE order_stream_sink
       USING tablestore
       OPTIONS(
       endpoint="http://vehicle-test.cn-hangzhou.vpc.tablestore.aliyuncs.com",
       access.key.id="",
       access.key.secret="",
       instance.name="vehicle-test",
       table.name="OrderStreamSink",
       catalog='{"columns": {"begin": {"type": "string"},"end": {"type": "string"}, "count": {"type": "long"}, "totalPrice": {"type": "double"}}}'
       );
       
       // Create a view named order_source_stream_view on the order_source table.
       CREATE SCAN order_source_stream_view ON order_source USING STREAM
       OPTIONS(
       tunnel.id="4987845a-1321-4d36-9f4e-73d6db63bf0f", 
       maxoffsetsperchannel="10000");
       
       // Run a Stream SQL job on order_source_stream_view. The following code provides an example on how to aggregate the number of orders and order amount at the granularity of 30s.
       // Write the aggregate results to the OrderStreamSink table in Tablestore in real time.
       CREATE STREAM job1
       options(
       checkpointLocation='/tmp/spark/cp/job1',
       outputMode='update'
       )
       INSERT INTO order_stream_sink
       SELECT CAST(window.start AS String) AS begin, CAST(window.end AS String) AS end, count(*) AS count, CAST(sum(price) AS Double) AS totalPrice FROM order_source_stream_view GROUP BY window(to_timestamp(timestamp / 1000), "30 seconds");

   

   You can obtain aggregate results after you run Stream SQL. The following figure shows an example of aggregate results. The aggregate results are saved in the OrderStreamSink table.

   



