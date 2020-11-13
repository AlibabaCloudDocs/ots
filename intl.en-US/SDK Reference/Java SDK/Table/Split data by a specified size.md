# Split data by a specified size

You can call the ComputeSplitsBySize operation to logically splits the data in a table into multiple shards whose sizes are close to a specified value. The locations of the split points among these splits and the hosts where the shards reside are returned. This operation is typically used to implement plans on compute engines, such as concurrency plans.

**Note:** For more information about ComputeSplitsBySize, see [ComputeSplitPointsBySize](/intl.en-US/API Reference/Operations/ComputeSplitPointsBySize.md).

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialize SDK for Java.md).
-   A data table is created. Data is written to the table.

## Parameters

|Parameter|Description|
|---------|-----------|
|tableName|The name of the base table.|
|splitSize|The approximate size of each shard.Unit: 100 MB. |

## Examples

The following code provides an example on how to logically splits the data in a table into multiple shards whose sizes are close to 200 MB:

```
private static void describeTable(SyncClient client) {
    // Splits the data in the table into multiple shards based on the size of 200 MB.
    ComputeSplitsBySizeRequest request = new ComputeSplitsBySizeRequest(TABLE_NAME, 2);
    ComputeSplitsBySizeResponse response = client.computeSplitsBySize(computeSplitsBySizeRequest);
    System.out.println("ConsumedCapacity="   response.getConsumedCapacity().jsonize());
    System.out.println("PrimaryKeySchema="   response.getPrimaryKeySchema());
    System.out.println("RequestId="   response.getRequestId());
    System.out.println("TraceId="   response.getTraceId());
    List<Split> splits = response.getSplits();
    System.out.println("splits.size="   splits.size());
    Iterator<Split> iterator = splits.iterator();
    while (iterator.hasNext()) {
        Split split = iterator.next();
        System.out.println("split.getLocation()="   split.getLocation());
        // You can inject split.getLowerBound() and split.getUpperBound() into RangeRowQueryCriteria, and pass RangeRowQueryCriteria to getRange() or createRangeIterator().
        System.out.println("split.getLowerBound()="   split.getLowerBound().jsonize());
        System.out.println("split.getUpperBound()="   split.getUpperBound().jsonize());
    }
}
```

