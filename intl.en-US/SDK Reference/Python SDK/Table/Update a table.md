# Update a table

You can call the UpdateTable operation to update the reserved read throughput or reserved write throughput of a specified table.

## Method

```
    """
        Description: You can call this method to update attributes of a table. This method can only modify the reserved read throughput or reserved write throughput currently.
        ``table_name`` specifies the name of the table.
        ``table_options`` is an instance of the ``tablestore.metadata.TableOptions`` class. It contains the time_to_live, max_version, and max_time_deviation parameters.
        ``reserved_throughput`` is an instance of the ``ots2.metadata.ReservedThroughput`` class. It specifies the reserved read throughput and reserved write throughput.

        Return value: the time when the reserved read throughput or reserved write throughput was raised or lowered most recently for this table and the number of times it was lowered today.

        ``update_table_response`` indicates the update result. It is an instance of the ots2.metadata.UpdateTableResponse class.
        """
        def update_table(self, table_name, table_options, reserved_throughput):
            
```

## Example

The following code provides an example of how to update the max version to 5 of a table.

```
        # Set both the reserved read throughput and reserved write throughput to 0.
        reserved_throughput = ReservedThroughput(CapacityUnit(0, 0))

        # Create TableOptions. Set time_to_live to 31,536,000 seconds (data is cleared automatically when expired), max_version to 5, and max_time_deviation to one day 86,400 seconds (one day).
        table_options = TableOptions(31536000, 5, 86400)

        try:
            # Call the update_table method to update the reserved read throughput and reserved write throughput of the table.
            ots_client.update_table('SampleTable', reserved_throughput)

            # If no exception is thrown, the operation is successful.
            print "update table succeeded"
        except Exception:
            # If an exception is thrown, the operation fails. Handle the exception.
            print "update table failed"
            
```

You can obtain the full sample code at [UpdateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/table_operations.py).

