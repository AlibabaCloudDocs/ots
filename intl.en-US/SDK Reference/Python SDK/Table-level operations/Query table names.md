# Query table names

You can call the ListTable operation to query the names of all tables that are created in the current instance.

**Note:** For more information about the ListTable operation, see [ListTable](/intl.en-US/API Reference/Operations/ListTable.md).

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A table is created.

## Operations

```
"""
Description: You can call this operation to query all table names.
Return value: the table name list.
table_list indicates the table name list of the tuple type. Example: ('MyTable1', 'MyTable2').
"""
def list_table(self):            
```

## Examples

The following code provides an example on how to query the names of all tables in an instance:

```
try:
    list_response = ots_client.list_table()
    print 'table list:'
    for table_name in list_response:
        print table_name
    print "list table succeeded"
except Exception:
    print "list table failed."          
```

For the detailed sample code, visit [ListTable@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/table_operations.py).

