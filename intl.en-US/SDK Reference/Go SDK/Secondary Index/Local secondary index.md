# Local secondary index

After you create index tables for a base table, you can read data from the index tables or delete specified index tables.

## Prerequisites

-   Client is initialized. For more information, see [Initialize a Tablestore client](/intl.en-US/SDK Reference/Go SDK/Initialize a Tablestore client.md).
-   A data table is created. The value of TimeToLive is -1. The value of MaxVersions is 1.
-   Predefined columns are configured for the data table.

## Create an index table by calling the CreateIndex operation

You can call the CreateIndex operation to create an index table for an existing data table.

**Note:** You can call the CreateTable operation to create one or more index tables when you create a data table. For more information, see [Create data tables](/intl.en-US/SDK Reference/Go SDK/Table/Create data tables.md).

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |MainTableName|The name of the data table.|
    |IndexMeta|The schema information of the index table. The schema information contains the following items:    -   IndexName: the name of the index table.
    -   PrimaryKey: the indexed columns of the index table. The indexed columns are a combination of primary key columns and predefined columns of the data table.

If you use the local secondary index feature, the first primary key column of an index table must be the same as the first primary key column of the corresponding data table.

    -   DefinedColumns: the attribute columns of the index table. The attribute columns are a combination of predefined columns of the data table.
    -   IndexType: the type of the index. Valid values: IT\_GLOBAL\_INDEX and IT\_LOCAL\_INDEX.
        -   If IndexType is not specified or is set to IT\_GLOBAL\_INDEX, the global secondary index feature is used.

When you use a global secondary index, Tablestore automatically synchronizes the columns to be indexed and the data in primary key columns from a data table to an index table in asynchronous mode. The synchronization latency is within a few milliseconds.

        -   If IndexType is set to IT\_LOCAL\_INDEX, the local secondary index feature is used.

If you use the local secondary index feature, Tablestore automatically synchronizes data from the indexed columns and the primary key columns of a data table to the columns of an index table in synchronous mode. After the data is written to the data table, you can query the data from the index table. |
    |IncludeBaseData|Specifies whether to include the existing data from the data table in the index table. When IncludeBaseData is set to true, the index table contains the existing data in the data table. When IncludeBaseData is set to false, the index table does not contain the existing data in the data table. |

-   Examples

    The following code provides an example on how to create an index table for a data table. The data table contains primary key columns pk1 and pk2. The index table contains primary key columns pk1 and definedcol1 and attribute column definedcol2.

    ```
    func CreateGlobalIndexSample(client *tablestore.TableStoreClient, tableName string) {
        indexMeta := new(tablestore.IndexMeta) // Create IndexMeta for the index table. 
        indexMeta.AddPrimaryKeyColumn("pk1") /// The first primary key column of an index table must be the same as that of the corresponding data table. 
        indexMeta.AddPrimaryKeyColumn("definedcol1") // Specify the definedcol1 column of the data table as the primary key column of the index table. 
        indexMeta.AddDefinedColumn("definedcol2") // Specify the definedcol2 column of the data table as the attribute column of the index table. 
        indexMeta.IndexType = IT_LOCAL_INDEX // Set the index type to IT_LOCAL_INDEX. 
        indexMeta.IndexName = "indexSample"
        indexReq := &tablestore.CreateIndexRequest{
            MainTableName:tableName, // Specify the data table to create the index table on the data table. 
            IndexMeta: indexMeta,
            IncludeBaseData: true, // Specify that the index table contains the existing data in the data table. 
        }
        /**
          You can set the IncludeBaseData parameter to true to synchronize existing data in a data table after an index table is created. Then, you can query all data from the index table. 
          The amount of time required to synchronize data to the index table is determined by the amount of data in the data table. 
        */  
        resp, err := client.CreateIndex(indexReq)
        if err != nil {
            fmt.Println("Failed to create table with error:", err)
        } else {
            fmt.Println("Create index finished", resp)
        }
    }
    ```


## Read data from an index table

