# Configure an auto-increment primary key column

This topic describes how to configure an auto-increment primary key column. An auto-increment primary key column cannot be the partition key. If you write data to a table that contains an auto-increment primary key column, you do not need to specify a value for the auto-increment primary key column because Tablestore generates a value for the auto-increment primary key column. The generated value for the auto-increment primary key column is unique, and all values in auto-increment primary key columns increase sequentially in a partition key.

**Note:** Tablestore SDK for Java V4.2.0 and later allow you to configure auto-increment primary key columns.

## Prerequisites

The OTSClient instance is initialized. For more information, see [Initialize SDK for Java](/intl.en-US/SDK Reference/Java SDK/Initialize SDK for Java.md).

## Usage notes

1.  When you create a table, you cannot set the partition key to an auto-increment primary key column.

    An auto-increment primary key column can only be an integer column. The generated value for an auto-increment primary key column is a 64-bit signed long integer.

2.  When you write data to a table, you do not need to specify a specific value for the auto-increment column. Instead, you need only to set the value of the auto-increment column to placeholders.

    If you want to obtain the value of the auto-increment column after data is written to the table, you can set ReturnType to RT\_PK.

    When you query data, you must specify the values of all primary key columns. To obtain a complete primary key value, you can set ReturnType to RT\_PK in PutRow, UpdateRow, or BatchWriteRow.


## Examples

When you use auto-increment primary key columns, you can call the CreateTable, PutRow, UpdateRow, or BatchWriteRow operation.

1.  Create a table

    To create an auto-increment primary key column when you create a table, you must set the attribute of the primary key column to AUTO\_INCREMENT.

    ```
    private static void createTable(SyncClient client) {
            TableMeta tableMeta = new TableMeta(“table_name”);
            // Create the first primary key column, and set it to the partition key.
            tableMeta.addPrimaryKeyColumn(new PrimaryKeySchema("PK_1", PrimaryKeyType.STRING));
            // Create the second primary key column, and set it to an auto-increment column. The type is set to INTEGER, and the attribute set to AUTO_INCREMENT.
            tableMeta.addPrimaryKeyColumn(new PrimaryKeySchema("PK_2", PrimaryKeyType.INTEGER, PrimaryKeyOption.AUTO_INCREMENT));
            int timeToLive = -1;  // Specify that data never expires.
            int maxVersions = 1;  // Save only one version.
            TableOptions tableOptions = new TableOptions(timeToLive, maxVersions);
            CreateTableRequest request = new CreateTableRequest(tableMeta, tableOptions);
            client.createTable(request);
        }
    ```

2.  Write data

    When you write data to a table, you do not need to specify a specific value for the auto-increment column. Instead, you need only to set the value of the auto-increment column to AUTO\_INCREMENT.

    ```
        private static void putRow(SyncClient client, String receive_id) {
            // Create the primary key.
            PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            // Set the value in the first primary key column to the first four digits of md5(receive_id).
            primaryKeyBuilder.addPrimaryKeyColumn("PK_1", PrimaryKeyValue.fromString("Hangzhou");
            // Set the value in the second primary key column to AUTO_INCREMENT. The second primary key column is an auto-increment primary key column. You need only to specify a placeholder for the column. Tablestore generates a value for the auto-increment primary key column.
            primaryKeyBuilder.addPrimaryKeyColumn("PK_2", PrimaryKeyValue.AUTO_INCREMENT);
            PrimaryKey primaryKey = primaryKeyBuilder.build();
            RowPutChange rowPutChange = new RowPutChange("table_name", primaryKey);
            // Set ReturnType to RT_PK to include the primary key value in the response. By default, no results are returned if ReturnType is not set.
            rowPutChange.setReturnType(ReturnType.RT_PK);
            // Add attribute columns.
            rowPutChange.addColumn(new Column("content", ColumnValue.fromString(content)));
            // Write data to the table.
            PutRowResponse response = client.putRow(new PutRowRequest(rowPutChange));
            // Display the returned primary key value.
            Row returnRow = response.getRow();
            if (returnRow ! = null) {
                System.out.println("PrimaryKey:" + returnRow.getPrimaryKey().toString());
            }
            // Display the consumed capacity units (CUs).
            CapacityUnit  cu = response.getConsumedCapacity().getCapacityUnit();
            System.out.println("Read CapacityUnit:" + cu.getReadCapacityUnit());
            System.out.println("Write CapacityUnit:" + cu.getWriteCapacityUnit());
        }
    ```


