Batch computing 
====================================

You can use Spark SQL on an E-MapReduce (EMR) cluster to access Tablestore. Compared with batch computing, Tablestore Spark connector allows you to select indexes, prune partitions, perform Projection column and Filter pushdown, and dynamically specify the sizes of partitions. In addition, Tablestore Spark connector uses global secondary index or search index of Tablestore to accelerate queries.

Prerequisites 
----------------------------------

* An EMR Hadoop cluster is created. For more information, see [Create a cluster](/intl.en-US/Quick Start/Create a cluster.md).

  When you create a cluster, make sure that **Assign Public IP Address** is enabled to access the cluster over the Internet and Remote Logon is enabled to log on to a remote server by using Shell.
  **Note**

  This topic uses Shell commands. For more information about how to use the graphical interfaces of EMR to implement data development, see [Manage projects](/intl.en-US/Data Development/Manage projects.md).

  

* The [emr-datasources_shaded_2.11-2.2.0-SNAPSHOT.jar](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-emr/emr-datasources_shaded_2.11-2.2.0-SNAPSHOT.jar) package is uploaded to the EMR Header server.

  




Use Spark to connect to a Tablestore table and global secondary index 
------------------------------------------------------------------------------------------

After Spark is connected to a Tablestore table and global secondary index, the system selects an index table based on the column conditions for queries.

1. Create a table or global secondary index at the Tablestore side.

   1. Create a Tablestore data table. For more information, see [Overview](/intl.en-US/Quick Start/Overview.md).

      In the example, the name of the table is tpch_lineitem_perf. The primary key columns are l_orderkey (type: LONG) and l_linenumber (type: LONG). Part of the 14 attribute columns are l_comment (type: STRING), l_commitdate (type: STRING), l_discount (type: DOUBLE), l_extendedprice (type: DOUBLE), l_linestatus (type: STRING), l_partkey(type: LONG), l_quantity(type: DOUBLE), and l_receiptdate (type: STRING). The number of data entries are 384,016,850. The following figure shows an example of the data entries.

      ![fig_00a](../images/p175968.png)
      
   
   2. (Optional). Create a global secondary index on the data table. For more information, see [Use SDKs](/intl.en-US/Function Introduction/Global secondary index/Use SDKs.md).

      **Note**

      To use non-primary key column columns in the query conditions, we recommend that you create a global secondary index to accelerate queries.

      Global secondary index allows you to create an index table based on specified columns. Data in the generated index table is sorted by the specified indexed columns. All data written to the base table is automatically synchronized to the index table in an asynchronous manner.

      ![fig_00b](../images/p175971.png)
      
   

   

2. Create an external table in Spark SQL at the EMR cluster side.

   1. Log on to the EMR Header server.

      
   
   2. Run the following command to start the command line of Spark SQL. You can use the command line to create an external table in Spark SQL and perform SQL-related operations.

      The standard parameters to start Spark is in the `--num-executors 32 --executor-memory 2g --executor-cores 2` format. You can adjust the parameter values based on the specific cluster configurations. \<Version\> specifies the version information of the uploaded JAR package. Example: 2.1.0-SNAPSHOT.

          spark-sql --jars emr-datasources_shaded_2.11-<Version>.jar --master yarn --num-executors 32 --executor-memory 2g --executor-cores 2

      
   
   3. Connect to the global secondary index when you create the external table in Spark SQL.

      * Parameters

        

        |     Parameter     |                                                                    Description                                                                    |
        |-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
        | endpoint          | The endpoint to access the Tablestore instance. A VPC address is used to access the EMR cluster.                                                  |
        | access.key.id     | The AccessKey ID of your Alibaba Cloud account.                                                                                                   |
        | access.key.secret | The AccessKey secret of your Alibaba Cloud account.                                                                                               |
        | instance.name     | The description of the instance.                                                                                                                  |
        | table.name        | The name of the data table.                                                                                                                       |
        | split.size.mbs    | The size of each split. Default value: 100. Unit: MB.                                                                                             |
        | max.split.count   | The maximum number of splits calculated for the table. The number of concurrent tasks corresponding to the number of splits. Default value: 1000. |
        | catalog           | The schema of the table.                                                                                                                          |

        

        
      
      * Examples

            DROP TABLE IF EXISTS tpch_lineitem;
            CREATE TABLE tpch_lineitem
            USING tablestore
            OPTIONS(
            endpoint="http://vehicle-test.cn-hangzhou.vpc.tablestore.aliyuncs.com",
            access.key.id="",
            access.key.secret="",
            instance.name="vehicle-test",
            table.name="tpch_lineitem_perf",
            split.size.mbs=10,
            max.split.count=1000,
            catalog='{"columns":{"l_orderkey":{"type":"long"},"l_partkey":{"type":"long"},"l_suppkey":{"type":"long"},"l_linenumber":{"type":"long"},"l_quantity":{"type":"double"},"l_extendedprice":{"type":"double"},"l_discount":{"type":"double"},"l_tax":{"type":"double"},"l_returnflag":{"type":"string"},"l_linestatus":{"type":"string"},"l_shipdate":{"type":"string"},"l_commitdate":{"type":"string"},"l_receiptdate":{"type":"string"},"l_shipinstruct":{"type":"string"},"l_shipmode":{"type":"string"},"l_comment":{"type":"string"}}}'
            );

        

        
      

      
   

   

