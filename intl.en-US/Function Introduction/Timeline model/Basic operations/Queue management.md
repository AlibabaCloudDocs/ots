# Queue management

This topic describes how to manage queues when you use the Timeline model.

## Obtain a Queue instance

A Queue is an abstract of a message queue. A Queue corresponds to all messages of an identifier of a TimelineStore. You can call the required operation of TimelineStore to create a Queue instance.

```
TimelineIdentifier identifier = new TimelineIdentifier.Builder()
        .addField("timeline_id", "group_1")
        .build();

// The Queue corresponds to an identifier of a TimelineStore.
TimelineQueue timelineQueue = timelineStore.createTimelineQueue(identifier);
```

The Queue instance manages a message queue that corresponds to an identifier of a TimelineStore. This instance provides operations such as Store, StoreAsync, BatchStore, Delete, Update, UpdateAsync, Get, and Scan.

## Store

You can call this operation to synchronously store messages. To use this operation, you can set SequenceId manually or by using an auto-increment column.

```
timelineQueue.store(message);//Generate the SequenceId value by using an auto-increment column.
timelineQueue.store(sequenceId, message);//Manually set the SequenceId value.
```

## StoreAsync

You can call this operation to asynchronously store messages. You can customize callbacks to process successful or failed storage. This operation returns Future<TimelineEntry\>.

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

timelineQueue.storeAsync(message, callback);//Generate the SequenceId value by using an auto-increment column.
timelineQueue.storeAsync(sequenceId, message, callback);//Manually set the SequenceId value.
```

## BatchStore

You can call this operation to store multiple messages in the callback and non-callback ways. You can customize callbacks to process successful or failed storage.

```
timelineQueue.batchStore(message);//Generate the SequenceId value by using an auto-increment column.
timelineQueue.batchStore(sequenceId, message);//Manually set the SequenceId value.

timelineQueue.batchStore(message, callback);//Generate the SequenceId value by using an auto-increment column.
timelineQueue.batchStore(sequenceId, message, callback);//Manually set the SequenceId value.
```

## Get

You can call this operation to read a single row based on the SequenceId value. If no messages exist, no error occurs and the system returns an empty string.

```
timelineQueue.get(sequenceId);
```

## GetLatestTimelineEntry

You can call this operation to read the latest message. If no messages exist, no error occurs and the system returns an empty string.

```
timelineQueue.getLatestTimelineEntry();
```

## GetLatestSequenceId

You can call this operation to obtain the SequenceId value of the latest message. If no messages exist, no error occurs and the system returns 0.

```
timelineQueue.getLatestSequenceId();
```

## Update

You can call this operation to synchronously update a message based on the SequenceId value.

```
TimelineMessage message = new TimelineMessage().setField("text", "Timeline is fine.");

//update message with new field
message.setField("text", "new value");
timelineQueue.update(sequenceId, message);
```

## UpdateAsync

You can call this operation to asynchronously update a message based on the SequenceId value. You can customize callbacks to process a successful or failed update. This operation returns Future<TimelineEntry\>.

```
TimelineMessage oldMessage = new TimelineMessage().setField("text", "Timeline is fine.") ;
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

You can call this operation to delete one row based on the SequenceId value.

```
timelineQueue.delete(sequenceId);
```

## Scan

You can call this operation to read messages in a single Queue in order forwards or backwards based on the Scan parameter. This operation returns Iterator<TimelineEntry\>. You can iterate all result sets by using the iterator.

```
ScanParameter scanParameter = new ScanParameter().scanBackward(Long.MAX_VALUE, 0);

timelineQueue.scan(scanParameter);
```

