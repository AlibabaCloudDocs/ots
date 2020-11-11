# Configure an auto-increment primary key column

This topic describes how to configure an auto-increment primary key column. You cannot set a partition key to an auto-increment column. If you write data to a table that contains an auto-increment primary key column, you do not need to specify specific values for the auto-increment primary key column because Tablestore generates values for the auto-increment primary key column. Values generated for the auto-increment primary key column are unique and consecutive within the partition that shares the same partition key value.

## Prerequisites

The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/NodeJS SDK/Initialization.md).

## Usage notes

1.  When you create a table, you cannot set a partition key to an auto-increment primary key column.

    The data type of an auto-increment primary key column can only be set to integer. Each value generated for an auto-increment primary key column is a 64-bit signed long integer.

2.  When you write data to a table, you do not need to specify a specific value for the auto-increment column. You need only to set a placeholder value for the auto-increment column.

    If you want to obtain the value of the auto-increment column after data is written to the table, you can set ReturnType to Primarykey.

    When you query data, you must specify the values of all primary key columns. To obtain a complete primary key value, you can set ReturnType to Primarykey in PutRow, UpdateRow, or BatchWriteRow.


## Examples

When you use auto-increment primary key columns, you can call the CreateTable, PutRow, UpdateRow, or BatchWriteRow operation.

1.  Create a table

    To create an auto-increment primary key column when you create a table, you must set the attribute of the primary key column to AUTO\_INCREMENT.

    ```
    var TableStore = require('../index.js');
    var Long = TableStore.Long;
    var client = require('./client');
    
    var tableName = "autoIncTable";
    var pk1 = "stringPK";
    var pk2 = "autoIncPK";
    
    function createTableWithAutoIncrementPk() {
        var createParams = {
            tableMeta: {
                tableName: tableName,
                primaryKey: [
                    {
                        name: pk1,
                        type: 'STRING'
                    },
                    {
                        name: pk2,
                        type: 'INTEGER',
                        option: 'AUTO_INCREMENT'// Set the auto-increment column by setting option to AUTO_INCREMENT.
                    },
                ]
            },
            reservedThroughput: {
                capacityUnit: {
                    read: 0,
                    write: 0
                }
            },
            tableOptions: {
                timeToLive: -1,// Specify the validity period of data in seconds. A value of -1 indicates that the data never expires. If you set this parameter to 365×24*3600, the validity period of the data is one year.
                maxVersions: 1// Specify the maximum number of versions that can be saved in each column. A value of 1 indicates that only the latest version is saved in each column.
            },
        };
    
        client.createTable(createParams, function (err, data) {
            if (err) {
                console.log('error:', err);
                return;
            }
            console.log('create table success');
        });
    }
    ```

2.  Write data

    When you write data to a table, you do not need to specify a value of the auto-increment column. You need only to set a placerholder value for the column.

    ```
    var TableStore = require('../index.js');
    var Long = TableStore.Long;
    var client = require('./client');
    
    var tableName = "autoIncTable";
    var pk1 = "stringPK";
    var pk2 = "autoIncPK";
    
    function putRow() {
        var putParams = {
            tableName: tableName,
            condition: new TableStore.Condition(TableStore.RowExistenceExpectation.IGNORE, null),
            primaryKey: [
                { stringPK: 'pk1' },
                { autoIncPK: TableStore.PK_AUTO_INCR }
            ],
            attributeColumns: [
                { 'col1': 'col1val' }
            ],
            returnContent: { returnType: TableStore.ReturnType.Primarykey }
        };
    
        client.putRow(putParams, function (err, data) {
            if (err) {
                console.log('error:', err);
                return;
            }
    
            console.log('put row success,autoIncrement pk value:' + JSON.stringify(data.row.primaryKey));
        });
    
    }
    ```