3. Perform queries by using SQL statements

   The following section provides examples on SQL queries to address different query needs:
   * Full table query

     * SQL statement: `SELECT COUNT(*) FROM tpch_lineitem;`

       
     
     * Total duration for running the SQL statement: 36.199s, 34.711s, and 34.801s. Average duration: 35.237s.

       
     

     
   
   * Primary key query

     * SQL statement **:** `SELECT COUNT(*) FROM tpch_lineitem WHERE l_orderkey = 1 AND l_linenumber = 1;`

       
     
     * Tablestore server: average duration of 0.585 ms for the GetRow operation

       
     

     
   
   * Non-primary key columns query without using global secondary index

     * SQL statement: `SELECT count(*) FROM tpch_lineitem WHERE l_shipdate = '1996-06-06';`

       
     
     * Total duration for running the SQL statement: 37.006s, 37.269s, and 37.17s. Average duration: 37.149s.

       
     

     
   
   * Non-primary key columns query by using global secondary index

     * SQL statement: `SELECT count(*) FROM tpch_lineitem WHERE l_shipdate = '1996-06-06';`

       
     
     * Total duration for running the SQL statement when global secondary index is enabled for the l_shipdate column: 1.686s, 1.651s, and 1.784s. Average duration: 1.707s

       
     

     
   

   






Use Spark to connect to a Tablestore table and search index 
--------------------------------------------------------------------------------

After Spark is connected to a Tablestore table and search index, the system selects an index table based on the column conditions for queries.

1. Create a table and search index at the Tablestore side.

   1. Create a data table. For more information, see [Overview](/intl.en-US/Quick Start/Overview.md).

      In the example, the name of the table is geo_table. The primary key column is pk1 (type: STRING). The attribute columns are val_keyword1 (type: STRING), val_keyword2 (type: STRING), val_keyword3 (type: STRING), val_bool (type: BOOLEAN), val_double (type: DOUBLE), val_long1 (type: LONG), val_long2 (type: LONG), val_text (type: STRING), and val_geo (type: STRING). The number of data entries is 208,912,382. The following figure shows an example.

      ![fig_002](../images/p175963.png)
      
   
   2. A search index is created on the table. For more information, see [Create and use search indexes](/intl.en-US/Quick Start/Create and use search indexes.md).

      When you create a search index, configure mappings for the search index based on field types.
      **Note**

      When you create a search index, select Geographical Location instead of STRING for a GEOPOINT field in the search index.

      ![fig_001](../images/p175962.png)

      After you create a search index, the search index synchronizes data from the table. When the search index enters the incremental state, the search index is created.

      ![fig_searchindex_batch_list](../images/p164617.png)
      
   

   

