Implementation of stream computing 
=======================================================

This topic describes how to use Structured Streaming in micro-batch mode and Spark Structured Streaming to connect to Tablestore.

Background information 
-------------------------------------------

Spark Data Source API V1 is used in the example to describe how to connect to Spark Structured Streaming in micro-batch mode.

1. Call the GetOffset method to obtain the maximum offset (EndOffset) the current batch can read.

   

2. Call the GetBatch method to obtain the data between the start offset (end offset of the last batch) and end offset of the current batch. Then, convert the data.

   

3. Execute the custom computational logic of Spark.

   




When the connection is in micro-batch mode, the streaming operation of the upstream database must provide the flexible and accurate Seek feature and obtain the start or end cursor of each partition in real time to estimate the offset for the micro-batch mode.

In distributed NoSQL databases, the change data capture (CDC)-based streaming operation uses the continuous mode. This operation does not provide the flexible Seek operation. It is difficult to connect this operation to the micro-batch operation of Structured Streaming. If data is obtained in the GetOffset stage in advance, the desired end offset can be obtained. However, additional Resilient Distributed Dataset (RDD)-based computing must be performed in advance, which requires the data to be persisted as the cache, which degrades the performance of Source.

Procedure 
------------------------------

The following figure shows the Unified Modeling Language (UML) sequence diagram for Tablestore to connect to Structured Streaming.

In the figure, MicroBatchExecutor serves as the micro-batch framework of Spark. Source serves as the abstract class of Structured Streaming. SourceRDD serves as the RDD abstract class of Spark. TablestoreClient serves as the client of Tablestore. Solid lines indicate detailed operations. Dashed lines indicate successful operations.

The process is implemented by the GetOffset, Commit, and GetBatch steps by using loops. Each loop corresponds to a batch operation of Spark Streaming.

Detailed connection process:

1. Call the GetOffset method to obtain the maximum offset (EndOffset) the current batch can read.

   1. Call the GetOffset method of Source by using MicroBatchExecutor to obtain the maximum offset (EndOffset) of the current batch.

      
   
   2. Generate a random UUID string in Source. One UUID corresponds to one offset value.

      
   
   3. (Optional) Obtain the tunnel information of Tablestore.

      **Note**

      This step is required only when the GetOffset method of Source is called for the first time. Otherwise, the system skips this step.
      1. Obtain the checkpoints when all current channels consume data from the Tablestore Tunnel server.

         
      
      2. Persists the checkpoints in the Meta table. Establish the mapping between the UUID and checkpoint.

         
      

      
   
   4. Encapsulate the UUID into the offset (you can convert between the offset and UUID). Return the end offset of the current batch to MicroBatchExecutor.

      
   

   

2. (Optional) Persist the checkpoints in the Tunnel server.

   **Note**

   This step is performed only when the ID of the current batch is greater than 0. Otherwise, the system skips this step.
   1. Call the Commit logic of Source by using MicroBatchExecutor.

      
   
   2. Persist the checkpoints that correspond to the end offsets of the last batches (start offset of the current batch) in the Tunnel server.

      
   

   
   **Note**

   In normal cases, the Commit logic is not required to process additional content. This operation serves the following purposes:
   * Display the consumption progress in real time after the checkpoints are persisted in the Tunnel server.

     
   
   * Load subpartitions to ensure that data is stored in the Tunnel server based on the root-child relationship. To load subpartitions, the client must return the checkpoints when partitions have consumed data to the server.

     
   

   
   

3. Call the GetBatch method to obtain the data between the start offset (end offset of the last batch) and end offset of the current batch. Then, convert the data.

   1. Call the GetBatch operation of Source by using MicroBatchExecutor based on the start offset of the current batch and end offset returned from GetOffset. This operation is called to obtain the data of the current batch for the computational logic.

      
   
   2. Obtain the real-time checkpoints of the channels in Tunnel from the Meta table based on the UUID to which the start offset corresponds.

      
   
   3. Obtain the checkpoints of channels from the Tunnel server on a regular basis.

      New partitions such as subpartitions are generated due to changes to partitions of a table. Therefore, the checkpoints of channels must be obtained on a regular basis.
      
   
   4. Obtain the checkpoints of all channels by merging the real-time checkpoints obtained from the Meta table and the checkpoints from the Tunnel server on a regular basis.

      
   
   5. Create SourceRDD based on information such as the latest checkpoint and UUID to which the end offset corresponds.

      RDDs are the primary data abstraction in Spark. An RDD is an immutable and partitionable collection of objects that can be worked on in parallel.
      
   
   6. In SourceRDD, the channel is bound to the partition of an RDD. Therefore, each channel converts and processes data in parallel on the node of Spark.

      
   

   

4. Execute the custom computational logic of Spark.

   1. Execute computational logic on each partition. Data is read from the channel. The checkpoint in the memory is updated for each channel.

      
   
   2. After each partition completes the task for the current batch, such as reading the specified number of entries or detecting that no new data is found, persist the latest checkpoint of the channel in the row that has the corresponding UUID in the Meta table. The checkpoint is written in EndOffset of the row that has the corresponding UUID. After each batch operation is completed, the checkpoint corresponding to the start offset of the new batch can be found in the Meta table.

      
   
   3. After the computational logic on all partitions is executed, return the data within the Offset range corresponding to the current batch to MicroBatchExecutor.

      
   

   

5. After the computational logic is executed for all partitions in the current batch, increase the batch ID. The preceding steps are performed from Step 1 in a continuous loop.

   




![fig_newuml](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8254126061/p170876.png)



