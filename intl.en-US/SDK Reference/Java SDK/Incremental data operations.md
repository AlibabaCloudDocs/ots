# Incremental data operations

Tablestore provides the ListStream and DescribeStream operations for streams, and GetShardIterator and GetShardRecord operations for shards.

## ListStream

You can call the ListStream operation to list all streams enabled for a table on an instance.

Example

List the information about all streams enabled for a table.

```
private static void listStream(SyncClient client, String tableName) {
    ListStreamRequest listStreamRequest = new ListStreamRequest(tableName);
    ListStreamResponse result = client.listStream(listStreamRequest);
}
            
```

## DescribeStream

You can call the DescribeStream operation to query the creationTime, expirationTime, status, shards, and ID of the next start shard \(if shards are not returned\) of a stream.

Example 1

Obtain the information about all shards of a stream.

```
    private static void describeStream(SyncClient client, String streamId) {
        DescribeStreamRequest desRequest = new DescribeStreamRequest(streamId);
        DescribeStreamResponse desStream = client.describeStream(desRequest);
    }
            
```

Example 2

Set the ID of the start shard \(InclusiveStartShardId\) and the maximum number of shards to return each time.

```
    private static void describeStream(SyncClient client, String streamId) {
    DescribeStreamRequest dsRequest = new DescribeStreamRequest(streamId);
    dsRequest.setInclusiveStartShardId(startShardId);
    dsRequest.setShardLimit(10);
    DescribeStreamResponse dscStream = client.describeStream(dsRequest);
    }
            
```

## GetShardIterator

You can call the GetShardIterator operation to obtain the start iterator for reading a shard.

Example

Obtain the start iterator for reading a shard.

```
    private static void getShardIterator(SyncClient client, String streamId, String shardId) {
    GetShardIteratorRequest getShardIteratorRequest = new GetShardIteratorRequest(streamId, shardId);
    GetShardIteratorResponse shardIterator = client.getShardIterator(getShardIteratorRequest);
    }
            
```

## GetStreamRecord

You can call the GetStreamRecord operation to obtain the update records of a shard.

Example

Obtain the first 100 update records of a shard.

```
    private static void getShardIterator(SyncClient client, String shardIterator) {
        GetStreamRecordRequest streamRecordRequest = new GetStreamRecordRequest(shardIterator);
        streamRecordRequest.setLimit(100);
        GetStreamRecordResponse streamRecordResponse = client.getStreamRecord(streamRecordRequest);
        List<StreamRecord> records = streamRecordResponse.getRecords();
        for(int k=0;k<records.size();k++){
            System.out.println("record info:" +  records.get(k).toString());
        }
        System.out.println("next iterator:" + streamRecordResponse.getNextShardIterator());
    }
            
```