2. Create an external table by using Spark at the EMR cluster side.

   1. Log on to the EMR Header server.

      
   
   2. Connect to the search index when you create the external table in Spark SQL.

      * Parameters

        

        |       Parameter        |                                                                                                                                                                                                                                                                                                                                                               Description                                                                                                                                                                                                                                                                                                                                                                |
        |------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
        | endpoint               | The endpoint to access the Tablestore instance. A VPC address is used to access the EMR cluster.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
        | access.key.id          | The AccessKey ID of your Alibaba Cloud account.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
        | access.key.secret      | The AccessKey secret of your Alibaba Cloud account.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
        | instance.name          | The description of the instance.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
        | table.name             | The name of the Tablestore table.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
        | search.index.name      | The name of the search index.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
        | max.split.count        | The maximum number of concurrent tasks used by parallel scan of search index. The maximum number of concurrent tasks used by parallel scan corresponds to that of splits in Spark.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
        | push.down.range.long   | Specifies whether to push down predicates where operators (\>= \> \< \<=) are used to compare values with LONG column values. For more information, see [Configure predicate pushdown for batch computing](/intl.en-US/Compute-Analysis/Spark/SparkSQL/Detail analysis/Configure predicate pushdown for batch computing.md). Type: Boolean. The default value is true, which indicates that predicates where operators (\>= \> \< \<=) are used to compare values with LONG column values are pushed down. A value of false indicates that predicates where operators (\>= \> \< \<=) are used to compare values with LONG column values are not pushed down. For more information, see. |
        | push.down.range.string | Specifies whether to push down predicates where operators (\>= \> \< \<=) are used to compare values with STRING column values. For more information, see [Configure predicate pushdown for batch computing](/intl.en-US/Compute-Analysis/Spark/SparkSQL/Detail analysis/Configure predicate pushdown for batch computing.md). Type: Boolean. The default value is true, which indicates that predicates where operators (\>=, \>, \<, and \<=) that are used to compare values with STRING column values are pushed down. A value of false indicates that predicates where operators (\>=, \>, \<, and \<=) are used to compare values with STRING column values are not pushed down.   |

        

        
      
      * Examples

            DROP TABLE IF EXISTS geo_table;
            CREATE TABLE geo_table (
            pk1 STRING, val_keyword1 STRING, val_keyword2 STRING, val_keyword3 STRING, 
            val_bool BOOLEAN, val_double DOUBLE, val_long1 LONG, val_long2 LONG,
            val_text STRING, val_geo STRING COMMENT "geo stored in string format"
            )
            USING tablestore
            OPTIONS(
            endpoint="https://sparksearchtest.cn-hangzhou.vpc.tablestore.aliyuncs.com",
            access.key.id="",
            access.key.secret="",
            instance.name="sparksearchtest",
            table.name="geo_table",
            search.index.name="geo_table_index",
            max.split.count=64,
            push.down.range.long = false,
            push.down.range.string = false
            );

        
      

      
   

   

3. Perform queries by using SQL statements

   The following section provides examples on SQL queries to address different query needs:
   * Full table query by using the search index

     * SQL statement: `SELECT COUNT(*) FROM geo_table;`

       
     
     * Duration for running the SQL statement: Actual duration: 165.208s. Average QPS: 1,264,500. Data entries for testing: 208,912,382. Number of concurrent tasks by using parallel scan: 64.

           208912382
           Time taken: 165.208 seconds, Fetched 1 row(s)
           20/06/29 20:55:11 INFO [main] SparkSQLCLIDriver: Time taken: 165.208 seconds, Fetched 1 row(s)

       
     

     
   
   * Boolean query

     * SQL statement: `SELECT val_long1, val_long2, val_keyword1, val_double FROM geo_table WHERE (val_long1 > 17183057 AND val_long1 < 27183057) AND (val_long2 > 1000 AND val_long2 < 5000) LIMIT 100;`

       
     
     * Duration for running the SQL statement: Actual duration: 2.728s. Spark pushes the Projection column and Filter to the search index, which accelerates query efficiency.

           21423964        4017    aaa     2501.9901650365096
           21962236        2322    eio     2775.9021545044116
           Time taken: 2.894 seconds, Fetched 100 row(s)
           20/06/30 18:51:24 INFO [main] SparkSQLCLIDriver: Time taken: 2.894 second

       
     

     
   
   * Geo query

     Geo query includes the following query types: geo-distance query, geo-bounding box query, and geo-polygon query. In the example, val_geo indicates the name of the GEOPOINT field. The coordinate pair of a geographical location is in the format of "latitude,longitude".
     * Geo-distance query

       Syntax: val_geo = '{"centerPoint":"the coordinate pair of the central point", "distanceInMeter": the distance from the central point'}'

       SQL statement: `SELECT COUNT(*) FROM geo_table WHERE val_geo = '{"centerPoint":"6.530045901643962,9.05358919674954", "distanceInMeter": 3000.0}';`
       
     
     * Geo-bounding box query

       Syntax: val_geo = '{"topLeft":"the coordinate of the upper-left corner in the rectangular area", "bottomRight": "the coordinate of the lower-right corner in the rectangular area"}'

       SQL statement: `SELECT COUNT(*) FROM geo_table WHERE val_geo = '{"topLeft":"6.257664116603074,9.1595116589601", "bottomRight": "6.153593333442616,9.25968497923747"}';`
       
     
     * Geo-polygon query

       Syntax: val_geo = '{"points":\["Coordinate Pair 1", "Coordinate Pair 2", .... "Coordinate Pair n-1", "Coordinate Pair n"\]}'

       SQL statement: `SELECT COUNT(*) FROM geo_table WHERE val_geo = '{"points":["6.530045901643962,9.05358919674954", "6.257664116603074,9.1595116589601", "6.160393397574926,9.256517839929597", "6.16043846779313,9.257192872563525"]}';`
       
     

     
   

   




