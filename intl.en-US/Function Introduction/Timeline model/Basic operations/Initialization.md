# Initialization

This topic describes how to initialize the Timeline model.

## Initialize TimelineStoreFactory

Use SyncClient as a parameter to initialize TimelineStoreFactory and create a Store to manage Meta data and Timeline data. The retry operation required after an error occurs depends on the retry policy of SyncClient. You can set SyncClient for the retry. If you have any special requirements, you can call the RetryStrategy operation to customize the retry policy.

```
/**
 * Configure the retry policy.
 * Code: configuration.setRetryStrategy(new DefaultRetryStrategy());
 * */
ClientConfiguration configuration = new ClientConfiguration();

SyncClient client = new SyncClient(
        "http://instanceName.cn-shanghai.ots.aliyuncs.com",
        "accessKeyId",
        "accessKeySecret",
        "instanceName", configuration);

TimelineStoreFactory serviceFactory = new TimelineStoreFactoryImpl(client);
```

## Initialize TimelineMetaStore

Create a schema for a Meta table. The schema includes parameters such as Identifier and MetaIndex. Create and obtain a Store to manage Meta data by using TimelineStoreFactory. You must specify the following parameters: Meta table name, index name, primary key field, and index type.

```
TimelineIdentifierSchema idSchema = new TimelineIdentifierSchema.Builder()
        .addStringField("timeline_id").build();

IndexSchema metaIndex = new IndexSchema();
metaIndex.addFieldSchema( //Set the index field and index type.
        new FieldSchema("group_name", FieldType.TEXT).setIndex(true).setAnalyzer(FieldSchema.Analyzer.MaxWord),
        new FieldSchema("create_time", FieldType.Long).setIndex(true)
);

TimelineMetaSchema metaSchema = new TimelineMetaSchema("groupMeta", idSchema)
        .withIndex("metaIndex", metaIndex); //Set the index.

TimelineMetaStore timelineMetaStore = serviceFactory.createMetaStore(metaSchema);
```

-   Create a table

    Create a table based on the parameters in metaSchema. If an index is configured in metaSchema, the index is created after the table is created.

    ```
    timelineMetaStore.prepareTables();
    ```

-   Delete a table

    If a table contains an index, the index is deleted before the table.

    ```
    timelineMetaStore.dropAllTables();
    ```


## Initialize TimelineStore

Create a schema for a Timeline table. The schema includes parameters such as Identifier and TimelineIndex. Create and obtain a Store to manage Timeline data by using TimelineStoreFactory. You must specify the following parameters: Timeline table name, index name, primary key field, and index type.

The BatchStore operation improves the concurrency performance on the basis of DefaultTableStoreWriter of Tablestore. You can set the number of concurrent threads in the thread pool.

```
TimelineIdentifierSchema idSchema = new TimelineIdentifierSchema.Builder()
        .addStringField("timeline_id").build();

IndexSchema timelineIndex = new IndexSchema();
timelineIndex.setFieldSchemas(Arrays.asList(//Configure the index field and index type.
        new FieldSchema("text", FieldType.TEXT).setIndex(true).setAnalyzer(FieldSchema.Analyzer.MaxWord),
        new FieldSchema("receivers", FieldType.KEYWORD).setIndex(true).setIsArray(true)
));

TimelineSchema timelineSchema = new TimelineSchema("timeline", idSchema)
        .autoGenerateSeqId() //Specify the auto-increment column as the method to generate the SequenceId value.
        .setCallbackExecuteThreads(5) //Set the number of initial threads of DefaultTableStoreWriter to 5.
        .withIndex("metaIndex", timelineIndex); //Set the index.

TimelineStore timelineStore = serviceFactory.createTimelineStore(timelineSchema);
```

-   Create a table

    Create a table based on the parameters in TimelineSchema. If an index is configured in TimelineSchema, the index is created after the table is created.

    ```
    timelineStore.prepareTables();
    ```

-   Delete a table

    If a table contains an index, the index is deleted before the table.

    ```
    timelineStore.dropAllTables();
    ```


