Use DLA 
============================

After you configure a delivery task, Tablestore continuously delivers data to the corresponding Object Storage Service (OSS) bucket. Before you execute SQL statements to analyze data in OSS by using Data Lake Analytics (DLA), you must use metadata crawling tasks or create a external table that maps to an OSS folder.

Prerequisites 
----------------------------------

* DLA is activated. For more information, see [Activate Data Lake Analytics]().

  

* A delivery task is created. For more information, see [Quick start](/intl.en-US/Developer Guide/Data Delivery/Quick start.md).

  




Procedure 
------------------------------

To use DLA to analyze data in OSS, you can create a metadata crawling task by using the wizard or use SQL to create a external table. We recommend that you create a metadata crawling task by using the wizard.

* Use the wizard to create a metadata crawling task

  1. Create a metadata crawling task for an OSS folder by using the wizard in the DLA console. For more information, see [Crawl metadata]().

     After you create a metadata crawling task, the metadata (one or more tables) is automatically created and updated in DLA for the objects stored in OSS in a single run. In addition, the metadata crawling task can automatically explore data fields and field types, map folders to partitions, detect new columns and partitions, and split objects into tables.
     
  
  2. Query data. For more information. see [Run SQL statements](https://help.aliyun.com/document_detail/110762.html).

     
  

  

* Use SQL to create a external table

  1. On the **Execute** page, use SQL to create a schema and a external table that maps to an OSS folder. For more information, see [PARQUET](https://help.aliyun.com/document_detail/122096.html).

     **Note**

     You can create a schema by using the wizard or select the created schema.

     To create a external table that maps to an OSS folder, you can also obtain the SQL statement from the Tablestore console. To obtain the SQL statement, perform the following operations:

     On the **Deliver Data to OSS** tab, click **View SQL Statement to Create Table** in the **Actions** column corresponding to a delivery task. You can view and copy the SQL statement. The following figure shows an example of an SQL statement that is used to create a external table.

     ![select](//static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5915464061/p169620.png)
     
  
  2. Synchronize information about data partitions from an OSS source to the metadata. For more information, see [Synchronize the partition information of the OSS data source to the metadata](https://help.aliyun.com/document_detail/72009.html).

     
  
  3. Query data. For more information, see [Run SQL statements](https://help.aliyun.com/document_detail/110762.html).

     
  

  



