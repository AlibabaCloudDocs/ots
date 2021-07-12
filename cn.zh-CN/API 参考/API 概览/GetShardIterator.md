# GetShardIterator

获取读取当前shard记录的起始iterator。

## 请求结构

```
message GetShardIteratorRequest {
    required string stream_id = 1;
    required string shard_id = 2;
}           
```

stream\_id:

-   类型：required string

-   当前stream的ID


shard\_id:

-   类型：required string

-   当前shard的ID


## 响应消息结构

```
message GetShardIteratorResponse {
    required string shard_iterator = 1;
}            
```

shard\_iterator:

-   类型：required string

-   读取当前shard记录的起始iterator


