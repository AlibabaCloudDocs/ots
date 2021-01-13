# UpdateTable

This topic describes the UpdateTable operation.

## UpdateTable

API description: [UpdateTable](/intl.en-US/API Reference/Operations/UpdateTable.md)

You can call the UpdateTable operation to update ReservedThroughput, TableOptions, and StreamSpecification of a table.

For more information about ReservedThroughput, TableOptions, and StreamSpecification, see the topic about how to create a table. Currently, the interval between attempts to modify the value of ReservedThroughput is a minimum of 1 minute.

Operations

```
    /**
     * Update a table, including the reserved read throughput and reserved write throughput, configuration information, and Stream configuration of the table.
     * This method can be used to increase or decrease the reserved read throughput and reserved write throughput.
     * @api
     * @param [] $request Request parameters.
     * @return [] The response. 
     * @throws OTSClientException The exception that occurs when a parameter error occurs or the Tablestore server returns a verification error.
     * @throws OTSServerException The exception that occurs when the Tablestore server returns an error.
     */
    public function updateTable(array $request);
			
```

Request format

```
$result = $client->updateTable([
    'table_name' => '<string>',         // REQUIRED
    'reserved_throughput' => [         
        'capacity_unit' => [
            'read' => <integer>, 
            'write' => <integer>
        ]
    ],
    'table_options' => [ 
        'time_to_live' => <integer>,   
        'max_versions' => <integer>,    
        'deviation_cell_version_in_sec' => <integer>  
    ],
    'stream_spec' => [
        'enable_stream' => true || false,
        'expiration_time' => <integer>
    ]
]);    
```

Request format description

-   The parameters of UpdateTable are defined and configured in the same way as those of CreateTable. However, UpdateTable does not have the table\_meta parameter. Except table\_name, all other parameters are optional.
-   table\_name: required. The table name.
-   reserved\_throughput: optional. The reserved read throughput and reserved write throughput, which are related to billing.
    -   capacity\_unit: the reserved read throughput and reserved write throughput. When the reserved read throughput or reserved write throughput is greater than 0, you are charged based on the reserved volume and duration. Traffic that exceeds the reserved volume is billed on a pay-as-you-go basis. By default, both the reserved read throughput and reserved write throughput are 0. That is, all traffic is billed on a pay-as-you-go basis by default. To set a value greater than 0, we recommend that you read the documentation about Tablestore billing to avoid unexpected fees. You can set the reserved read throughput and reserved write throughput of capacity instances only to 0. Throughput reservation is not allowed for these instances.
        -   read: the reserved read throughput.
        -   write: the reserved write throughput.
-   table\_options: optional. The TTL, MaxVersions, and MaxTimeDeviation configurations of the table.
    -   time\_to\_live: the retention time of data. Unit: seconds.
        -   The latest version of Tablestore API supports automatic data expiration. If you do not want data to expire, set time\_to\_live to -1.
        -   Tablestore checks whether data has expired based on the data timestamp, current time, and table TTL. If the difference between the current time and the data timestamp is greater than the table TTL, data expires and is cleared by the Tablestore server. For more information about the data timestamp, see related documentations.
        -   After the TTL is set, the server checks whether data expires based on the data timestamp. If you specify a timestamp when you write data and the timestamp is significantly different from the actual time, unexpected data expiration may occur. For example, if the specified data timestamp is too small, the data may expire and be deleted as soon as it is written. If the specified timestamp is too large, the data may not expire when required. Therefore, when writing data with the TTL specified, make sure that you set a reasonable timestamp.
    -   max\_versions: the maximum number of versions retained by each attribute column.
        -   The max\_versions parameter is used to specify the maximum number of data versions saved by each attribute column. If a new version causes the total number of versions to exceed the value of max\_versions, the server deletes the earliest version to ensure that the number of versions do not exceed this value.
    -   deviation\_cell\_version\_in\_sec: the maximum deviation allowed between the specified version and the current system time when data is written to the version. Unit: seconds.
        -   Tablestore supports multiple versions. By default, the system generates a version number for newly written data. The version number is a timestamp corresponding to the write time in milliseconds. The TTL feature uses this timestamp to determine if data has expired. In addition, you can specify the data writing timestamp. If you set an extremely small timestamp, which deviates from the current time by more than the TTL set for the table, the written data immediately expires. To prevent immediate data expiration, Tablestore provides the deviation\_cell\_version\_in\_sec parameter to limit the permissible deviation between the data writing timestamp and the current system time. The unit of this parameter is second. You can specify this parameter when you create a table and modify it by using the UpdateTable operation.
-   stream\_spec: optional. The Stream-related settings.
    -   enable\_stream: specifies whether to enable Stream.
    -   expiration\_time: the expiration time of the Stream data of the table. Unit: hours. Earlier modification records are deleted.

Response format

```
[
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

Response format description

-   capacity\_unit\_details: the reserved read/write throughput configuration, which is related to billing.
    -   capacity\_unit: the reserved read throughput and reserved write throughput. When the reserved read throughput or reserved write throughput is greater than 0, you are charged based on the reserved volume and duration. Traffic that exceeds the reserved volume is billed on a pay-as-you-go basis. By default, both the reserved read throughput and reserved write throughput are 0. That is, all traffic is billed on a pay-as-you-go basis by default. To set a value greater than 0, we recommend that you read the documentation about Tablestore billing to avoid unexpected fees. You can set the reserved read throughput and reserved write throughput of capacity instances only to 0. Throughput reservation is not allowed for these instances.
        -   read: the reserved read throughput.
        -   write: the reserved write throughput.
    -   last\_increase\_time: the time when the reserved read throughput or reserved write throughput was increased most recently for this table. The time is expressed in the number of seconds that have elapsed since 00:00:00, January 1, 1970 \(UTC\).
    -   last\_decrease\_time: the time when the reserved read throughput or reserved write throughput was decreased most recently for this table. The time is expressed in the number of seconds that have elapsed since 00:00:00, January 1, 1970 \(UTC\).
-   table\_options: the TTL, MaxVersions, and MaxTimeDeviation configurations of the table, The options are the same as those in the request.
-   stream\_details: the Stream information of the table.
    -   enable\_stream: indicates whether Stream is enabled for the table.
    -   stream\_id: the Stream ID of the table.
    -   expiration\_time: the expiration time of the Stream data of the table. Unit: hours. Earlier modification records are deleted.
    -   last\_enable\_time: the time when Stream was enabled most recently.

Examples

The following code provides an example on how to update the numbers of read CUs of a table to 1 and the numbers of write CUs to 2:

```
    $result = $client->updateTable([
        'table_name' => 'SampleTable',
        'reserved_throughput' => [         
            'capacity_unit' => [
                'read' => 1,            // The number of read or write CUs can be separately updated.
                'write' => 2
            ]
        ]
    ]);
			
```

The following code provides an example on how to set the TTL of a table to 86,400s \(one day\), retain two versions, and set the maximum deviation to 10s:

```
    $result = $client->updateTable([
        'table_name' => 'SampleTable',
        'table_options' => [ 
            'time_to_live' => 86400,   
            'max_versions' => 2,    
            'deviation_cell_version_in_sec' => 10  
        ]
    ]);
			
```

The following code provides an example on how to enable Stream for a table and set the expiration time to 24 hours:

```
    $result = $client->updateTable([
        'table_name' => 'SampleTable',
        'stream_spec' => [
            'enable_stream' => true,
            'expiration_time' => 24
        ]
    ]);
			
```

