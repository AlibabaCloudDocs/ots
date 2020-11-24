# Global secondary index

After you create index tables for a base table, you can read data from the index tables or delete specified index tables.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/.NET SDK/Initialization.md).
-   A table is created. The value of timeToLive is -1. The value of maxVersions is 1.
-   Predefined columns are set for the table.

## Create an index table by calling the CreateGlobalIndex operation

You can call the CreateIndex operation to create an index table for an existing base table.

**Note:** You can also create one or more index tables when you create a base table by calling the CreateTable operation. For more information, see [Create tables](/intl.en-US/SDK Reference/.NET SDK/Table operations/Create tables.md).

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |mainTableName|The name of the table.|
    |indexMeta|The schema information of the index table, which includes the following items:    -   IndexName: the name of the index table.
    -   primaryKey: the primary key of the index table, which is a combination of primary key columns and predefined columns of the base table.
    -   definedColumns: the indexed attribute column, which is a combination of predefined columns of the base table.
    -   indexUpdateMode: the update mode of the index table. Only IUM\_ASYNC\_INDEX is supported.
    -   indexType: the type of the index table. Only IT\_GLOBAL\_INDEX is supported. |
    |includeBaseData|Specifies whether the index table includes the existing data in the base table.When the includeBaseData parameter is set to true, the index includes the existing data. When the value is false, the index excludes the existing data. |

-   Examples

    ```
    public static void CreateGlobalIndex()
    {
        OTSClient otsClient = Config.GetClient();
    
        Console.WriteLine("Start create globalIndex...") ;
    
        IndexMeta indexMeta = new IndexMeta(IndexName2);
        indexMeta.PrimaryKey = new List<string>() { Col2 };
        indexMeta.DefinedColumns = new List<string>() { Pk1 };
    
    
        CapacityUnit reservedThroughput = new CapacityUnit(0, 0);
        CreateGlobalIndexRequest request = new CreateGlobalIndexRequest(TableName, indexMeta);
        otsClient.CreateGlobalIndex(request);
    
        Console.WriteLine("Global Index is created,tableName: " + TableName + ",IndexName:" + IndexName2);
    
     }
    ```


## Read data from the index table

You can read a row of data or read data within a specified range from the index table. If the columns to return are included in the index table, you can query the data from the index table. Otherwise, you must query the base table for the required data.

-   Read a row of data from the index table

    For more information, see [Single-row operations](/intl.en-US/SDK Reference/.NET SDK/Single-row operations.md).

    When you use the GetRow operation to read data from the index table, take note of the following items:

    -   You must set tableName to the name of the index table.
    -   Tablestore autocompletes the primary key that is not configured in the index columns for the index table. Therefore, when you specify the primary key of a row, you must specify the indexed columns based on which you create the index table and the autocompleted primary key columns.
-   Read data within a specified range from the index table

    For more information, see [Multi-row operations](/intl.en-US/SDK Reference/.NET SDK/Multi-row operations.md).

    -   Parameters

        When you use the GetRange operation to read data from the index table, take note of the following items:

        -   You must set tableName to the name of the index table.
        -   Tablestore autocompletes the primary key that is not configured in the index columns for the index table. Therefore, when you specify the start primary key and the end primary key of the range, you must specify the indexed columns based on which you create the index table and the autocompleted primary key columns.
    -   Examples

        ```
        public static void GetRangeFromIndexTable()
        {
            Console.WriteLine("Start getRange from index...") ;
            OTSClient otsClient = Config.GetClient();
            // Specify the value of the first primary key column Col1 and read the data.
            PrimaryKey inclusiveStartPrimaryKey = new PrimaryKey
            {
                { Col1, new ColumnValue("Col1Value") },
                { Pk1,  ColumnValue.INF_MIN },
                { Pk2, ColumnValue.INF_MIN }
            };
        
            PrimaryKey exclusiveEndPrimaryKey = new PrimaryKey
            {
                { Col1, new ColumnValue("Col1Value") },
                { Pk1,  ColumnValue.INF_MAX },
                { Pk2, ColumnValue.INF_MAX }
            };
        
            GetRangeRequest request = new GetRangeRequest(IndexName, GetRangeDirection.Forward, inclusiveStartPrimaryKey, exclusiveEndPrimaryKey);
        
            GetRangeResponse response = otsClient.GetRange(request);
            IList<Row> rows = response.RowDataList;
            PrimaryKey nextStartPrimaryKey = response.NextPrimaryKey;
            while (nextStartPrimaryKey ! = null)
            {
                request = new GetRangeRequest(TableName, GetRangeDirection.Forward, nextStartPrimaryKey, exclusiveEndPrimaryKey);
                response = otsClient.GetRange(request);
                nextStartPrimaryKey = response.NextPrimaryKey;
                foreach (var row in response.RowDataList)
                {
                    rows.Add(row);
                }
            }
        
            foreach (var row in rows)
            {
                PrintRow(row);
            }
        
            Console.WriteLine("TotalRowsRead: " + rows.Count);
        }
        
        private static void PrintRow(Row row)
        {
            Console.WriteLine("-----------------");
        
            foreach (KeyValuePair<string, ColumnValue> entry in row.GetPrimaryKey())
            {
                Console.WriteLine(entry.Key + ":" + PrintColumnValue(entry.Value));
            }
        
            foreach (Column entry in row.GetColumns())
            {
                Console.WriteLine(entry.Name + ":" + PrintColumnValue(entry.Value));
            }
        
            Console.WriteLine("-----------------");
        }             
        ```


## Delete the index table by calling the DeleteGlobalIndex operation

You can call the DeleteIndex operation to delete a specified index table from a base table.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |mainTableName|The name of the table.|
    |indexName|The name of the index table.|

-   Examples

    ```
    public static void DeleteGlobalIndex()
    {
        OTSClient otsClient = Config.GetClient();
    
        Console.WriteLine("Start delete globalIndex...") ;
    
        DeleteGlobalIndexRequest request = new DeleteGlobalIndexRequest(TableName, IndexName);
        otsClient.DeleteGlobalIndex(request);
    
        DeleteGlobalIndexRequest request2 = new DeleteGlobalIndexRequest(TableName, IndexName2);
        otsClient.DeleteGlobalIndex(request2);
    
        Console.WriteLine("Global Index is deleted,tableName: " + TableName + ",IndexName:" + IndexName + "," + IndexName2);
    
    }
    ```


