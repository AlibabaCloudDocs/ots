# Configure an auto-increment primary key column

This topic describes how to configure an auto-increment primary key column. You cannot set a partition key to an auto-increment column. If you write data to a table that contains an auto-increment primary key column, you do not need to specify specific values for the auto-increment primary key column because Tablestore generates values for the auto-increment primary key column. Values generated for the auto-increment primary key column are unique and consecutive within the partition that shares the same partition key value.

## Prerequisites

The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).

## Usage notes

1.  When you create a table, you cannot set a partition key to an auto-increment primary key column.

    The data type of an auto-increment primary key column can only be set to integer. Each value generated for an auto-increment primary key column is a 64-bit signed long integer.

2.  When you write data to a table, you do not need to specify specific values for the auto-increment column. Instead, you need only to set placeholder values for the auto-increment column.

    If you want to obtain the values of the auto-increment column after data is written to the table, you can set ReturnType to RT\_PK.

    When you query data, you must specify the values of all primary key columns. To obtain a complete primary key value, you can set ReturnType to RT\_PK in PutRow, UpdateRow, or BatchWriteRow.


## Examples

When you use auto-increment primary key columns, you can call the CreateTable, PutRow, UpdateRow, or BatchWriteRow operation.

1.  Create a table

    To create an auto-increment primary key column when you create a table, you must set the attribute of the primary key column to PK\_AUTO\_INCR.

    ```
    from tablestore import *
    
    table_name = 'OTSPkAutoIncrSimpleExample'
    
    def create_table(client):
        # Create a table that includes the following two INTEGER primary keys: gid and uid. Set uid to an auto-increment column.
        schema_of_primary_key = [('gid', 'INTEGER'), ('uid', 'INTEGER', PK_AUTO_INCR)]
        table_meta = TableMeta(table_name, schema_of_primary_key)
        table_options = TableOptions()
        reserved_throughput = ReservedThroughput(CapacityUnit(0, 0))
        client.create_table(table_meta, table_options, reserved_throughput)
        print ('Table has been created.')
    ```

2.  Write data

    When you write data to a table, you do not need to specify specific values for the auto-increment column. Instead, you need only to set placerholder values for the column.

    ```
    from tablestore import *
    
    table_name = 'OTSPkAutoIncrSimpleExample'
    
    def put_row(client):
        # Write data to the primary keys. Set the value of gid to 1 and set uid to the auto-increment column. You must specify values for the uid column. Otherwise, an error is returned.
        primary_key = [('gid',1), ('uid', PK_AUTO_INCR)]
        attribute_columns = [('name','John'), ('mobile',15100000000), ('address','China'), ('age',20)]
        row = Row(primary_key, attribute_columns)
    
        # Write data to attribute columns.
        row.attribute_columns = [('name','John'), ('mobile',15100000000), ('address','China'), ('age',25)]
        consumed, return_row = client.put_row(table_name, row)
        print ('Write succeed, consume %s write cu.' % consumed.write)
    
        consumed, return_row = client.put_row(table_name, row, return_type = ReturnType.RT_PK)
        print ('Write succeed, consume %s write cu.' % consumed.write)
        print ('Primary key:%s' % return_row.primary_key)
    ```


