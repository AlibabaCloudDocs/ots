# Queue管理

本文主要为您介绍使用Timeline模型时如何进行Queue管理。

## 获取Queue实例

Queue是单个消息队列的抽象概念，对应TimelineStore下单个Identifier的所有消息。获取Queue实例时通过TimelineStore的接口创建。

```
TimelineIdentifier identifier = new TimelineIdentifier.Builder()
        .addField("timeline_id", "group_1")
        .build();

// 单TimelineStore下单identifier对应的消息队列（Queue）
TimelineQueue timelineQueue = timelineStore.createTimelineQueue(identifier);
```

Queue是单存储库下单Identifier对应的消息队列的管理实例，主要有同步写、异步写、批量写、删、同步改、异步改、单行读、范围读等接口。

## Store

同步存储消息，两个接口分别支持SequenceId的两种实现方式：自增列、手动设置，相关配置在TimelineSchema中。

```
timelineQueue.store(message);//自增列
timelineQueue.store(sequenceId, message);//手动
```

## StoreAsync

异步存储消息，用户可自定义回调，对成功、失败做自定义处理。接口返回Future<TimelineEntry\>。

```
TimelineCallback callback = new TimelineCallback() {
    @Override
    public void onCompleted(TimelineIdentifier i, TimelineMessage m, TimelineEntry t) {
        // do something when succeed.
    }

    @Override
    public void onFailed(TimelineIdentifier i, TimelineMessage m, Exception e) {
        // do something when failed.
    }
};

timelineQueue.storeAsync(message, callback);//自增列实现的SequenceId
timelineQueue.storeAsync(sequenceId, message, callback);//手动设置SequenceId
```

## BatchStore

批量存储消息，支持无回调、有回调两种。用户可自定义回调，对成功、失败做自定义处理。

```
timelineQueue.batchStore(message);//自增列
timelineQueue.batchStore(sequenceId, message);//手动

timelineQueue.batchStore(message, callback);//自增列
timelineQueue.batchStore(sequenceId, message, callback);//手动
```

## Get

通过SequenceId读取单行消息，消息不存在时不抛错，返回null。

```
timelineQueue.get(sequenceId);
```

## GetLatestTimelineEntry

读取最新一条消息，消息不存在时不抛错，返回null。

```
timelineQueue.getLatestTimelineEntry();
```

## GetLatestSequenceId

获取最新一条消息的SequenceId，消息不存在时不抛错，返回0。

```
timelineQueue.getLatestSequenceId();
```

## Update

通过SequenceId同步更新消息内容。

```
TimelineMessage message = new TimelineMessage().setField("text", "Timeline is fine.");

//update message with new field
message.setField("text", "new value");
timelineQueue.update(sequenceId, message);
```

## UpdateAsync

通过SequenceId异步更新消息，用户可自定义回调，对成功、失败做自定义处理。接口返回Future<TimelineEntry\>。

```
TimelineMessage oldMessage = new TimelineMessage().setField("text", "Timeline is fine.");
TimelineCallback callback = new TimelineCallback() {
    @Override
    public void onCompleted(TimelineIdentifier i, TimelineMessage m, TimelineEntry t) {
        // do something when succeed.
    }

    @Override
    public void onFailed(TimelineIdentifier i, TimelineMessage m, Exception e) {
        // do something when failed.
    }
};

TimelineMessage newMessage = oldMessage;
newMessage.setField("text", "new value");
timelineQueue.updateAsync(sequenceId, newMessage, callback);
```

## Delete

根据SequenceId删除单行消息。

```
timelineQueue.delete(sequenceId);
```

## Scan

根据Scan参数正序（或逆序）范围读取单个Queue下的消息，返回Iterator<TimelineEntry\>，通过迭代器遍历。

```
ScanParameter scanParameter = new ScanParameter().scanBackward(Long.MAX_VALUE, 0);

timelineQueue.scan(scanParameter);
```

