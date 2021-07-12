# DescribeStream

获取当前stream的shard信息。

## 请求结构

```
message DescribeStreamRequest {
    required string stream_id = 1;
    optional string inclusive_start_shard_id = 2;
    optional int32 shard_limit = 3;
}           
```

stream\_id:

-   类型：required string

-   当前stream的ID


inclusive\_start\_shard\_id:

-   类型：required string

-   查询起始shard的ID


shard\_limit:

-   类型：required string

-   单次查询返回shard数目的上限


## 响应消息结构

```
message DescribeStreamResponse {
    required string stream_id = 1;
    required int32 expiration_time = 2;
    required string table_name = 3;
    required int64 creation_time = 4;
    required StreamStatus stream_status = 5;
    repeated StreamShard shards = 6;
    optional string next_shard_id = 7;
}

message StreamShard {
    required string shard_id = 1;
    optional string parent_id = 2;
    optional string parent_sibling_id = 3;
}
            
```

stream\_id:

-   类型：required string

-   当前stream的ID


expiration\_time:

-   类型：required int32

-   Stream的过期时间


table\_name:

-   类型：required string

-   当前stream 所属的table名称


creation\_time:

-   类型：required int32

-   当前stream创建的时间


stream\_status:

-   类型：required StreamStatus

-   当前stream的状态，包括enabling和active


shards:

-   类型：required StreamShard

-   streamShard的信息，包括shard的ID，父shard的ID，父shard的邻居shard信息（适用于父shard发生merge）


next\_shard\_id:

-   类型：optional string

-   分页查询下一个shard的起始ID


## 注意事项

读取当前shard的数据时需要确保父shard的数据已经全部读取完毕。

