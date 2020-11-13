# Query the description of tables

You can call the DescribeTable operation to query the description such as the schema information and the reserved read and write throughput of a table.

**Note:** For more information about the DescribeTable operation, see [DescribeTable](/intl.en-US/API Reference/Operations/DescribeTable.md).

## Prerequisites

-   OTSClient is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A table is created.

## Operations

```
/**
 * Query the schema information, configuration information, reserved read and write throughput, and Stream configuration information of the specified table.
 * @api
 * @param [] $request The request parameters.
 * @return [] Response.
 * @throws OTSClientException The exception that is returned when a parameter error occurs or the Tablestore server returns a verification error.
 * @throws OTSServerException The exception that is returned when the Tablestore server returns an error.
 */
public function describeTable(array $request);
```

## Parameters

-   Request parameters

    |Parameter|Description|
    |---------|-----------|
    |table\_name|The name of the table.|

-   Request format

    ```
    $result = $client->describeTable([
         'table_name' => '<string>', // Specify the name of the table. The table name must be specified.
    ]);
    ```

-   Response parameters

    |Parameter|Description|
    |---------|-----------|
    |table\_meta|The schema information of the table, which includes the following items:    -   table\_name: the name of the table.
    -   primary\_key\_schema: the primary key of the table, which is the same as the primary key defined when the table is created. |
    |capacity\_unit\_details|The reserved read and write throughput of the table, which includes the following items:    -   capacity\_unit indicates the reserved read and write throughput configuration of the table, which is related to billing.
        -   read: the reserved read throughput.
        -   write: the reserved write throughput.
    -   last\_increase\_time: the time when the reserved read throughput or reserved write throughput was increased most recently for this table. The time is expressed in the number of seconds that have elapsed since 00:00:00, January 1, 1970 \(UTC\).
    -   last\_decrease\_time: the time when the reserved read throughput or reserved write throughput was decreased most recently for this table. The time is expressed in the number of seconds that have elapsed since 00:00:00, January 1, 1970 \(UTC\). |
    |table\_options|The schema information of the table, which includes the following items:    -   time\_to\_live: the period for which data in the table can be retained. This period is the validity period of data.
    -   max\_versions: the maximum number of versions of data that can be retained for a single attribute column.
    -   deviation\_cell\_version\_in\_sec: the max version offset, which is the maximum difference between the timestamp of the written data and the current system time. |
    |stream\_details|The Stream information of the table, which includes the following items:    -   enable\_stream: indicates whether Stream is enabled for the table.
    -   stream\_id: the Stream ID of the table.
    -   expiration\_time: the expiration time of the Stream data of the table. Unit: hour. Earlier modification records are deleted.
    -   last\_enable\_time: the time when Stream was enabled most recently. |

-   Response format

    ```
    [
        'table_meta' => [
            'table_name' => '<string>',
            'primary_key_schema' => [
                ['<string>', <PrimaryKeyType>],
                ['<string>', <PrimaryKeyType>, <PrimaryKeyOption>]
            ]
        ],
        'capacity_unit_details' => [
            'capacity_unit' => [
                'read' => <integer>,
                'write' => <integer>
            ],
            'last_increase_time' => <integer>,
            'last_decrease_time' => <integer>
        ],
        'table_options' => [
            'time_to_live' => <integer>,
            'max_versions' => <integer>,
            'deviation_cell_version_in_sec => <integer>
        ],
        'stream_details' => [
            'enable_stream' => true || false,
            'stream_id' => '<string>',
            'expiration_time' => <integer>,
            'last_enable_time' => <integer>
        ]
    ]        
    ```


## Examples

The following code provides an example on how to query the description of a table:

```
$result = $client->describeTable([
        'table_name' => 'mySampleTable',
        ]);
        var_dump($result);    
```

