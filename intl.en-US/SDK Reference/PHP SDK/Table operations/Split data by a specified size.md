# Split data by a specified size

You can call the ComputeSplitsBySize operation to logically splits the data in a table into multiple shards whose sizes are close to a specified value. The locations of the split points among these splits and the hosts where the shards reside are returned. This operation is typically used to implement plans on compute engines, such as concurrency plans.

**Note:** For more information about the ComputeSplitsBySize operation, see [ComputeSplitPointsBySize](/intl.en-US/API Reference/Operations/ComputeSplitPointsBySize.md).

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A data table is created. Data is written to the table.

## Operations

```
    /**
     * Logically split data in a table into several partitions whose sizes are close to the specified size, and return the split points between partitions and prompt about hosts where the partitions reside.
     * This operation is generally used for execution plans on computing engines, such as concurrency plans.
     * @api
     * @param [] $request The request parameters.
     * @return [] Response.
     * @throws OTSClientException The exception that is returned when a parameter error occurs or the Tablestore server returns a verification error.
     * @throws OTSServerException The exception that is returned when the Tablestore server returns an error.
     */
    public function computeSplitPointsBySize(array $request)
            
```

## Parameters

-   Request parameters

    |Parameter|Description|
    |---------|-----------|
    |table\_name|The name of the table.|
    |split\_size|The approximate size of each partition.Unit: 100 MB. |

-   Request format

    ```
    $result = $client->ComputeSplitsBySize([
        'table_name' => '<string>', // Specify the name of the table. This parameter is required.
        'Split_size ' =>  // Set the part size. This parameter is required.
    ]);     
    ```

-   Response parameters

    |Parameter|Description|
    |---------|-----------|
    |consumed|The value of capacity units consumed by this operation. capacity\_unit indicates the number of read and write CUs consumed.

    -   read: the reserved read throughput.
    -   write: the reserved write throughput. |
    |primary\_key\_schema|The schema of the primary key for the table, which is the same as that specified when the table is created.|
    |splits|The split points between partitions, which includes the following items:    -   lower\_bound: the minimum value during the range of the primary key.

The lower\_bound value can be passed to GetRange to range read data.

        -   Each item contains the primary key name, primary key value \(PrimaryKeyValue\), and primary key type \(PrimaryKeyType\) in sequence.
        -   PrimaryKeyType can be set to PrimaryKeyTypeConst::CONST\_INTEGER, PrimaryKeyTypeConst::CONST\_STRING, PrimaryKeyTypeConst::CONST\_BINARY, PrimaryKeyTypeConst::CONST\_INF\_MIN, or PrimaryKeyTypeConst::CONST\_INF\_MAX, which separately indicate the INTEGER, STRING \(UTF-8 encoded string\), BINARY, INF\_MIN\(-inf\), and INF\_MAX\(inf\) types.
    -   upper\_bound: the maximum value during the range of the primary key. The format of upper\_bound is same as that of lower\_bound.

The upper\_bound value can be passed to GetRange to read data by range.

    -   location: the machine where the split point is located. The value of this parameter can be empty. |

-   Response format

    ```
    [
        'consumed' => [
            'capacity_unit' => [
                'read' => <integer>,
                'write' => <integer>
            ]
        ],
        'primary_key_schema' => [
            ['<string>', <PrimaryKeyType>],
            ['<string>', <PrimaryKeyType>, <PrimaryKeyOption>]
        ]
        'splits' => [
            [ 
                'lower_bound' => [
                    ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>],
                    ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
                ],
                'upper_bound' => [
                    ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>],
                    ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
                ],
                'location' => '<string>'
            ],
            // ...
        ]
    ]           
    ```


## Examples

The following code provides an example on how to logically splits the data in a table into multiple partitions whose sizes are close to 100 MB:

```
    $result = $client->ComputeSplitsBySize([
        'table_name' => 'MyTable', 
        'split_size' => 1
    ]);
    foreach($result['splits'] as $split) {
        print_r($split['location']);    
        print_r($split['lower_bound']);    
        print_r($split['upper_bound']);    
    }       
```

