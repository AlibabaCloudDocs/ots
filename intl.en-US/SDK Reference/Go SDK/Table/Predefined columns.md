# Predefined columns

This topic describes how to add predefined columns to or delete predefined columns from a table. You can set predefined columns to the indexed columns or indexed attribute columns when you create a secondary index.

## Prerequisites

-   TableStoreClient is initialized. For more information, see [Initialize a Tablestore client](/intl.en-US/SDK Reference/Go SDK/Initialize a Tablestore client.md).
-   A table is created.

## Add predefined columns

When you create a secondary index for a base table, if the base table does not contain predefined columns or the existing predefined columns are not suitable for creating a secondary index, you can add predefined columns to the base table.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |TableName|The name of the base table.|
    |DefinedColumns|The name of the predefined columns and the data types of the predefined column values. The predefined columns cannot be primary key columns. You can set predefined columns to the indexed columns or indexed attribute columns for the secondary index. This parameter includes the following configurations:    -   Name: the name of the predefined column.
    -   ColumnType: the data type of the predefined column value. |

-   Examples

    Add the following predefined columns to the base table named sampleTable: definedColumnName01 \(type: STRING\), definedColumnName02 \(type: INTEGER\), and definedColumnName03 \(type: STRING\).

    ```
    func AddDefinedColumn() {
        client := new(tablestore.TableStoreClient)
        addDefinedColumnRequest := new(tablestore.AddDefinedColumnRequest)
        addDefinedColumnRequest.AddDefinedColumn("definedColumnName01",tablestore.DefinedColumn_STRING)
        addDefinedColumnRequest.AddDefinedColumn("definedColumnName02",tablestore.DefinedColumn_INTEGER)
        addDefinedColumnRequest.AddDefinedColumn("definedColumnName03",tablestore.DefinedColumn_STRING)
        addDefinedColumnRequest.TableName = "sampleTable"
        _, err := client.AddDefinedColumn(addDefinedColumnRequest)
        if (err != nil) {
            fmt.Println("Failed to Add DefinedColumn with error:", err)
        } else {
            fmt.Println("Add DefinedColumn finished")
        }
    }
    ```


## Delete predefined columns

You can delete predefined columns that you no longer use from a base table.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |TableName|The name of the base table.|
    |DefinedColumns|The name of the predefined column.|

-   Examples

    The following code provides an example on how to delete the predefined columns definedColumnName01 and definedColumnName02 from the base table sampleTable:

    ```
    func DeleteDefinedColumn(){
        client := new(tablestore.TableStoreClient)
        deleteDefinedColumnRequest := new(tablestore.DeleteDefinedColumnRequest)
        deleteDefinedColumnRequest.DefinedColumns = []string{"definedColumnName01","definedColumnName02"}
        _, err := client.DeleteDefinedColumn(deleteDefinedColumnRequest)
        if (err != nil) {
            fmt.Println("Failed to delete DefinedColumn with error:", err)
        } else {
            fmt.Println("Delete DefinedColumn finished")
        }
    }
    ```


