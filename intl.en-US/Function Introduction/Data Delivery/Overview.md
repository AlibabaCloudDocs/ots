Overview 
=============================

Tablestore uses data delivery to deliver full or incremental data to Object Storage Service (OSS) that is used as a data lake in real time. This feature enables Tablestore to store historical data in OSS at lower costs while Tablestore implements offline or quasi-real-time analysis of larger amounts of data.

Scenarios 
------------------------------

You can use data delivery to address needs of the following scenarios:

* Tiered storage of cold and hot data

  Data delivery uses the [time to live (TTL)](/intl.en-US/Function Introduction/Wide Column model/Max versions and TTL.md) feature of Tablestore to store full data in OSS at low costs. Tablestore allows you to query and analyze hot data with low latencies.
  

* Full data backup

  You can use data delivery to deliver data of a whole table to an OSS bucket for backup and archiving.
  

* Large-scale data analysis in real time

  You can use data delivery to deliver incremental data from Tablestore to OSS in real time (every 2 minutes). Delivered data is partitioned based on the system time and stored in the Parquet format. You can use OSS high-speed bandwidth for reading and optimization of scanning for Parquet data to implement efficient real-time data analysis.
  

* Accelerated analysis by using SQL statements

  When search indexes are not created for Tablestore data, and the query conditions exclude primary key column-based filter conditions, you can use data delivery to synchronize data to OSS. Then, use DLA and OSS data scanning to accelerate SQL-based analysis.

  ![image.png](../images/p168841.png "image.png")
  




Features 
-----------------------------

Data delivery has the following features:

* Data delivery obtains full and incremental data of Tablestore. When the amount of data reaches the predetermined size or after the data is delivered for more than two minutes, the data is stored in OSS.

  

* Data delivery allows you to deliver data in the following modes: incremental, full, and differential. All delivered data is stored in the Parquet format.

  

* Data delivery supports the monitoring of the time when data delivery is complete. Data delivery provides the DescribeDeliveryTask operation to return the time when data delivery is complete.

  




Benefits 
-----------------------------

* Ease of use

  To deliver data from Tablestore to OSS, you need only to complete simple configurations in the console. Synchronization tasks run and the throughput capacity is scaled based on the load while monitoring and O\&M are not required. However, service-level agreements (SLAs) are guaranteed.
  

* A complete set of data delivery modes

  Data delivery modes of full, incremental, and differential are provided. When the incremental mode is set, delivery tasks implement quasi-real-time delivery of data, obtain the latest data, cache the data, and write the data to OSS after 2 minutes.
  

* Seamless integration with the computational ecology

  Data delivery is compatible with open source ecology standards and the naming conventions followed by Hive. Delivered data is stored in the Parquet format. To analyze the data in an external table after data is delivered to an OSS bucket, use [E-MapReduce](https://www.alibabacloud.com/help/zh/product/28066.htm?spm=a2c63.m28257.a1.63.16d9ed69Q7b5Fm).
  

* Tiered storage and access experience

  After data is delivered to OSS, you can access different data such as data in tables and index tables and data delivered to OSS. This way, the analysis requirements of different scenarios are met.
  



