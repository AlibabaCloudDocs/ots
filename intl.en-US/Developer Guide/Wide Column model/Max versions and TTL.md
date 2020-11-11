# Max versions and TTL

This topic describes how to use the max versions and time to live \(TTL\) parameters of Tablestore to help manage data.

## Version number

When you update a value in an attribute column, a new version of that value is generated. The version value uses a timestamp as the version number. When you write data to an attribute column, you can specify a version number or allow Tablestore to automatically generate a version number.

-   The version number is calculated based on the number of milliseconds that have elapsed since 00:00:00 on January 1, 1970 \(UTC\).
-   If you manually specify a version number, make sure that the version number is a 64-bit timestamp accurate to the millisecond within the valid version range.

Version numbers are timestamps in milliseconds. When you compare version numbers with the TTL value and calculate the max version offset value in seconds, you must divide the version number by 1000.

Version numbers are used to implement the following features:

-   TTL

    The TTL value of data in a table is specified based on version numbers. When the written data is retained for a period of time that exceeds the TTL value, Tablestore automatically deletes the data of the corresponding version numbers.

    For example, assume that the version number of data in an attribute column is 1468944000000, which is calculated based on the time of 00:00:00 on July 20, 2016 \(UTC\). When you set TTL to 86400 \(one day\), the data of that version expires at 00:00:00 on July 21, 2016 \(UTC\). Tablestore automatically deletes the data after the data expires.

-   Maximum number of versions or range of version numbers to read from each attribute column

    When Tablestore reads a row of data, you can specify the maximum number of versions or the range of version numbers to read from each attribute column.


## Max versions

The max versions value indicates the maximum number of versions that can be retained for the data in an attribute column. When the number of versions of data in attribute columns exceeds the max versions value, Tablestore automatically deletes data of earlier versions.

When you create a table, you can customize the maximum number of versions that can be retained for the data in an attribute column. After the table is created, you can call the UpdateTable operation to modify the max versions value for the table.

**Note:** If the number of versions exceeds the specified max versions value, the data of earlier versions becomes invalid and you are unable to read the data even if it has not been deleted by Tablestore.

-   When you decrease the value of max versions, Tablestore asynchronously deletes data of earlier versions if the number of actual data versions exceeds the latest max versions value.
-   When you increase the value of max versions, you can read data of the corresponding versions as long as Tablestore has not deleted the data of these versions and these versions are within the latest version range.

## Max version offset

Data written to a table expires when the difference between the version timestamp and the current time exceeds the TTL value specified for the table. To address this problem, Tablestore allows you to configure the max version offset feature.

Max version offset specifies the maximum difference between the specified version number and current system time in seconds. The max version offset is a positive integer that can be greater than the number of seconds that have elapsed since 00:00:00 on January 1, 1970 \(UTC\).

When data is written to an attribute column, Tablestore checks the version number of the data. The valid version range of data in an attribute column is calculated based on the following formula: Valid version range = **\[Time when data is written - Max version offset, Time when data is written + max version offset\)**. The data can be written to the attribute column only when its version number \(converted into seconds by dividing by 1000\) falls within the max version offset range.

For example, assume that the max version offset of data in each attribute column for a table is set to 86400 seconds \(one day\). At 00:00:00 on July 21, 2016 \(UTC\), only data whose versions range between 1468944000000 \(00:00:00 on July 20, 2016 \(UTC\)\) and 1469116800000 \(00:00:00 UTC on July 22, 2016 \(UTC\)\) can be written. If you attempt to write data whose version number is 1468943999000 \(corresponding to 23:59:59 on July 19, 2016 \(UTC\)\), the data fails to be written.

If you do not specify max version offset when you create a table, Tablestore uses a default value of 86400. You can call the UpdateTable operation to modify the max version offset value.

## TTL

TTL is a table attribute measured in seconds. This attribute indicates the validity period of data. When the written data is retained for a period of time that exceeds the TTL value, Tablestore automatically deletes the data to free storage space and reduce costs.

Example: Assume that the TTL of data in each attribute column for a table is set to 86400 seconds \(one day\). At a given time of 00:00:00 on July 21, 2016 \(UTC\), data whose version number is less than 1468944000000 \(00:00:00 on July 20, 2016 \(UTC\)\) is expired. Tablestore automatically deletes the data of expired versions.

If you do not want data in a table to expire, you can set TTL to -1 . After the table is created, you can call the UpdateTable operation to modify the TTL value.

**Note:** If the written data is retained for a period of time that exceeds the TTL value, the data becomes invalid and you are unable to read the data even if it has not been deleted by Tablestore.

-   When you decrease the TTL value, Tablestore asynchronously deletes data that is retained for a period of time that exceeds the latest TTL value.
-   When you increase the TTL value, you can read data of the corresponding versions as long as Tablestore has not deleted the data and these versions are retained for a period of time that is within the latest TTL value.
