# Does Tablestore allow me to migrate data across regions?

Yes, Tablestore allows you to migrate data across regions. You can use DataWorks or Tunnel Service to migrate data.

When you migrate data across regions, make sure that the machine where scheduling tasks are executed is connected to the networks where the source and destination instances are deployed. You can use the following methods to test the network connectivity:

-   Use DataWorks to migrate data: Create data sources for OTSReader and OTSWriter.
-   Use Tunnel Service to migrate data: Log on to the machine that is connected to a tunnel and use cURL to connect to the endpoints of the source and destination instances. If Unsupported Operation is returned from the preceding test results, the networks are connected.

For more information, see [t817126.md\#]().

