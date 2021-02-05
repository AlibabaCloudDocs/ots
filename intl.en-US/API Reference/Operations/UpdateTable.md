# UpdateTable

You can call this operation to update the reserved read or write throughput settings of the specified table. New settings take effect within one minute after a successful update.

## Request structure

```
message UpdateTableRequest {
    required string table_name = 1;
    optional ReservedThroughput reserved_throughput = 2;
    optional TableOptions table_options = 3;
    optional StreamSpecification stream_spec = 4;
}
```

table\_name

-   Type: string

-   Required: yes

-   This parameter specifies the name of the table for which you want to modify the reserved read/write throughput settings.


reserved\_throughput

-   Type: [ReservedThroughput](/intl.en-US/API Reference/Data Types/ReservedThroughput.md)

-   Required: yes

-   This parameter specifies the reserved read/write throughput settings you want to modify for the table. New settings take effect within one minute after a successful update.

-   You may modify the reserved read or write throughput settings of the table as required.

-   You must specify a non-null value for either the read or write parameter of capacity\_unit. Otherwise, the request fails and an error is returned.


table\_options:

-   Type: [TableOptions](/intl.en-US/API Reference/Data Types/TableOptions.md)

-   Required: yes

-   This parameter specifies TimeToLive and MaxVersions.


StreamSpecification

-   Type: [StreamSpecification](/intl.en-US/API Reference/Data Types/StreamSpecification.md)

-   Required: no

-   This parameter specifies whether to enable attributes related to streams such as Stream.


## Response structure

```
message UpdateTableResponse {
    required ReservedThroughputDetails reserved_throughput_details = 1;
    required TableOptions table_options = 2;
}
```

capacity\_unit\_details

-   Type: [ReservedThroughputDetails](/intl.en-US/API Reference/Data Types/ReservedThroughputDetails.md)

-   After the update, the reserved read/write throughput settings information of the table contains the current reserved read/write throughput settings, the time these settings were last updated, and the number of times they have been lowered on the current date.


**Notice:**

-   The minimum interval between updates to the reserved read/write throughput of a table is two minutes. If the current UpdateTable operation is requested within two minutes of the previous operation, it will be rejected.
-   The reserved read/write throughput of a table can be raised or lowered an unlimited number of times within a single day \(from 00:00:00 to 00:00:00 the next day in UTC time\).

table\_options

-   Type: [TableOptions](/intl.en-US/API Reference/Data Types/TableOptions.md)

-   The latest value of the table\_options parameter after the update.