-   Read a row of data from an index table

    For more information, see [Single-row operations](/intl.en-US/SDK Reference/Go SDK/Single-row operations.md).

    When you call the GetRow operation to read data from an index table, take note of the following items:

    -   You must set TableName to the name of the index table.
    -   Tablestore automatically adds the primary key columns that are not configured in the indexed columns to an index table. Therefore, when you specify the primary key columns of a row, you must specify the indexed columns based on which you create the index table and the primary key columns of the data table.
-   Read data within a specified range from an index table

    For more information, see [Multi-row operations](/intl.en-US/SDK Reference/Go SDK/Multi-row operations.md).

    -   Parameters

        When you call the GetRange operation to read data from an index table, take note of the following items:

        -   You must set TableName to the name of the index table.
        -   Tablestore automatically adds the primary key columns that are not specified as indexed columns to an index table. Therefore, when you specify the start primary key and end primary key, you must specify the indexed columns based on which you create the index table and the primary key columns of the data table.
    -   Examples

        ```
        func GetRangeFromIndex(client *tablestore.TableStoreClient, indexName string) {
            getRangeRequest := &tablestore.GetRangeRequest{}
            rangeRowQueryCriteria := &tablestore.RangeRowQueryCriteria{}
            rangeRowQueryCriteria.TableName = indexName
        
            startPK := new(tablestore.PrimaryKey)
            startPK.AddPrimaryKeyColumnWithMinValue("pk1") // Specify the first primary key column of the index table. 
            startPK.AddPrimaryKeyColumnWithMinValue("definedcol1") // Specify the second primary key column of the index table. 
            startPK.AddPrimaryKeyColumnWithMinValue("pk2") // Specify the third primary key column of the index table, which is the primary key column that is automatically added from the data table. 
        
            endPK := new(tablestore.PrimaryKey)
            endPK.AddPrimaryKeyColumnWithMaxValue("pk1")
            endPK.AddPrimaryKeyColumnWithMaxValue("definedcol1")  
            endPK.AddPrimaryKeyColumnWithMaxValue("pk2")
            rangeRowQueryCriteria.StartPrimaryKey = startPK
            rangeRowQueryCriteria.EndPrimaryKey = endPK
            rangeRowQueryCriteria.Direction = tablestore.FORWARD
            rangeRowQueryCriteria.MaxVersion = 1
            rangeRowQueryCriteria.Limit = 10
            getRangeRequest.RangeRowQueryCriteria = rangeRowQueryCriteria
        
            getRangeResp, err := client.GetRange(getRangeRequest)
        
            fmt.Println("get range result is ", getRangeResp)
        
            for {
                if err != nil {
                    fmt.Println("get range failed with error:", err)
                }
                if len(getRangeResp.Rows) > 0 {
                    for _, row := range getRangeResp.Rows {
                        fmt.Println("range get row with key", row.PrimaryKey.PrimaryKeys[0].Value, row.PrimaryKey.PrimaryKeys[1].Value, row.PrimaryKey.PrimaryKeys[2].Value)
                    }
                    if getRangeResp.NextStartPrimaryKey == nil {
                        break
                    } else {
                        fmt.Println("next pk is :", getRangeResp.NextStartPrimaryKey.PrimaryKeys[0].Value, getRangeResp.NextStartPrimaryKey.PrimaryKeys[1].Value, getRangeResp.NextStartPrimaryKey.PrimaryKeys[2].Value)
                        getRangeRequest.RangeRowQueryCriteria.StartPrimaryKey = getRangeResp.NextStartPrimaryKey
                        getRangeResp, err = client.GetRange(getRangeRequest)
                    }
                } else {
                    break
                }
        
                fmt.Println("continue to query rows")
            }
            fmt.Println("putrow finished")
        }
        ```


## Delete an index table by calling the DeleteIndex operation

You can call the DeleteIndex operation to delete the specified index table from the corresponding data table.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |MainTableName|The name of the data table.|
    |IndexName|The name of the index table.|

-   Examples

    ```
    func DeleteIndex(client *tablestore.TableStoreClient, tableName string, indexName string) { 
        deleteIndex := &tablestore.DeleteIndexRequest{ MainTableName:tableName, IndexName: indexName }
        resp, err := client.DeleteIndex(deleteIndex)
    
        if err != nil {
            fmt.Println("Failed to delete index:", err)
        } else {
            fmt.Println("drop index finished", resp)
        }
    ```


