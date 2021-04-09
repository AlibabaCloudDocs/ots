# Secondary index

This topic describes the limits of secondary index.

## Limits on index tables

|Item|Limit|Description|
|----|-----|-----------|
|Length of a table name|1~255 Bytes|The name of a table can contain uppercase and lowercase letters, digits, and hyphens \(-\). The name must start with a letter or an underscore \(\_\).|
|Number of secondary indexes for a single base table|5|A maximum of five index tables can be created for each base table.|
|Number of indexed columns|1 to 4|A maximum of four indexed columns can be added for an index table. Indexed columns consist of the primary key of the base table and predefined columns.The primary key of an index table consists of indexed columns and autocompleted primary key columns of the base table. |
|Data types supported by indexed columns|STRING, INTEGER, and BINARY|The data types supported by indexed columns are STRING, INTEGER, and BINARY.|
|Number of attribute columns|32|A maximum of 32 attribute columns can be added to an index table. Attribute columns of an index table consist of predefined columns of the base table.|
|Data types supported by attribute columns|STRING, INTEGER, DOUBLE, BOOLEAN, and BINARY|The data types supported by attribute columns are STRING, INTEGER, DOUBLE, BOOLEAN, and BINARY.|

## Additional limits

|Item|Limit|Description|
|----|-----|-----------|
|Indexed column|Columns except auto-increment primary key columns|The first primary key column of an index table cannot be an auto-increment column.|
|Operation on data in index tables|Read-only|You can only read data from index tables. You cannot write data to index tables.|
|Max versions|Not supported|You cannot create secondary indexes for tables that have max versions enabled.|
|Time to live \(TTL\)|Supported|Updates for base tables must be disabled. Make sure that the TTL value set for an index table is consistent with that for the base table.|
|Stream|Not supported|None.|
|Query from the base table|Not supported|Queries from the base table are required.|

