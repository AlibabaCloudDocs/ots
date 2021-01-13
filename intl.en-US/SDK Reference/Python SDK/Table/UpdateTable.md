# UpdateTable

You can call the UpdateTable operation to update the reserved read throughput, reserved write throughput, and the maximum number of versions of a specified table.

## Operations

```
    """
        Description: You can call this method to update the attributes of a table. This method can modify only the reserved read/write throughput of a table.
        ``table_name`` specifies the name of the table.
        ``table_options`` is an instance of the ``tablestore.metadata.TableOptions`` class. It contains the time_to_live, max_version, and max_time_deviation parameters.
        ``reserved_throughput`` is an instance of the ``ots2.metadata.ReservedThroughput`` class. It specifies the reserved read throughput and reserved write throughput.

        Return value: the time when the reserved read throughput or reserved write throughput was raised or lowered most recently for this table and the number of times it was lowered today.

        ``update_table_response`` indicates the update result. It is an instance of the ots2.metadata.UpdateTableResponse class.
        """
        def update_table(self, table_name, table_options, reserved_throughput):
			
```

## Examples

The following code provides an example on how to update the maximum number of versions of a table to five:

```
        # Set both the reserved read throughput and reserved write throughput to 0.
        reserved_throughput = ReservedThroughput(CapacityUnit(0, 0))

        # Create a TableOptions instance. Set time_to_live to 31536000 seconds (one year) and delete expired data, max_version to 5, and max_time_deviation to 86400 seconds (one day).
        table_options = TableOptions(31536000, 5, 86400)

        try:
            # Call the operation to update the reserved read/write throughput of the table.
            ots_client.update_table('SampleTable', reserved_throughput)

            # If the task succeeds, no exception is returned.
            print "update table succeeded"
        except Exception:
            # If the task fails, an exception is returned for you to handle.
            print "update table failed"
			
```

For the detailed sample code, visit [UpdateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/table_operations.py).

