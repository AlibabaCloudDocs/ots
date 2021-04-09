# Global secondary index

After you create index tables for a base table, you can read data from the index tables or delete specified index tables.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialize a Tablestore client](/intl.en-US/SDK Reference/Go SDK/Initialize a Tablestore client.md).
-   A table is created. The value of timeToLive is -1, and the value of maxVersions is 1.
-   Predefined columns are set for the table.

## Create an index table by calling the CreateIndex operation

You can call the CreateIndex operation to create an index table for an existing data table.

**Note:** You can also create one or more index tables when you create a data table by calling the CreateTable operation. For more information, see [Create tables](/intl.en-US/SDK Reference/Go SDK/Table/Create tables.md).

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |MainTableName|The name of the data table.|
    |IndexMeta|The schema information of the index table, which includes the following items:    -   IndexName: the name of the index table.
    -   PrimaryKey: the primary key of the index table, which is a combination of any primary key columns and predefined columns of the data table.
    -   DefinedColumns: the attribute column of the index table, which is a combination of predefined columns of the data table.
    -   indexType: the type of the index table. Only IT\_GLOBAL\_INDEX is supported. |
    |IncludeBaseData|Specifies whether the index table includes existing data in the data table.When IncludeBaseData is set to true, the index table includes the existing data in the data table. When IncludeBaseData is set to false, the index table does not include the existing data in the data table. |

-   Examples

    ```
    func CreateGlobalIndexSample(client *tablestore.TableStoreClient, tableName string) {
        indexMeta := new(tablestore.IndexMeta) // Create an index table named Meta.
        indexMeta.AddPrimaryKeyColumn("definedcol1") // Specify the definedcol1 column of the data table as a primary key column of the index table.
        indexMeta.AddDefinedColumn("definedcol2") // Specify the definedcol2 column of the data table as an attribute column of the index table.
        indexMeta.IndexName = "indexSample"
        indexReq := &tablestore.CreateIndexRequest{
            MainTableName:tableName, // Add the index table to the data table.
            IndexMeta: indexMeta,
            IncludeBaseData: true, // Specify that the index table includes existing data in the data table.
        }
        /**
          You can set the IncludeBaseData parameter to true to enable the synchronization of existing data in the data table after an index table is created. You can then query all data from the index table.
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


## Read data from the index table

You can read a row of data or read data within a specified range from the index table. If the columns to return are included in the index table, you can query the data from the index table. Otherwise, you must query the data table for the required data.

-   Read a row of data from the index table

    For more information, see [Single-row operations](/intl.en-US/SDK Reference/Go SDK/Single-row operations.md).

    When you use the GetRow operation to read data from the index table, take note of the following items:

    -   You must set tableName to the name of the index table.
    -   Tablestore autocompletes the primary keys for the index table. Therefore, when you specify the primary key of a row, you must also specify the columns based on which you create the index table and the autocompleted primary key columns.
-   Read data within a specified range from the index table

    For more information, see [Multi-row operations](/intl.en-US/SDK Reference/Go SDK/Multi-row operations.md).

    -   Parameters

        When you use the GetRange operation to read data from the index table, take note of the following items:

        -   You must set tableName to the name of the index table.
        -   Tablestore autocompletes the primary keys for the index table. Therefore, when you specify the start primary key and the end primary key of the range, you must also specify the columns based on which you create the index table and the autocompleted primary key columns.
    -   Examples

        ```
        func GetRangeFromIndex(client *tablestore.TableStoreClient, indexName string) {
            getRangeRequest := &tablestore.GetRangeRequest{}
            rangeRowQueryCriteria := &tablestore.RangeRowQueryCriteria{}
            rangeRowQueryCriteria.TableName = indexName
        
            startPK := new(tablestore.PrimaryKey)
            startPK.AddPrimaryKeyColumnWithMinValue("pk1") // Specify the first primary key column of the index table.
            startPK.AddPrimaryKeyColumnWithMinValue("pk2") // Specify the second primary key column of the index table.
            startPK.AddPrimaryKeyColumnWithMinValue("pk3") // Specify the third primary key column of the index table.
            endPK := new(tablestore.PrimaryKey)
            endPK.AddPrimaryKeyColumnWithMaxValue("pk1")
            endPK.AddPrimaryKeyColumnWithMaxValue("pk2")
            endPK.AddPrimaryKeyColumnWithMaxValue("pk3")
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


## Delete the index table by calling the DeleteIndex operation

You can call the DeleteIndex operation to delete a specified index table from a data table.

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


