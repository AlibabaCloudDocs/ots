# Predefined columns

This topic describes how to add predefined columns to or delete predefined columns from a table. You can set predefined columns to the indexed columns or indexed attribute columns when you create a global secondary index.

## Prerequisites

-   OTSClient is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialization.md).
-   A table is created.

## Add predefined columns

When you create a global secondary index for a base table, if the base table does not contain predefined columns or the existing predefined columns are not suitable for creating a global secondary index, you can add predefined columns to the base table.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |tableName|The name of the base table.|
    |definedColumns|The name of the predefined columns and the data types of the predefined column values. The predefined columns cannot be primary key columns. You can set predefined columns to the indexed columns or indexed attribute columns for the global secondary index. This parameter includes the following configurations:    -   name: the name of the predefined column.
    -   type: the data type of the predefined column value. |

-   Examples

    Add the following predefined columns to the base table named sampleTable: definedColumnName01 \(type: STRING\), definedColumnName02 \(type: INTEGER\), and definedColumnName03 \(type: STRING\).

    ```
    public static void addDefinedColumnRequest(SyncClient client) {
    AddDefinedColumnRequest addDefinedColumnRequest = new AddDefinedColumnRequest();
    addDefinedColumnRequest.setTableName("sampleTable");
    addDefinedColumnRequest.addDefinedColumn("definedColumnName01",DefinedColumnType.STRING);
    addDefinedColumnRequest.addDefinedColumn("definedColumnName02",DefinedColumnType.INTEGER);
    addDefinedColumnRequest.addDefinedColumn("definedColumnName03",DefinedColumnType.STRING);
    client.addDefinedColumn(addDefinedColumnRequest);
    }
    ```


## Delete predefined columns

You can delete predefined columns that you no longer use from a base table.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |tableName|The name of the base table.|
    |name|The name of the predefined column.|

-   Examples

    The following code provides an example on how to delete the predefined columns definedColumnName01 and definedColumnName02 from the base table sampleTable:

    ```
    public static void deleteDefinedColumnRequest(SyncClient client){
    DeleteDefinedColumnRequest deleteDefinedColumnRequest = new DeleteDefinedColumnRequest();
    deleteDefinedColumnRequest.setTableName("sampleTable");
    deleteDefinedColumnRequest.addDefinedColumn("definedColumnName01");
    deleteDefinedColumnRequest.addDefinedColumn("definedColumnName02");
    client.deleteDefinedColumn(deleteDefinedColumnRequest);
    }
    ```


