# Update a table

You can call the UpdateTable operation to modify TableOptions and ReservedThroughput.

## Parameters

-   TableOptions

    TableOptions includes the TTL, MaxVersions, and MaxTimeDeviation parameters for a table.

    |Parameter|Definition|Description|
    |:--------|:---------|:----------|
    |TTL|The time to live, which indicates the lifespan of data.|    -   Unit: seconds.
    -   If you do not want data to expire, set TTL to -1.
    -   Tablestore checks whether data has expired based on the timestamp, current time, and TTL values. Data is expired and deleted if the following condition is met: `Current time - Data timestamp > Table TTL`.
    -   When you write data configured with TTL, make sure that you specify a valid timestamp. We recommend that you set MaxTimeDeviation to specify the timestamp. |
    |MaxTimeDeviation|The maximum version offset between the timestamp of written data and the current system time.|    -   By default, the system generates a timestamp for new data. The system checks whether data is expired based on the timestamp. You can specify the timestamp of written data. When the version offset between the timestamp of written data and the current system time is greater than the specified TTL, the written data immediately expires. To solve this problem, set MaxTimeDeviation.
    -   Unit: seconds. |
    |MaxVersions|The maximum number of versions that each attribute column can contain.|If the number of versions of an attribute column is greater than the value of MaxVersions, Tablestore keeps only the latest versions. The number of the kept versions is equal to the MaxVersions value.|

-   ReservedThroughtput

    The configuration of reserved read and write throughput of a table.

    -   The minimum interval of changing the value of ReservedThroughput is one minute.
    -   Fees are charged based on the reserved read and write throughput you have specified in the ReservedThroughtput field.
    -   If the ReservedThroughtput value is greater than 0, fees are charged based on the reserved throughput and duration. Tablestore charges additional reserved read and write throughput on a pay-as-you-go basis. For more information, see [Billing items and pricing](/intl.en-US/Pricing/Billing overview.md) to avoid unexpected fees.
    -   The default value 0 indicates that all throughput is billed on a pay-as-you-go basis.
    -   You can set the reserved read and write throughput of capacity instances only to 0. These instances do not allow reserved throughput.

## Examples

Update TTL and MaxVersions for the specified table.

```
private static void updateTable(SyncClient client) {
    int timeToLive = -1;
    int maxVersions = 5; // Update the value of MaxVersions to 5.
    TableOptions tableOptions = new TableOptions(timeToLive, maxVersions);
    UpdateTableRequest request = new UpdateTableRequest(TABLE_NAME);
    request.setTableOptionsForUpdate(tableOptions);
    client.updateTable(request);
}
```

